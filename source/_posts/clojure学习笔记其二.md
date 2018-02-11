---
title: clojure学习笔记其二
date: 2018-01-07T16:02:17.000Z
tags:
  - clojure
  - 编程语言
---

# 今天的主题是Function

正如标题所言，今天这篇笔记的主题是function，当然也就是lisp语言中的一等公民，"函数"啦XD

## 那么直入主题，首先来看一下怎么定义一个函数：

```clojure
;;    name   params         body
;;    -----  ------  -------------------
(defn greet  [name]  (str "Hello, " name) )
```

这里我引用了[官方文档](https://www.clojure.org/guides/learn/functions)中的说明。

那么这个函数的使用就可以像上一篇中所说的那样：

```clojure
user=> (greet "小明")
"Hello, 小明"
```

## 你也可以给你的方法增加方法说明：

```clojure
(defn greet  
  "this is the greet function for you"
  [name]  
  (str "Hello, " name))
```

<!-- more -->

 ## 文档说明可以用doc函数去查看:

```clojure
(doc greet)
;;----------------------------------
user/greet
([name])
  this is the greet function for you
```

## 函数是支持定义多种参数列表的

```clojure
(defn messenger
  ([]     (messenger "Hello world!"))
  ([msg]  (println msg)))
```

当传入不同的参数的时候，会根据参数列表执行不同的函数体。

但执行的方法没有匹配的参数列表时：

```clojure
(messenger "aaa" "bbbb")
ArityException Wrong number of args (2) passed to: user/messenger  clojure.lang.AFn.throwArity (AFn.java:429)
```

那么怎么实现上面这种不定参数的问题呢，那就需要用到 & 符号：

```clojure
(defn messenger [greeting & who]
  (println greeting who))

(messenger "aaa" "bbbb" "cccc" "dddd")
;;------------------------------------------------------
aaa (bbbb cccc dddd)
```

或许和你的预期不同，clojuer将&符号后面的参数认为是一个列表，因此在打印的时候是按照列表打印的，而没有与第一个参数作为同一个列表。

## 匿名函数

匿名函数有两种写法：

```clojure
;; 写法一
((fn [x] (println "Hello, " x)) "Leejoker!")
Hello,  Leejoker!
user=>
nil
;;------------------------------------------------------
;; 写法二
(#(println "Hello, " %) "Leejoker!")
Hello,  Leejoker!
user=>
nil
```

那让我们看一下这两种写法的组成吧：

```clojure
;;    anonymous params         body
;;    --------  ------  -------------------
  (fn           [name]  (str "Hello, " name) )

;;    anonymous       body    params
;;    --------  ------------ -------  
#(             (str "Hello, " %1 %2 %&))

;; 同样，匿名函数里也可以用 & 来定义不定参数列表
```

当然，你也可以把匿名函数绑定一个函数名：

```clojure
(def greet (fn [x] (println "Hello, " x)))
;; 或者是这样
(def greet #(println "Hello, " %))

;;-------------------------------------------------------
(greet "lala")
Hello,  lala
user=>
nil
```

## 好用的apply

apply可以使一个函数依次调用一个列表中的参数：

```clojure
(defn plot [a b]
  (apply + a b))

(plot 1 '(2 3 4 5))
;;----------------------------------------
user=>
15
```

## closure

clojure的总用看官方文档说应该是用来突破参数作用范围的，这里理解的可能不是很透彻，也希望有大大能够指正，下面是从[官方文档](https://www.clojure.org/guides/learn/functions#_closures)中拿来的例子：

```clojure
(defn messenger-builder [greeting]
  (fn [who] (println greeting who))) ; closes over greeting

;; greeting provided here, then goes out of scope
(def hello-er (messenger-builder "Hello"))

;; greeting value still available because hello-er is a closure
(hello-er "world!")
;; Hello world!
```

## java function

最后一部分就是java方法的调用了：

```clojure
(defn getlen [obj] (.length obj))
;;--------------------------------------------
(getlen "123")
user=>
3
```

下面是官方文档给出的调用方式：

 Task  |              JAVA              |          Clojure
:----: | :----------------------------: | :-----------------------:
 实例化   | Object obj = new Object("foo") | (def obj (Object. "foo"))
实例方法调用 |         obj.toString()         |      (.toString obj)
实例属性值  |           obj.field            |       (.-field obj)
 静态方法  |         Math.sqrt(25)          |      (Math/sqrt 25)
 静态属性  |            Math.PI             |          Math/PI

## 官方文档中习题

1. Define a function greet that takes not arguments and prints "Hello". Replace the _ with the implementation:

  ```clojure
  (defn greet [] _)
  ;;---------------------------------
  (defn greet [] (println "Hello"))
  ```

2. Redefine greet using def, first with the fn special form and then with the #() reader macro.

  ```clojure
  ;; using fn
  (def greet __)

  ;; using #()
  (def greet __)
  ;;------------------------------------------------
  (def greet (fn [] (println "Hello")))
  (def greet #(println "Hello"))
  ```

3. Define a function greeting which:

  - Given no arguments, returns "Hello, World!"
  - Given one argument x, returns "Hello, x!"
  - Given two arguments x and y, returns "x, y!"

  ```clojure
  ;; Hint use the str function to concatenate strings
  (doc str)

  (defn greeting ___)

  ;; For testing
  (assert (= "Hello, World!" (greeting)))
  (assert (= "Hello, Clojure!" (greeting "Clojure")))
  (assert (= "Good morning, Clojure!" (greeting "Good morning" "Clojure")))
  ;;-----------------------------------------------------------
  (defn greeting
   ([] (str "Hello, World!"))
   ([param] (str "Hello, " param "!"))
   ([param1 param2] (str param1 ", " param2 "!")))
  ```

4. Define a function do-nothing which takes a single argument x and returns it, unchanged.

  ```clojure
  (defn do-nothing [x] ___)
  ;;--------------------------------
  (defn do-nothing [x] x)
  ```

5. Define a function always-thing which takes any number of arguments, ignores all of them, and returns the keyword :thing.

  ```clojure
  (defn always-thing [__] ___)
  ;;---------------------------------
  (defn always-thing [& args] :thing)
  ;; ps:
  ;;      & 符号后面必须有空格才能识别
  ;;      &args 这样写无效
  ```

6. Define a function make-thingy which takes a single argument x. It should return another function, which takes any number of arguments and always returns x.

  ```clojure
  (defn make-thingy [x] ___)

  ;; Tests
  (let [n (rand-int Integer/MAX_VALUE)
       f (make-thingy n)]
   (assert (= n (f)))
   (assert (= n (f :foo)))
   (assert (= n (apply f :foo (range)))))

  ;;---------------------------------------------------------
  (defn make-thingy [x] (fn [& args] x))
  ```

7. Define a function triplicate which takes another function and calls it three times, without any arguments.

  ```clojure
  (defn triplicate [f] ___)

  ;;----------------------------------
  (defn triplicate [f] (f) (f) (f))

  ;;PS:
  ;;    在网上查了一下，问题的翻译是定义一个函数，参数是另一个函数，并在函数体
  ;;    中调用3次，大概是这么个意思，刚开始看了好长时间都没明白过来要我做什么，汗
  ```

8. Define a function opposite which takes a single argument f. It should return another function which takes any number of arguments, applies f on them, and then calls not on the result. The not function in Clojure does logical negation.

  ```clojure
  (defn opposite [f]
   (fn [& args] ___))
  ;;-----------------------------
  (defn opposite [f]
   (fn [& args] (not (apply f args))))
  ```

9. Define a function triplicate2 which takes another function and any number of arguments, then calls that function three times on those arguments. Re-use the function you defined in the earlier triplicate exercise.

  ```clojure
  (defn triplicate2 [f & args]
   (triplicate ___))
  ;;--------------------------------
  (defn triplicate2 [f & args]
   (triplicate (fn [] (apply f args))))
  ```

10. Using the java.lang.Math class (Math/pow, Math/cos, Math/sin, Math/PI), demonstrate the following mathematical facts: _The cosine of pi is -1_ For some x, sin(x)^2 + cos(x)^2 = 1

  ```clojure
  (= 1.0 (+ (Math/pow (Math/sin Math/PI) 2) (Math/pow (Math/cos Math/PI) 2)))

  (= -1.0 (Math/cos Math/PI))
  ```

11. Define a function that takes an HTTP URL as a string, fetches that URL from the web, and returns the content as a string.

  ```clojure
  ; Hint: Using the java.net.URL class and its openStream method.
  ; Then use the Clojure slurp function to get the content as a string.
  (defn http-get [url] ___)
  (assert (.contains (http-get "http://www.w3.org") "html"))
  ;;-----------------------------------------------------------
  (defn http-get [url]
    (slurp (.openStream (java.net.URL. url))))
  ```

12. Define a function one-less-arg that takes two arguments: * f, a function * x, a value and returns another function which calls f on x plus any additional arguments.

  ```clojure
  (defn one-less-arg [f x]
    (fn [& args] ___))
  ;;-----------------------------
  (defn one-less-arg [f x]
    (fn [& args] (apply f x args)))
  ```

13. Define a function two-fns which takes two functions as arguments, f and g. It returns another function which takes one argument, calls g on it, then calls f on the result, and returns that.

  ```clojure
  (defn two-fns [f g]
    ___)
  ;;----------------------------
  (defn two-fns [f g]
    (fn [x] (f (g x))))
  ```
