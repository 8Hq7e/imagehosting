√> 使用到的镜像：[Qiandao —— 一个HTTP请求定时任务自动执行框架 base on HAR Editor and Tornado Server](https://registry.hub.docker.com/r/a76yyyy/qiandao)
```bash
$ docker pull a76yyyy/qiandao

$ docker run -d --name qiandao -p 8923:80 -v $(pwd)/qiandao/config:/usr/src/app/config a76yyyy/qiandao
```
## 第一步，制作签到模板

!> 本次以设置 PT 站自动签到为例，站点选择为聆音。

1.Edge开启隐身模式，登陆一次PT站点。按F12打开开发者工具，切换到网络选项卡，勾选保留日志，再点按🚫清除之前的日志：

![](https://image.ioeer.com/resource/20220803/001.png)

2.刷新网页，等待加载完成后，点击签到，等待加载完成。可以看到记录栏有很多记录。点击下箭头导出har，并将签到成功的提示语复制到别处，方便后续调试日志：

> 这是您的第 XX 次签到，已连续签到 11 天，本次签到获得 360 个魔力值。

![](https://image.ioeer.com/resource/20220803/002.png)

3.注册并登陆docker里的签到控制端，点击我的模板旁的+号创建新模板：

![](https://image.ioeer.com/resource/20220803/003.png)

4.上传HAR，用户名和密码不用填：

<img src="https://image.ioeer.com/resource/20220803/004.png" alt="" style="zoom:50%;" />

5.选择Document，勾选首页和签到页：

![](https://image.ioeer.com/resource/20220803/005.png)

6.点击选择的网址<https://pt.soulvoice.club/login.php>，进入编辑模式，勾选cookie：

![](https://image.ioeer.com/resource/20220803/006.png)

7.在测试页面点击测试，预览界面得到自己的用户名信息则测试通过，排版和格式错误不用管：

![](https://image.ioeer.com/resource/20220803/007.png)

8.返回请求页面，剪切cookie的值，并编辑cookie为变量```{{my_cookie}}```，方便以后更新cookie：

![](https://image.ioeer.com/resource/20220803/008.png)

9.切换至测试页面，点击Variables旁边的ADD，name填写为```my_cookie```,值粘贴为第八步剪切的cookie值，点击Cookies旁边的CLEAR按钮，并点击测试：

![](https://image.ioeer.com/resource/20220803/009.png)

10.预览界面得到包含用户名的正确信息测试通过，点击页面下方的SAVE按钮保存。另一个页面

<https://pt.soulvoice.club/attendance.php>页面重复第6-9步，但最后不保存：

![](https://image.ioeer.com/resource/20220803/010.png)

11.页面提示您今天已经签到过了，请勿重复刷新，表示请求了签到动作，现在提取日志，点击变量提取旁边的ADD按钮，填写变量提取规则并保存：

**name** 填写为：```__log__```
**rule** 填写为：```(这是您的第\s<b>\d+</b>\s次签到，已连续签到\s<b>\d+</b>\s天，本次签到获得\s<b>\d+</b>\s个魔力值。|您今天已经签到过了，请勿重复刷新。)```

![](https://image.ioeer.com/resource/20220803/011.png)

12.最后保存模板:

![](https://image.ioeer.com/resource/20220803/012.png)

13.编辑名称，重复时间等信息，保存：

<img src="https://image.ioeer.com/resource/20220803/013.png" alt="" style="zoom:50%;" />

## 第二步，制作定时签到

1.点击我的签到旁边的+号按钮：

![](https://image.ioeer.com/resource/20220803/014.png)

2.填写my_cookie变量为自己剪贴的cookie，点击提交：

<img src="https://image.ioeer.com/resource/20220803/015.png" alt="" style="zoom: 50%;" />

3.点击操作栏执行按钮，签到成功，日志显示正确：

<img src="https://image.ioeer.com/resource/20220803/016.png" alt="" style="zoom:50%;" />

<img src="https://image.ioeer.com/resource/20220803/017.png" alt="" style="zoom:50%;" />

4.点击定时，设置随机延时定时签到，并提交完成整个自动签到设置：

<img src="https://image.ioeer.com/resource/20220803/018.png" alt="" style="zoom: 50%;" />

5.点击日志按钮可查看最近签到日志：

![](https://image.ioeer.com/resource/20220803/019.png)

6.签到报错,先尝试更新cookie，与抓取HAR类似，按F12打开开发者工具，定位到网络，请求标头：

![](https://image.ioeer.com/resource/20220803/020.png)

备注：工具箱内可设置推送信息，方便推送日志到bark，telegram，微信等等。

<img src="https://image.ioeer.com/resource/20220803/021.png" alt="" style="zoom:67%;" />