## Typecho 介绍
**Typecho**基于PHP，使用数据库储存数据，是一款内核强健、扩展方便、体验友好、运行流畅的轻量级开源博客程序。
### 轻量高效
仅仅 7 张数据表，加上不足 400KB 的代码，就实现了完整的插件与模板机制。超低的 CPU 和内存使用率，足以发挥主机的最高性能。
### 先进稳定
原生支持 **Markdown** 排版语法，易读更易写。支持 BAE/GAE/SAE 等各类云主机，即使面对突如其来的高访问量，也能轻松应对。
### 简洁友好
精心打磨过的操作界面，依然是你熟悉的面孔，更多了一份成熟与贴心。每一个像素的剪裁，都只为离完美更进一步。
## 使用 Docker 构建 Typecho
!> **'#'为注释行，'$'为命令行**

### 构建专用隔离网络
创建专用网络的目的是为了隔离数据库，只让 typecho 博客服务有权限访问，保护数据库安全：
```bash
$ sudo docker network create typecho
```
### 构建数据库服务
创建 Mariadb Docker 容器：
```bash
# 创建mariadb数据文件夹
$ mkdir -p ~/docker/mariadb

# 创建容器，root 范例密码设置为 rootpassword，注意修改
$ sudo docker run -d \
    --name=mariadb \
    --network=typecho \
    --restart=always \
    --env MARIADB_ROOT_PASSWORD='rootpassword' \
    -v ~/docker/mariadb:/var/lib/mysql \
    mariadb:latest
```
对数据库进行设置：
```bash
# 进入容器，为 typecho 创建低权限账户及数据库
$ sudo docker exec -it mariadb bash

# 以 root 身份登陆到数据库，登陆数据库后注意命令以;结尾，root 密码为 rootpassword
$ mysql -u root -p

# 在数据库中创建可互联网访问的低权限账户：typecho，范例密码是：userpassword
$ CREATE USER typecho@'%' IDENTIFIED BY 'userpassword';

# 创建名为 typechodb 的数据库
$ CREATE DATABASE typechodb;

# 给 typecho 赋予从互联网访问数据库 typechodb 的所有权限
$ GRANT ALL PRIVILEGES ON typechodb.* TO 'typecho'@'%';

# 退出 root 账户，切换为 typecho 账户
$ exit

# 以 typecho 身份登陆数据库进行核查
$ mysql -u typecho -p

# 检查是否有数据库权限，能看到 typechodb 即可
$ SHOW databases;

# 退出数据库管理
$ exit

# 退出容器管理
$ exit
```
### 构建博客服务
创建 Typecho Docker 容器：
```bash
# 创建mariadb数据文件夹
$ mkdir -p ~/docker/typecho

# 注意 typecho 文件夹在 ~/docker/typecho
$ sudo docker run -d \
    --name=typecho \
    --restart=always \
    --network=typecho \
    -p 127.0.0.1:8080:80/tcp \
    -v ~/docker/typecho:/app \
    -e TIMEZONE='Asia/Shanghai' \
    -e MEMORY_LIMIT=256M \
    -e MAX_POST_BODY=50M \
    joyqi/typecho:nightly-php7.4-apache
```
## 开启HTTPS安全访问
### 安装 Nginx 服务
```bash
# 切换 root 身份
$ sudo -i

# 更新 apt-get
$ apt-get update

# 安装 nginx
$ apt-get install nginx

# 设置开机自启 nginx 服务
$ systemctl enable nginx
```
### Nginx配置示例
编辑 Nginx 配置，启用 HTTPS 访问：
```bash
# 配置 nginx 反向代理，注意修改证书路径 /path/to/cert/*
$ vim /etc/nginx/nginx.conf
```
配置示例：
```nginx
worker_processes  3;
error_log  /etc/nginx/logs/error.log;

events {
    worker_connections  2048;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile      on;
    keepalive_timeout  65;

    access_log /etc/nginx/logs/access.log;

    # HTTPS server

    server {

        listen 443 ssl;
        listen [::]:443 ssl;
        server_name              www.ioeer.com;
        
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

            proxy_pass http://127.0.0.1:8080;

            proxy_read_timeout 300;
            proxy_connect_timeout 300;
            proxy_redirect off;

            proxy_ssl_server_name on;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

        }
    }
}
```
### 检查并启用 Nginx
检查 Nginx 配置文件并重启 Nginx 服务：
```bash
# 检查配置文件
$ nginx -t
# nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /etc/nginx/nginx.conf test is successful

# 重启 Nginx 服务
$ systemctl restart nginx
```
## 配置 Typecho
浏览器打开 https://www.ioeer.com 会自动跳转到安装向导界面：

首先需要将 typecho 连接到数据库，注意正确输入数据库地址：
![](https://image.ioeer.com/resource/20220802/guide1.png)

然后配置 typecho 管理员密码：
![](https://image.ioeer.com/resource/20220802/guide2.png)

提示安装成功：
![](https://image.ioeer.com/resource/20220802/guide3.png)

## 自定义 Handsome
Handsome 是一款typecho主题，界面美观，功能强大，体积轻巧，作者已经列出了详细的教程，这里只是记录自定义部分。
√> 作者博客：https://www.ihewro.com/
主题购买地址：https://www.ihewro.com/archives/489/
官方使用手册：https://handsome.ihewro.com/

### 文章列表悬浮上浮效果

设置路径：控制台 &rArr; 外观 &rArr; 设置外观 &rArr; 开发者设置 &rArr; 自定义CSS
```java
.blog-post .panel-small:not(article),
.blog-post .panel:not(article), .panel-picture {
    transition: all 0.3s;
}

.blog-post .panel-small:not(article):hover,
.blog-post .panel:not(article):hover, .panel-picture:hover {
    transform: translateY(-10px);
    box-shadow: 0 8px 10px rgba(73, 90, 47, 0.47);
}
```

### 开关部分功能

**开启存档功能**

路径：设置 &rArr; 永久链接：
- 是否使用地址重写功能 &rArr; 启用；
- 自定义文章路径 &rArr; wordpress风格

路径：管理 &rArr; 独立页面 &rArr; 新增
- 标题：归档
- 地址：www.ioeer.com/archives.html
- 设置页面：选项 &rArr; 自定义模板 &rArr; 文章归档

![](https://image.ioeer.com/resource/20220802/archives.png)

路径：控制台 &rArr; 外观 &rArr; 设置外观 &rArr; 高级设置 &rArr; 左侧边栏导航
```json
{"name":"归档","class":"archive","link":"https://www.ioeer.com/archives.html","target":"_self"}
```
**关闭闲言碎语**

路径：控制台 &rArr; 外观 &rArr; 设置外观 &rArr; 高级设置 &rArr; 顶部导航按钮配置

```json
{"name":"talk","status":"hide"}
```
**其他细微功能开关**

勾选：
- 外观设置 &rArr; 外观设置开关 &rArr; 右侧边栏不显示图标
- 主题增强功能 &rArr; 增强功能开关 &rArr; 隐藏主题中所有登录注册地址
- 主题增强功能 &rArr; 增强功能开关 &rArr; 关闭文章页面的分享按钮
- 主题增强功能 &rArr; markdown扩展设置 &rArr; 添加文字着重强调书写方式

取消勾选：
- 主题增强功能 &rArr; 增强功能开关 &rArr; 启用全局顶部音乐播放器
- 主题增强功能 &rArr; 增强功能开关 &rArr; 启用文章打赏功能

路径：开发者设置 &rArr; 开发者高级设置
```json
{
  "off_star_post":true,
  "off_star_page":true,
  "off_star_comment":true,
  "show_static":false
}
```
具体关闭的功能为：
- 关闭文章点赞开关
- 关闭独立页面点赞开关
- 关闭统计图显示

## 修复 Handsome 代码高亮失效

路径：控制台 &rArr; 外观 &rArr; 设置外观 &rArr; 开发者设置 &rArr; 自定义输出head 头部的HTML代码
```html
<link rel="stylesheet"
      href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/styles/default.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/10.6.0/highlight.min.js"></script>
```
