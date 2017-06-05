---
title: Go环境搭建小记
date: 2017-03-23 22:00:00
tags: [环境搭建, Go]
---

----------

Go环境搭建
Mac + Go1.7 + VSCode1.8


<!-- more -->
<br/>

由于工作原因，需要学习一下Go。
其实，我一直就对这个语言挺感兴趣的。
虽说技多不压身，但贪多嚼不烂；本来想着嚼烂了客户端，再去学一学Go。
没关系，先提前了解一下吧。
<br/>
这个语言，对新手真是不友好到极点。
搭环境废了很大功夫。

先列一下我的环境：
Mac系统 - Go 1.7 - VSCode 1.8
（ IDE为什么选择VSCode？VSCode优点可以自行Google ）

<br/>

### Go的设置
先把Go下载，然后安装到Mac。
然后需要配置一下环境变量

- GOROOT
这个是你Go安装到的位置，一般在  /usr/local/go
- GOPATH
这个是你工作区路径，一般在自建的文件夹下（这个自建的文件夹下要有3个子文件夹[src、pkg、bin]）
- GOBIN（选配）
工作区路径下的bin文件夹（**路径唯一**）

当时这块就混淆了一下。
因为Go是支持多个工作区域的，后面我们要为Go下载一系列依赖包（就是在命令行用 go get 获得的一堆东西），如果都放在一个工作区域，就会有些混乱。
所以，很多人就建立两个区域，一个放依赖包，一个用来放自己平时的东西。
这时候，环境变量上就要有多个区域，Mac用 ':' 来区隔。（依赖包默认下载到第一个工作区域）

在命令行下：

1. 打开配置（`vim .bash_profile`） 开始搭Go环境。
2. `export GOPATH="/Users/你的用户名/依赖包目录:/Users/你的用户名/工作区目录"`
依赖包默认下载到GOPATH的第一个目录下，所以依赖包目录要放在第一个，然后用':'分隔
`export PATH=$PATH:${GOPATH//://bin:}/bin`
如果设置GOBIN，GOBIN只能设置一个路径，但是你可以将每个GOPATH下的bin添加到PATH中
`export PATH=$PATH:$GOPATH`
路径加入到PATH
3. 保存退出，然后重启配置（source .bash_profile)
4. 输入 go env 看看Go的环境变量

*PS: 关于GOBIN：go install编译存放路径。不允许设置多个路径。可以为空。为空时则遵循“约定优于配置”原则，可执行文件放在各自GOPATH目录的bin文件夹中（前提是：package main的main函数文件不能直接放到GOPATH的src下面。)*

<br/>

### VSCode的配置
下载VSCode，然后在左侧小图标的最下面那个扩展选项中，搜索Go，进行下载(一般会搜索到两个go 和 Go，我下载的是第二个Go)。
接下来，要下载一系列的依赖包。


##### 1. 打开命令行，分别输入以下命令进行下载：

```
go get -u -v github.com/nsf/gocode
go get -u -v github.com/rogpeppe/godef
go get -u -v github.com/golang/lint/golint
go get -u -v github.com/lukehoban/go-outline
go get -u -v sourcegraph.com/sqs/goreturns
go get -u -v golang.org/x/tools/cmd/gorename
go get -u -v github.com/tpng/gopkgs
go get -u -v golang.org/x/tools/cmd/guru
```

这一步，需要  科学上网工具  的支持，而且有时候，你有科学上网工具的支持都不顶用。
本质上讲，以gocode为例，它将gocode相关文件下载到src，然后将它编译生成可执行文件，将可执行文件放到bin目录下。
所以，可以直接去github上下载下来，然后自己编译一下，将生成的可执行文件放到bin目录下。

##### 2. 配置VSCode相关
主要是 settings.json 与 launch.json

如果你配置了 GOPATH、GOROOT ，就不需要配置相应的setting.json。
（进入方法是 code -> 首选项 -> 用户设置）

打开VSCode，选择你的工作区域文件夹，然后在编辑配置文件。（不选文件夹不能配置）
进入方法是 code -> 首选项 -> 工作区设置

主要修改program字段: `program: "${workspaceRoot}"`
如果你的文件，比如test.go 放在src下就： `program: "${workspaceRoot}/src"`

<br/>

### 让Mac 支持 VSCode调试功能
英语原文：  [>这里<](https://github.com/derekparker/delve/blob/master/Documentation/installation/osx/install.md)

<br/>
简单翻译一下步骤：
1.  创建一个自签名证书
	找到钥匙串访问，（ 证书助理 -> 创建证书（身份类型：自签名证书，证书类型：代码签名，勾选 覆盖这些默认值） -> 继续到最后，指定用于该证书的位置: 系统 ）
2.  设定信任属性
	重启系统后，找到自己创建的证书，在 （ 显示简介 -> 信任 -> 代码签名 ） 选择 （ 始终信任 ）
3.  重新编译dlv文件
	打开命令行，进入依赖包所处的工作区: `src/github.com/derekparker/delve`
	如果你的Go版本是1.5，则运行： `GO15VENDOREXPERIMENT=1 CERT=你创建的证书名称 make install`
	其他版本就直接：`CERT=你创建的证书名称 make install`


<br/>

### 总结小记。
终于折腾完了。
花了好久时间，看了N多教程，但还是走了不少弯路。
在 GOPATH、GOROOT理解上，在GOBIN路径唯一上，在创建签名证书处等等。
所以，整理了一下这篇文章，希望对他人有所帮助。