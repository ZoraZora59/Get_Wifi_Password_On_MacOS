# 通过Aircrack-ng获取Wifi密码-
在MacOS上，利用Aircrack-ng工具获取附近可见wifi的明文密码

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
		 
## 开始！
	
