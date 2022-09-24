本文以 **仓耳今楷05** 这款字体为例，介绍了如何为 Kindle 全平台更换自定义字体。其中，Kindle iOS（OLED）使用 03 字重，Kindle Paperwhite 2（电纸书）使用 04 字重，Kindle Android（海信 A7 电纸书）使用 05 字重。

![](https://image.ioeer.com/resource/20220804/001.png)

## 一、为 Kindle App（iOS）更换自定义字体

由于 iOS 系统限制，无法像 Kindle 设备那样使用自定义字体的方法替换 Kindle APP 下载字体，因此需要配合 iOS 上的网络调试工具重写下载字体的网络地址，此处以 Surge 为例展示操作方法，配置和信任证书的教程自行网络搜索，其他工具同理。抓包得到 Kindle 下载字体的连接为：

- **楷体**：https://s3.cn-north-1.amazonaws.com.cn/maes-appexpan-protected-prod/STFontSC/STKaitiSC.ttf?XXXXXXXXXXXXXX
- **黑体**：https://s3.cn-north-1.amazonaws.com.cn/maes-appexpan-protected-prod/STFontSC/STHeitiSC.ttf?XXXXXXXXXXXXXX
- **宋体**：https://s3.cn-north-1.amazonaws.com.cn/maes-appexpan-protected-prod/STFontSC/STSongSC.ttf?XXXXXXXXXXXXXX
- **圆体**：https://s3.cn-north-1.amazonaws.com.cn/maes-appexpan-protected-prod/STFontSC/STYuanMedium-2018-02-16.ttf?XXXXXXXXXXXXXX

以宋体为例，浏览器打开抓包链接下载宋体字体文件，使用在线字体编辑器：

**FontEditor**：[https://kekee000.github.io/fonteditor/index.html][https://kekee000.github.io/fonteditor/index.html]

打开**仓耳今楷05-W03.ttf**，并在 **设置** &rArr; **字体信息** 中将所有信息替换为"**STSongSC.ttf**"中的信息，然后导出 TTF 文件，重命名"**仓耳今楷05-W03.ttf**"（下载的TTF）为"**STSongSC.ttf**"，上传至自己的 Github 或者其他可以直接访问的地址。

最后得到 302 重写规则，创建如下所示的 Surge 模块（注意，一定要把代码里的字体地址 https://example.com/STSongSC.ttf 替换成你上传字体后得到的真实可用的地址）：
```nginx
#!name=Kindle Fonts Customize
#!desc=自定义Kindel字体

[URL Rewrite]

^https://s3.cn-north-1.amazonaws.com.cn/maes-appexpan-protected-prod/STFontSC/STSongSC.ttf.+ https://example.com/STSongSC.ttf 302

# ^https://s3.cn-north-1.amazonaws.com.cn/maes-appexpan-protected-prod/STFontSC/STKaitiSC.ttf.+
# ^https://s3.cn-north-1.amazonaws.com.cn/maes-appexpan-protected-prod/STFontSC/STHeitiSC.ttf.+
# ^https://s3.cn-north-1.amazonaws.com.cn/maes-appexpan-protected-prod/STFontSC/STYuanMedium-2018-02-16.ttf.+

[MITM]

hostname = %APPEND% s3.cn-north-1.amazonaws.com.cn
```

启用重写模块，Kindle iOS 在管理字体里删除已经下载好的宋体字体，重新下载，彻底退出 Kindle 重新打开即可生效。

iPhone XS 使用 03 字重效果如图：

![](https://image.ioeer.com/resource/20220804/002.jpg)

iPad Pro 12.9 使用03字重效果如图：

![](https://image.ioeer.com/resource/20220804/003.png)

![](https://image.ioeer.com/resource/20220804/004.png)

## 二、为 Kindle 设备更换自定义字体

直接将"**仓耳今楷05-W04.ttf**"字体文件拷贝至 Kindle 根目录下的"**fonts**"文件夹中，启用即可，具体操作详见书伴教程：如何使用 Kindle 的原生自定义字体功能：<https://bookfere.com/post/655.html>

Kindle Paperwhite 2 使用 04 字重效果如图：

![](https://image.ioeer.com/resource/20220804/005.png)

## 三、为 Kindle App（Android）更换自定义字体

可下载字体管理里面字体下载完成以后，可在手机中如下所示的目录下：

```
/Android/data/com.amazon.kindlefc/files/appexpan
```

看到类似如下的目录：

- **STHeitiSC** ← 对应黑体简体
- **STHeitiTC** ← 对应黑体繁体
- **STKaitiSC** ← 对应楷体简体
- **STKaitiTC** ← 对应楷体繁体
- **STSongSC** ← 对应宋体简体
- **STSongTC** ← 对应宋体繁体
- **STYuanSC** ← 对应圆体简体
- **STYuanTC** ← 对应圆体繁体

我这里替换圆体，也就是将 **仓耳今楷05-W05.ttf** 重命名为 **STZhongyuan.ttf** ，并替换如下所示的文件：

```
/Android/data/com.amazon.kindlefc/files/appexpan/STYuanSC/1/STZhongyuan.ttf
```

彻底退出 Kindle 重新打开即可生效。

海信 A7（Android）使用 05 字重效果如图：

![](https://image.ioeer.com/resource/20220804/006.jpg)