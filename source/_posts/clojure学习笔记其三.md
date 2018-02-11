---
title: clojure学习笔记其三  
date: 2018-01-21 15:47:33  
tags:  
       - clojure
       - 编程语言
---

# Namespace

名称空间对于Clojure来说是一个逻辑上对于代码进行分组的概念，类似于Java的package，通过使用名称空间能够规避方法名冲突，有效的对代码进行管理。主要应用的话，其实就是方便代码的引入和使用了，毕竟你不可能将所有代码放到一个clj文件中。

### create-ns

```clojure  
user> (create-ns 'clojure.by.example)
nil
```

这样一个名称空间就建立好了，是不是so easy！

### in-ns

```clojure
;; create the namespace and switch to it
(in-ns 'first-namespace)
```

in-ns的主要作用是切换到指定的namespace中，如果这个namespace不存在，则会创建他。

<!-- more -->

### require/refer/use

这三个要放在一起来说，因为他们的作用都是引入一个namespace，但是使用细节上略有不同。

#### require

require的作用最为纯粹，就是单纯的引入一个namespace。

```clojure
;; 最简单的使用
user> (require 'clojure.by.example)
nil

;; 当然你也可以起个别名
user> (require '[clojure.by.example :as cbe])
nil

;; 当然你还可以引入多个
user> (require '[clojure.by.example :as cbe]
               '[clojure.core :as cc])
nil
```

引入namespace后，你就可以调用namespace中的方法了。  

```clojure
user> (cbe/your_function_name)
```

#### refer/use

refer的作用是绑定一个namespace到当前namespace，这样你就可以直接使用函数名来调用函数，而不需要前面的namespace了。

```clojure
user> (refer 'clojure.by.example)
nil

user> (your_function_name)
```

refer的使用存在一定问题，就是后引入的namespace中如果方法名与当前namespace或之前引入的namespace中存在冲突，会自动覆盖这些冲突，而且你无法在引入之后重新定义这些函数，会抛出异常。

```clojure
;; 就是下面类似的这种异常
CompilerException java.lang.IllegalStateException: join already refers to: #'clojure.string/join in namespace: user, compiling:(null:1:1)
```

refer还可以单独引入namespace中的特定方法。

```clojure

;; 引入的namespace中的方法为了避免冲突是可以进行重命名的
user=> (refer 'clojure.string
              :rename '{capitalize cap, trim trm})

;; only可以单独引入namespace中的指定方法，但是方法名必须是原名，而不能是经过rename之后的名字
user> (refer ‘clojure.string :only ‘[capitalize trim]) 

;; 这样写就会抛出异常
user=> (refer 'clojure.string
              :rename '{capitalize cap, trim trm}
              :only '[cap trm])
IllegalAccessError cap does not exist  clojure.core/refer (core.clj:3849)

```

而use就是将require和refer合二为一的函数，这里就不再赘述了。

#### import

import是clojure中专门用来引入java包的函数。

```clojure
user=> (import java.util.Date)
java.util.Date

user=> (new Date)
#inst "2018-01-21T09:49:55.468-00:00"
```

#### ns

ns方法感觉是引入namespace中相对常用的函数。

```clojure
(ns example.namespace
  (:require [clojure.java.io])
  (:use [clojure.data])
  (:import [java.util List Set]))
```