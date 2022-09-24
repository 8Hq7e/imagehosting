!> 本文的使用环境为 macOS



Evernote （国际版）与印象笔记（中国版）本是同源，使用相同客户端，只是账号相互独立。中国版印象笔记相较于国际版 Evernote，添加了转存微博微信等本地化服务。2018 年 6 月，中国版印象笔记独立重组，开发思路逐渐与国际版有所不同，客户端相互分离。重点是，中国版印象笔记广告多到了无孔不入的程度，移动客户端屏幕内容甚至有一大半被用于展示推广，即使订购会员也无法得到改善，对日常使用产生了较大的影响，故放弃印象笔记，转投 Evernote。



### 优化网络连接

Evernote macOS 版从 10.0 版本开始，采用 Electron 框架开发，不再有离线笔记的提法，笔记的加载和修改与服务器实时保持同步，对网络的要求有所提升，是否能够流畅的访问 Evernote 服务器对使用体验有较大影响。因此，需要针对网络连接进行特别优化，将 Evernote App 和 evernote.com 的网络连接通过代理服务器进行访问。

此处以 Surge 为例，相应配置模块为：

```nginx
#!name=Evernote Rule Module
#!desc=Evernote 配置模块

[Rule]

PROCESS-NAME,Evernote*,Proxy
DOMAIN-SUFFIX,evernote.com,Proxy

```



### 优化 Chrome 剪藏插件登陆体验

退出 Chrome\Edge 重新打开后，使用 Evernote 剪藏插件会有一个自动登陆行为，如果检查到使用地区位于中国，会优先调用印象笔记的登陆地址，导致登陆失败，需要手动切换到 Evernote 登陆界面。此处的解决方案是对印象笔记的登陆地址进行重写，迫使剪藏插件直接访问 Evernote 完成登陆操作。

在 Chrome 剪藏插件中，印象笔记的登陆地址为：

```
https://app.yinxiang.com/ClipperLogin.action?wck=clipper_chr
```
重写后 Evernote 的登陆地址为：
```
https://www.evernote.com/ClipperLogin.action?wck=clipper_chr
```

依旧利用 Surge 完成登陆地址重写功能，值得一提的是，开启 Surge 重写功能的前提条件是已完成相关自签证书的安装和信任，并打开 Surge 的 MitM 和重写功能。Surge 配置模块添加 Evernote 扩展登陆地址重写示例如下：

```nginx
#!name=Evernote Rule Module
#!desc=Evernote 模块配置

[Rule]

PROCESS-NAME,Evernote,Proxy
DOMAIN-SUFFIX,evernote.com,Proxy

[URL Rewrite]

^https?://app\.yinxiang\.com/ClipperLogin\.action\?wck=clipper_chr https://www.evernote.com/ClipperLogin.action?wck=clipper_chr 302

[MITM]

hostname = %APPEND% app.yinxiang.com

```



### 优化 Spark 邮件 Evernote 服务使用体验

默认情况下，Spark 为中国用户做了本地化处理，检测到语言为中文时，直接调用印象笔记提供的服务接口，且无法更改为 Evernote。此处的解决方案为将 Spark 的程序语言设置为英文。

具体设置路径为打开 **系统偏好设置**，选择 **语言与地区**，点击通用旁边的 **App** 按钮，添加 Spark 并设置为英文：

![](https://image.ioeer.com/resource/20220919/spark.png)

完成上述设置后，退出 Spark 重新打开，即可添加 Evernote 国际版账户。

![](https://image.ioeer.com/resource/20220919/spark2.png)
