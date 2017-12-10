---
title: Ubuntu下如何添加ppa仓库
date: 2016-10-11 22:41:21
tags:
    - linux
---
#### Ubuntu下添加ppa仓库
此处以安装atom为例，命令如下：  
```shell
sudo add-apt-repository ppa:webupd8team/atom  
sudo apt-get update  
sudo apt-get install atom 
```
这样就把atom的仓库添加并安装完成了。  
  
然而，并没有这么简单
```shell
sudo: add-apt-repository : command not found
```
mdzz 这是怎么回事，不应该是这样的(╯‵□′)╯︵┻━┻    

<!--more-->

#### 解决
百度之后（虽然百度烂但是有些东西还是能查到的），发现还要添加一个python-software-properties包，话说为什么apt仓库要用python的包啊喂(#`O′)`)  
没办法，照办吧，遂执行：
```shell
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:webupd8team/atom

sudo: add-apt-repository : command not found
```
什么鬼！！！为什么还是不对，看来依赖还是不足，继续百度，发现还需要安装software-properties-common  
抱着试试的心态执行了一下： 
```shell
    sudo apt-get install software-properties-common
    sudo add-apt-repository ppa:webupd8team/atom

    More info, report packaging bugs, feedback, etc.: http://www.webupd8.org/2014/05/install-atom-text-editor-in-ubuntu-via-ppa.html

    Report non-packaging Atom bugs here: https://github.com/atom/atom/issues
     更多信息： https://launchpad.net/~webupd8team/+archive/ubuntu/atom
    按回车继续或者 Ctrl+c 取消添加

    gpg: 钥匙环‘/tmp/tmp7nbejmnw/secring.gpg’已建立
    gpg: 钥匙环‘/tmp/tmp7nbejmnw/pubring.gpg’已建立
    gpg: 下载密钥‘EEA14886’，从 hkp 服务器 keyserver.ubuntu.com
    gpg: /tmp/tmp7nbejmnw/trustdb.gpg：建立了信任度数据库
    gpg: 密钥 EEA14886：公钥“Launchpad VLC”已导入
    gpg: 没有找到任何绝对信任的密钥
    gpg: 合计被处理的数量：1
    gpg:               已导入：1  (RSA: 1)
    OK
```
执行成功ㄟ(≧◇≦)ㄏ    

#### 结语
虽然感觉这点很坑（话说包管理添加第三方库为什么还要用其他包啊），但是总算圆满解决了问题，可喜可贺，可喜可贺！
