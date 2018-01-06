---
title: clojure学习笔记其一
date: 2018-01-06 15:59:05
tags:
      - 编程语言
      - clojure
---
## 简述

　　学习clojure的起因主要还是想学习lisp语言吧。由于既想学lisp现阶段又无法脱离jvm这个大的框架,所以就发现了clojure语言，然后就踏上了“自虐”的不归路:D

## 学习思路

　　在刚开始学习的过程中，我看了很多相关的教程、博客，但总是感觉不得其门而入，简单来说就是找不到这门语言的学习思路。大体是因为clojure和传统的C type语言思路不同吧，再加上语法糖太多，导致各种磕磕碰碰。仔细想想，还是整理一下目前所学，也希望整理的东西能给clojure的初学者提供一定的帮助。  
　　本系列的笔记是基于官方文档整理、补充的，如有错漏，请在评论区指出，以便及时进行修改，也有助于我自身的提升和进步。

## 语言学习的第一步

没错，就是Hello World！

```clojure
user=> (println "Hello, Clojure!")
Hello, Clojure!
nil
```
这一句是在repl中执行的，哦，差点忘了，记得安装lein哦，至于怎么装请自行百度，如果需要梯子，emmmmm，自己想办法吧，程序员或者想成为程序员的人不应该被一面墙阻拦（有人敲门，我去开一下，Σ(っ °Д °;)っ好像有哪里不对）  

言归正传，输出的结果是“Hello, Clojure!”，但方法的返回值是nil，这个输出其实就是函数println的副产物。

<!-- more -->

## 对于clojure的整体认知

下面这一段是我从官方文档中截取的类型说明。

```clojure
;; 数字类型
42              ; Long - 64-bit integer (from -2^63 to 2^63-1)
6.022e23        ; Double - double-precision 64-bit floating point
42N             ; BigInt - arbitrary precision integer
1.0M            ; BigDecimal - arbitrary precision fixed-point decimal
22/7            ; Ratio

;; 字符类型
"hello"         ; String
\e              ; Character

;; 其他类型
nil             ; null value
true            ; Boolean (also, false)
#"[0-9]+"       ; Regular expression
:alpha          ; Keyword
:release/alpha  ; Keyword with namespace
map             ; Symbol
+               ; Symbol - most punctuation allowed
clojure.core/+  ; Namespaced symbol

;; 集合
'(1 2 3)        ; list
[1 2 3]         ; vector
#{1 2 3}        ; set
{:a 1, :b 2}    ; map

```

clojure所有的语句都是包含括号的，当然像集合那几个声明方式可以除外（集合那几种同样也是有普通的函数声明方式的）。  

括号内的第一个值是函数体，剩下的值都是这个函数的参数。  

这是clojure最基本的一个概念，也是最重要的一个概念，“函数是一等公民”  

同样，当你想这样使用的时候，他会给你一个错误。

```clojure
user=> (1 2 3)
ClassCastException java.lang.Long cannot be cast to clojure.lang.IFn
```

所以你需要使用quote去让clojure知道你不想把它解读成一个函数。

```clojure
user=> (quote (1 2 3))
(1 2 3)

;; 操作同  '(1 2 3)
user=> '(1 2 3)
(1 2 3)
```

不过，这样一等公民就变成quote了，哈哈哈哈

## 说一下变量

说一下变量的一些概念吧，不然第一篇就这么草草的结束，肯定有人要说我没有干货了XD

变量的定义主要包含两种，一个是def，另一个是let

def类似于全局变量，可以被重新赋值

let则相当于局部变量，下面给出了一些例子（感谢clojure-by-example项目提供的例子）。

##### def的使用：

```clojure
user=> (def object "light")
#'user/object

user=> (println (str "God said let there be " object))
God said let there be light
nil

user=> (def object "darkness")
#'user/object

user=> (println (str "God said let there be " object))
God said let there be darkness
nil
```

##### let的使用
```clojure

user> (let [a "aaa"]
        (println a))
aaa
nil

user> (let [a "aaa"]
        (let [a "AAA"]
          (println a)))

AAA
nil

user> (let [a "aaa"]
        (let [a "AAA"]
          (println a))
        (println a))
AAA
aaa
nil

user> (let [a "a"]
        (let []
          (println a)))
a
nil

user=> (let [l "light"
             d "darkness"]
             (println (str "God said let there be " l))
             (println (str "God also said let there be " d)))
God said let there be light
God also said let there be darkness
nil

user> (let [l "light"
            l_d (str l " and" " darkness")]
            (println (str "God also said let there be " l_d)))
God also said let there be light and darkness
nil
```  

## 第一篇就这样吧

　　第一篇姑且就到这里吧，学习这种函数式语言其实最重要的是对于这种语言有一个正确的认知，然后才是各种语法和应用，如果你始终带着面向对象那种思路反而不太利于clojure的学习。
　　嘛~ 各位我们下一篇再见！
