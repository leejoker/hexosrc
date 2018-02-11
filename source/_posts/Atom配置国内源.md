---
title: Atom配置国内源
date: 2018-01-15 15:48:19
tags:
      - atom
      - 配置技巧
---

#### 配置国内插件源

atom这个编辑器确实很好用，但是她的插件安装的速度实在是让人难受，没有梯子的话会让人抓狂w(ﾟДﾟ)w，在网上查了一下，找到了一个比较靠谱的方案，记下来方便以后使用。

1. 本方法在linux和windows下都是可用的
2. 在**$HOME/.atom**目录下新建**.atomrc**文件
3. 在文件内添加如下内容：

  ```text
  registry=https://registry.npm.taobao.org/  
  strict-ssl=false
  ```

Over !
