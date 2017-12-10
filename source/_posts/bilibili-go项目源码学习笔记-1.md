---
title: bilibili-go项目源码学习笔记_1
date: 2016-09-27 00:02:19
tags: 
    - go
    - bilibili
---
#### bilibili-go服务加载流程浅析

main.go中使用了service包中的application.go创建了新的应用，进而开启了bilibili-go服务，并监听了8080端口。代码如下：  
```go
    package main

    import (
        "github.com/go-playground/log"
        "github.com/whiteblue/bilibili-go/service"
    )

    func main() {
        app, err := service.NewApplication("conf.json")
        if err != nil {
            log.Fatal(err)
        }

        app.Router.Run(":8080")
    }
```
在application.go中，使用了gin作为web框架，并依次执行了以下操作： 
<!--more-->
 
首先读取了appkey和secret，  
```go
    conf, err := ReadConfigFromFile(configFile)
```
设置日志，  
```go
    cLog := console.New()

    if conf.Debug {
        log.RegisterHandler(cLog, log.AllLevels...)
        gin.SetMode(gin.DebugMode)
    } else {
        log.RegisterHandler(cLog, ProdLevels...)
        gin.SetMode(gin.ReleaseMode)
    }
```
加载了gin的中间件，  
```go
    r := gin.New()

    //use gzip
    r.Use(gin.Recovery())
    r.Use(gzip.Gzip(gzip.BestCompression))
```
启动了CornService用于初始化数据（主要初始化了各类别下的rank数据），  
```go
    //corn service
    corn := NewCornService()
```
创建了bilibiliClient，  
```go
    //bilibili client
    cli := client.NewClient(conf.Appkey, conf.Secret)
```
启动了缓存管理,  
```go
    cache := NewCacheManager()
```
最后建立app，并执行了初始化操作,  
```go
    //建立app应用
    app := &BiliBiliApplication{Router: r, Corn: corn, Conf: conf, Client: cli, Cache: cache}

    //加载路由
    ConformRoute(app)

    log.Info("conform route")

    //进行rank数据初始化
    conformTask(app)
    corn.Start()

    log.Info("conform task")

    log.Info("init complete, start listen...")
```

完整代码如下：  
```go
func NewApplication(configFile string) (*BiliBiliApplication, error) {
    conf, err := ReadConfigFromFile(configFile)

    if err != nil {
        return nil, err
    }

    cLog := console.New()

    if conf.Debug {
        log.RegisterHandler(cLog, log.AllLevels...)
        gin.SetMode(gin.DebugMode)
    } else {
        log.RegisterHandler(cLog, ProdLevels...)
        gin.SetMode(gin.ReleaseMode)
    }

    log.Info("conform config file")

    r := gin.New()

    //use gzip
    r.Use(gin.Recovery())
    r.Use(gzip.Gzip(gzip.BestCompression))

    //corn service
    corn := NewCornService()

    //bilibili client
    cli := client.NewClient(conf.Appkey, conf.Secret)

    cache := NewCacheManager()

    app := &BiliBiliApplication{Router: r, Corn: corn, Conf: conf, Client: cli, Cache: cache}

    ConformRoute(app)

    log.Info("conform route")

    conformTask(app)
    corn.Start()

    log.Info("conform task")

    log.Info("init complete, start listen...")

    return app, nil
}
```

通过路由和初始化的rank数据，就可以获取相应的列表以及视频信息了。