---
title: 对于Go语言中指针的思路整理
date: 2016-09-21 12:58:24
tags: 
    - go
---
#####  为什么要写这个 
***  
最近对go语言萌生了不小的学习欲望，因而找了一本书开始看，看到指针一节，不禁再次想起了那些被c语言支配的恐怖日子（这也是我选择java的原因啊，（╯' - ')╯︵ ┻━┻ ）。不过，经过再次整理之后发现其实指针这个概念还是蛮好理解的，也希望这篇小文章能给那些难以理解指针的童鞋一点帮助（没帮倒忙就好，o(╯□╰)o），下面就来详细说明一番。

##### 指针概念解析  
*** 
指针的概念其实主要分为两个部分，一个是内存地址，另一个则是内存地址指向的值。举个栗子：  
```go
package main

import (
	"fmt"
)

func main() {
	a := 3
	b := 5
	fmt.Println(a, b) //输出值：3 5
}
```
<!--more-->

对于变量a而言，&a就是内存中变量a的地址。那么我们如何通过内存中的地址获取到值呢，看下面代码：   
```go
package main

import (
	"fmt"
)

func main() {
	a := 3
	c := &a
	fmt.Println(*c) //输出值：3
}
```
我们将a的内存地址&a赋给变量c，通过*c就取出了地址&a中的值，因此通过*就可以将相应地址的值取出了。

##### 总结 
*** 
*和&是一对相对的概念，&代表地址，*代表地址对应的值，因此一个变量
```go
a := 3
```
&a代表了变量a内存地址，而a的值就可以写作*&a了。怎么样，是不是很简单（本来就很简单啦，果然还是自己太笨了，╮(╯▽╰)╭）