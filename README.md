# 通过Aircrack-ng获取Wifi密码-
在MacOS上，利用Aircrack-ng工具通过跑字典方式获取附近可见wifi的明文密码

# 本文仅供学习交流，破解他人wifi可能要负担法律责任，请勿触犯法律。本文演示使用的是作者本人的wifi。

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

![截图1](https://raw.githubusercontent.com/ZoraZora59/Aircrack-ng-Get-Wifi-Password/master/Screenshots/S1.png)

2.启动终端，输入下列命令，探测周围的Wi-Fi信号。
```shell
/System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport -s
```
![截图2](https://raw.githubusercontent.com/ZoraZora59/Aircrack-ng-Get-Wifi-Password/master/Screenshots/S2.png)

图中`SSID`为Wifi名称，`BSSID`为路由器的MAC地址，`RSSI`为信号强度（这个怎么算的···好像要弄懂信噪比的概念，与破解无关，暂时不管，可以通过点屏幕右上角的Wi-Fi标志看信号强弱嘛🤪），`CHANNEL`是该Wi-Fi所用信道，`SECURITY`是该Wi-Fi的加密方式（现在主流加密方式是WPA2，所以我们针对WPA2进行破解）。

### TODO：探测握手包，开始破解。
