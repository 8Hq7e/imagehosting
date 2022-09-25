### 主页

- 搜索DTG（DEVONthink to go）：```x-devonthink://search```
- 聚合搜索：```https://duckduckgo.com/?q=[prompt:聚合搜索]&t=iphone```，需配合 [Surge 聚合搜索重写模块](https://raw.githubusercontent.com/Neurogram-R/Surge/master/module/Q-Search.sgmodule)使用
- 检查（OmniFocus）：```omnifocus:///review```
- 微信天府健康通：```ksk://sbboy.com/#weixin://app/wx2604a227cbdfc489/jumpWxa/?userName=gh_f0f2d3970dbc```，需配合 App [跨时空](https://apps.apple.com/app/id1574032305) 使用

### 扫一扫

- QQ扫一扫：```mqqapi://qrcode/scan_qrcode?version=1&src_type=app```
- 淘宝扫一扫：```taobao://tb.cn/n/scancode```
- 微博扫一扫：```sinaweibo://qrcode```
- 京东扫一扫：```openapp.jdmobile://virtual?params=%7B%22category%22:%22jump%22,%22des%22:%22saoasao%22%7D```
- LCP二维码扫描：```launch://qr```
- 微信扫一扫：```weixin://scanqrcode```
- 美团扫一扫：```imeituan://www.meituan.com/scanQRCode?openAR=1```
- 知乎扫一扫：```zhihu://codereader```

### 购物搜索

- 搜索京东：```openapp.jdmobile://virtual?params={{{"des":"productList","keyWord":"[prompt:搜索京东]","from":"search","category":"jump"}}}```
- 搜索淘宝：```taobao://s.taobao.com/?q=[prompt:搜索淘宝]```
- 搜索 App Store：```itms-apps://search.itunes.apple.com/WebObjects/MZSearch.woa/wa/search?media=software&term=[prompt:搜索App Store]```
- 搜索什么值得买：```smzdm://search?json={{{"channelName":"home","search_type":"1","keyWord":"[prompt:搜索什么值得买]"}}}```
- 搜索拼多多：```pinduoduo://com.xunmeng.pinduoduo/search_result.html?search_key=[prompt:搜索拼多多]```

### 剪贴板

- URL 解码：```anubis://x-callback-url/tools/urlencode?input=%7B%22text%22%3A%20%22[clipboard]%22%2C%20%22decode%22%3A%20true%7D```，需配合 App  Anubis 使用
- URL 编码：```launch://clipboard?text=[list:Url编码|1️⃣ 剪贴板=[+clipboard]|2️⃣ 输入=[+prompt:Url编码]]```
- 编辑剪贴板：```launch://clipboard?text=[prompt-return:编辑剪贴板=[clipboard]]```
- 清空剪贴板：```launch://clipboard?text={{}}```
- 打开链接：```[[clipboard]]```
- 查询慢慢买：
	```
	manmanbuy://?type=func&value=MainUtils.openWin(%7bname%3a%27TrendDetailScene%27%2cnavi%3anavigation%2cpageParam%3a%7bsearchKey%3a%27[clipboard]%27%7d%7d)%3b
	```
- 用京东打开：
	```
	openapp.jdmobile://virtual?params=%7B%22category%22%3A%22jump%22%2C%22des%22%3A%22m%22%2C%22url%22%3A%22[clipboard]%22%7D
	```
- 用 Evernote 剪藏：```evernote://x-callback-url/new-clipped-note?url=[clipboard]```
- 写入 Dropbox 剪贴板：```launch://dropbox/new?text=[clipboard]&path={{Apps/Launch Center Pro/}}&overwrite=YES&name={{Dropclip.txt}}```
- 同步 Dropbox 剪贴板：```launch://dropbox/clipboard?path={{Apps/Launch Center Pro/Dropclip.txt}}```
- 剪贴板写入预设内容：```launch://clipboard?text=[list:预设内容|1️⃣ 预设内容=预设内容|2️⃣ 预设内容=预设内容|3️⃣ 预设内容=预设内容|4️⃣ 预设内容=预设内容|5️⃣ 预设内容=预设内容|6️⃣ 预设内容=预设内容]```

### 支付宝

- 支付宝扫一扫：```alipayqr://platformapi/startapp?saId=10000007```
- 支付宝出行：```alipays://platformapi/startapp?appId=20002047```
- 支付宝菜鸟驿站：```alipays://platformapi/startapp?appId=2021001141626787&page=pages%2Fall-package%2Fall-package```
- 支付宝行程卡：```alipays://platformapi/startapp?appId=2021002170600786```
- 支付宝丰巢智能柜：```alipays://platformapi/startapp?appId=2019042864359220&page=page%2FsearchExpressList%2FsearchExpressList```
- 支付宝付款码：```alipay://platformapi/startapp?appId=20000056```
- 支付宝天府健康通：```alipays://platformapi/startapp?appId=2021002116662889```
- 支付宝渝康码：```alipays://platformapi/startapp?appId=2021001130674650```
- 支付宝基金：```alipays://platformapi/startapp?appId=20000793```
- 支付宝余额宝：```alipays://platformapi/startapp?appId=20000793```
- 支付宝医保码：```alipays://platformapi/startapp?appId=20002069```

### 其他

- 新建笔记（Evernote）：```evernote://x-callback-url/new-note?type=text&title=[prompt:笔记标题]```
- 照片笔记（Evernote）：```launch://x-callback-url/clipboard?attach=photo:last&x-success={{evernote://x-callback-url/new-note?type=clipboard&title=[prompt:笔记标题]}}```
- 开始录音（Just Press Record）：```justpressrecord://record```，停止录音：```justpressrecord://stop```
- 打开 telegram 群组/用户/机器人 等（以 Price Tag 为例）：```tg://resolve?domain=pricetagapp```
- 给 telegram 群组/用户/机器人 等发消息（以 Price Tag 为例）：```tg://msg?text=[prompt:发消息]&to=@pricetagapp```
- 殴路词典：```eudic://dict/[prompt:欧路词典]```
- 微信丰巢智能柜：```ksk://sbboy.com/#weixin://app/wx2604a227cbdfc489/jumpWxa/?userName=gh_cd6a2f7aa6be&path=pages/order/index.html?orderType=receive```，需配合 App [跨时空](https://apps.apple.com/app/id1574032305) 使用
- 记录喝水（Waterminder）：```waterminder://x-callback-url/add-water?amount=300```
- 识别文字（白描）：```baimiao://x-callback-url/scanText?in=recent&scan=single&count=1&out=copy```
- 拼图（Picsew）：```picsew://x-callback-url/[list:截图方式|长截图=scroll|横向拼接=hori|竖向拼接=vert]?in=latest&count=[prompt-num:拼接张数]&out=save&clean_status=yes```
- 编辑截图（Pico）：```pico://last-photo```
- 网页翻译：
  ```
  [list:选择翻译器|1️⃣ 🈯️ DeepL 译至中=https://www.deepl.com/translator#auto/zh/|2️⃣ 🈯️ Google 译至中=https://translate.google.com/#view=home&op=translate&sl=auto&tl=zh-CN&text=|3️⃣ 🆎 DeepL 译至英=https://www.deepl.com/translator#auto/en/|4️⃣ 🆎 Google 译至英=https://translate.google.com/#view=home&op=translate&sl=auto&tl=en&text=][clipboard]
  ```
- 批量新建任务（OmniFocus）：
  ```
  omnifocus:///paste?target=inbox&content=[prompt-list(
  [list:列表类型|1️⃣ 同级=|2️⃣ 子项=	]- ):列表录入=- \|]
  ```

### 其他 App 直接调用 Launch Center Pro 动作

Launch Center Pro 支持从其他 App（例如 OmniFocus、Evernote等） 直接调用特定动作：```launch://?url=%5BAction:129%5D```,URL 解码后为```launch://?url=[action:129]```，动作ID可从动作编辑页面底部找到：
![](https://image.ioeer.com/resource/20220925/action.jpeg)

### URL Scheme 查询网站

1. MoneyWiz：https://help.wiz.money/en/articles/4525440-automate-transaction-management-with-url-schemas
2. OmniFocus：https://inside.omnifocus.com/url-schemes
3. Picsew：https://sugarmo.github.io/picsew-help/zh-Hans/x-callback-url.html
4. Fantastical：https://flexibits.com/fantastical-ios/help/integration
