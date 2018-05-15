# 通过Aircrack-ng等工具获取Wifi密码-
在MacOS上，利用Aircrack-ng等工具通过跑字典方式获取附近可见wifi的明文密码

# 本文仅供学习交流，破解他人wifi可能要负担法律责任，请勿触犯法律。本文演示使用的是作者本人的wifi。

### 主要思路

用户与路由器建立连接是通过含有hash加密的密码握手包来确认身份的。那么如果得到握手包，获取到密码的hash值。那么通过跑字典比对hash值就可以得出密码的明文。

所需工具：

* 一个可用的支持监听功能的无线网卡（这句基本是废话）
* Terminal（MacOS系统自带，新版名为‘终端‘）
* Airport工具（MacOS系统自带）
* Aircrack-ng（破解工具）
* 一个密码字典（项目中提供了两个，也可以用其他的替代）
* PS:你或许还需要HomeBrew（用于安装Aircrack-ng）


## 预备！

1.安装HomeBrew

打开终端Terminal，输入如下命令，自动安装HomeBrew。安装完毕后可输入 `brew --version` 检测，若提示 `Homebrew 1.6.3` 则表明安装正确。
```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

2.安装Aircrack-ng

第一步完成后，在终端中输入如下命令，进行安装。安装完毕后可输入 `aircrack-ng` 检测，若提示一系列关于Aircrack-ng的版本信息以及命令选项，则表明安装正确。
```shell 
brew install aircrack-ng 
``` 

3.下载密码字典

作者所用的密码字典是网上的前辈帖子发到帖子里的，当然可以用其他字典替换。

[精简版字典 约700M](http://pan.baidu.com/s/1o7MCcHk) 

[完善版字典 约15G](http://pan.baidu.com/s/1clxaCA)

## 开始！

1.查看本机网卡信息。

点屏幕左上角点小苹果->关于本机->系统报告，找到图中所示，Wi-Fi网卡，记住网卡的BSD设备名称，也就是`en0` `en1`之类的那些。

![截图1](https://raw.githubusercontent.com/ZoraZora59/Get_Wifi_Password_On_MacOS/master/Screenshots/S1.png)

2.启动终端，输入下列命令，探测周围的Wi-Fi信号。
```shell
/System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport -s
```
![截图2](https://raw.githubusercontent.com/ZoraZora59/Get_Wifi_Password_On_MacOS/master/Screenshots/S2.png)

图中`SSID`为Wifi名称，`BSSID`为路由器的MAC地址，`RSSI`为信号强度（这个怎么算的···好像要弄懂信噪比的概念，与破解无关，暂时不管，可以通过点屏幕右上角的Wi-Fi标志看信号强弱嘛🤪），`CHANNEL`是该Wi-Fi所用信道，`SECURITY`是该Wi-Fi的加密方式（现在主流加密方式是WPA2，所以我们针对WPA2进行破解）。

3.在终端中输入下列命令，监听路由器信道，等待获取握手包
```shell
sudo /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport en0 sniff 8
```
![截图3](https://raw.githubusercontent.com/ZoraZora59/Get_Wifi_Password_On_MacOS/master/Screenshots/S3.png)

尽可能选在用户大批连接前开始监听，不然可能会耗费漫长的时间监听到一大堆没用的数据。（这一步可以通过欺骗包方法使用户端误以为路由器重启，从而快速获得握手包。具体方法作者正在研究大佬的博客）

4.等了很久！看看监听的怎么样了！`CTRL+C`快捷键中断监听，在终端里输入`cd /tmp`进入tmp目录，输入`ls`显示目录内文件，监听到的包就放在这里，文件名为`airportSniffCrRc8p.cap`（就是下图中框起来的那个）😈

![截图4](https://raw.githubusercontent.com/ZoraZora59/Get_Wifi_Password_On_MacOS/master/Screenshots/S4.png)

在终端中输入命令对监听包进行分析，看看里面有没有握手包（作者的包是airportSniffCrRc8p.cap，密码字典放在了'密码破解字典'文件夹里，需要自行更改为自己监听到的包名和字典）
```sheel
sudo aircrack-ng -w ~/密码破解字典/crackstation-human-only.txt /tmp/airportSniffCrRc8p.cap
```
![截图5](https://raw.githubusercontent.com/ZoraZora59/Get_Wifi_Password_On_MacOS/master/Screenshots/S5.png)

如果监听包的内容太多可以用快捷键`Command+F`查找关键字来看。

握手包的标记为`1 handshake`，除此之外的都是无效信息。如果全部内容都没有`1 handshake`，很遗憾，重新回到第三步，继续监听信道->分析->监听信道->分析，直到分析到有握手包标记的监听包。

5.当发现有握手包之后，接下来就是最激(man)动(de)人(yao)心(si)的破解阶段！在 aircrack-ng 程序中看到下图提示后输入握手包所在行数（就是#对应的那一列）

![截图6](https://raw.githubusercontent.com/ZoraZora59/Get_Wifi_Password_On_MacOS/master/Screenshots/S6.png)

或者在终端中输入下列命令，开始对监听包进行密码破解（Hash值比对)。
```shell
sudo aircrack-ng -w ~/密码破解字典/crackstation-human-only.txt -b 50:3A:A0:D2:8C:AE /tmp/airportSniffCrRc8p.cap
```
![截图7](https://raw.githubusercontent.com/ZoraZora59/Get_Wifi_Password_On_MacOS/master/Screenshots/S7.png)

这一步默认是由CPU完成的，而CPU本身相对而言不擅长做这种单一的hash计算，擅长做这个的是GPU（原理同显卡挖矿）。可以通过工具将这一步交由GPU来完成，效率会高很多。（不过作者还在研究，做好了会更新的）

6.Finally！密码Hash比对完毕/比对失败！

失败原因非常简单：字典不给力，换个更大更全的吧。或者是设密码的人太变态，弄个`LN&tDIr82!1B`之类的复杂密码，如果是这样的话，放弃吧🤣不然破解到天荒地老。

如果比对成功，程序会进入如下界面，`KEY FOUND ![*]`里面的‘*’就是密码.恭喜获得成功🎉

###（如果你觉得本项目有帮助，请给本项目一个Star，作者感激不尽！）

![截图8](https://raw.githubusercontent.com/ZoraZora59/Get_Wifi_Password_On_MacOS/master/Screenshots/S8.png)
