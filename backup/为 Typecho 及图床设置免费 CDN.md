cloudflare 的网络在全球拥有许多连线到互联网交换点的连线。cloudflare 会将内容缓存到其边缘位置，以扮演内容提供网络（cdn）的角色，所有要求接着会透过 cloudflare 进行反向 proxy 处理，并直接从 cloudflare 提供缓存的内容。

本站使用 cloudflare，主要用到免费 cdn 来加速网站访问，隐藏服务器真实地址来防御 ddos 攻击两个主要功能。

## 为 Typecho Docker 容器安装主题

### 安装主题与插件

从 handsome 用户中心下载 zip 主题安装包并上传至服务器。解压主题文件包后，会得到三个文件夹：主题、插件、浏览器插件。
- 将「主题」文件夹下面的「handsome」文件夹上传至 typecho 的 usr/themes 目录下（主题文件夹必须命名为 handsome）
- 将「插件」文件夹下面的「Handsome」文件夹上传至 typecho 的 usr/plugins 目录下（插件文件夹必须命名为 Handsome，首字母大写，安装后必须启用，否则无法使用主题功能。启用该插件后，后台 &rArr; 管理栏目下会多一个友情链接的栏目。）

```bash
# 进入主题安装包文件夹
$ cd /path/to/handsome

# 解压主题安装包
$ unzip handsome-vx.x.x.zip

# 分别将主题所需资源拷贝到 typecho 容器映射文件夹对应目录下
$ cp -r /path/to/handsome/主题/handsome ~/docker/typecho/usr/themes
$ cp -r /path/to/handsome/插件/Handsome ~/docker/typecho/usr/plugins
```

### 启用插件和主题
主题的个性化设置：后台设置 &rArr; 控制台 &rArr; 外观 &rArr; 设置外观
插件设置：后台设置 &rArr; 控制台 &rArr; 插件 &rArr; 插件管理（安装后必须要在typecho插件列表中启用该插件）
其中浏览器插件是 chrome 内核浏览器插件，具体介绍可以看下这里使用浏览器插件发送时光机，可以不用。

## 为 Handsome 创建静态资源

### 创建主题静态资源

handsome 主题静态资源分为两种，一种是外部框架库，另一种是主题自身的 js/css 资源，都在主题目录下的 /assets/ 目录下。
把 asset 目录上传到 cdn 服务器上，比如 cdn 服务器的 resource 目录里，地址即为 https://cdn.ioeer.com/resource/assets/

```bash
# 创建静态资源文件夹
mkdir -p /path/to/web

# 将主题静态资源拷贝到文件夹
cp -r /path/to/handsome/主题/handsome/assets /path/to/web/resource
```

### 公共资源本地化

主题的静态资源除了主题本身的 js、css，还使用到了一些公共库 mathjax、jquery、vditor、mdui、bootstrap。这些库体积较大，使用 cdn 加速会使得网站速度更快。

从 handsome 用户中心下载公共资源并上传至服务器。

!> **注意需要容器和自建 cdn 同时安装公共资源**

```bash
# 进入公共资源安装包文件夹
$ cd /path/to/vditor

# 解压公共资源
$ tar -xzvf vditor-x.x.x.tgz

# 将压缩包中的 dist 文件夹移动到主题和 cdn 目录下面的 assets/libs/vditor 目录下
$ cp -r /path/to/vditor/package/dist ~/docker/typecho/usr/themes/handsome/assets/libs/vditor
$ cp -r /path/to/vditor/package/dist /path/to/web/resource/assets/libs/vditor
```
√> 配置公共资源的另一个好处的，可以修复 handsome 高亮设置失效问题，无需单独设置**自定义输出head 头部的HTML代码**，详情可见主题外观设置页面公告。

### 启用 cdn 加速

设置路径：后台设置 &rArr; 控制台 &rArr; 外观 &rArr; 设置外观 &rArr; 速度优化

![](https://image.ioeer.com/resource/20220802/speed.png)

## 配置 Nginx CDN 服务

!> **注意为 nginx 配置跨域资源共享，防止启用 cdn 以后网站加载出错**

如果没有配置跨域资源，Handsome 主题加载字体会提示 **Access-Control-Allow-Origin** 错误：
![](https://image.ioeer.com/resource/20220802/error.png)
完整错误提示为：
```bash
Access to font at 'https://cdn.ioeer.com/resource/assets/fonts/fontello/fontello.woff?7176355' from origin 'https://www.ioeer.com' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```
需在nginx配置相应服务内添加 cors:
```nginx
add_header Access-Control-Allow-Origin https://www.ioeer.com;
```
**nginx 配置示例**

```nginx
    server {

        listen 443 ssl;
        listen [::]:443 ssl;
        server_name              cdn.ioeer.com;

        add_header Access-Control-Allow-Origin https://www.ioeer.com;
        
        ssl_certificate         /path/to/cert/fullchain.cer;
        ssl_certificate_key     /path/to/cert/ioeer.com.key;
        ssl_session_timeout 1d;
        ssl_session_cache shared:MozSSL:10m;
        ssl_session_tickets off;
        
        ssl_protocols            TLSv1.2 TLSv1.3;
        ssl_ciphers              ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
        ssl_prefer_server_ciphers off;

        client_max_body_size 50m; 
        client_body_buffer_size 256k;
        client_header_timeout 3m;
        client_body_timeout 3m;
        send_timeout 3m;
        proxy_connect_timeout 300s; 
        proxy_read_timeout 300s; 
        proxy_send_timeout 300s;
        proxy_buffer_size 64k; 
        proxy_buffers 4 32k; 
        proxy_busy_buffers_size 64k;
        proxy_temp_file_write_size 64k; 
        proxy_ignore_client_abort on; 
                
        location / {

            root /path/to/web/;

        }
    }
```

## Cloudflare设置

### 配置 DNS
dns 启用 cloudflare 代理，用以开启 cdn 及隐藏地址功能：
![](https://image.ioeer.com/resource/20220802/dns.png)

### 配置缓存规则

缓存规则有优先级区分，第一条规则为最优先，免费账户一共可以设置3条规则。因此，需要精打细算，完成网站，cdn，以及图床的缓存加速设置。
![](https://image.ioeer.com/resource/20220802/rule0.png)

#### 第一条规则

第一条规则的作用是浏览器输入 ioeer.com，快捷跳转到 https://www.ioeer.com
![](https://image.ioeer.com/resource/20220802/rule1.png)

#### 第二条规则

第二条规则的作用是为静态资源和图床设置 cdn 加速：

- 静态资源加速目录为：https://cdn.ioeer.com/resource/assets/
- 小型文件分享目录为：https://cdn.ioeer.com/resource/files/
- 自建图床加速目录为：https://image.ioeer.com/resource/poster

![](https://image.ioeer.com/resource/20220802/rule2.png)

#### 第三条规则

第三条规则的作用是为下列网址设置通用缓存规则：

- www.ioeer.com 
- image.ioeer.com
- cdn.ioeer.com 

![](https://image.ioeer.com/resource/20220802/rule3.png)

缓存设置中文释义：
| 设置项            | 中文释义       |
| :---------------- | :------------- |
| Browser Cache TTL | 浏览器缓存 TTL |
| Cache Level       | 缓存级别       |
| Edge Cache TTL    | 边缘缓存 TTL   |
