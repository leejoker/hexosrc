---
title: bilibili-go项目源码学习笔记_2
date: 2016-09-27 00:02:19
tags: 
    - go
    - bilibili
---
这一篇主要说明application.go的主要构成和其中用到方法的主要作用。

##### 参数定义  
application.go中定义了初始化时需要用到的常量和变量，  
1. 常量定义：  
```go
const (
    INDEX_CACHE        = "index"        //各类别下rank
    ALL_RANK_CACHE     = "all_rank"     //总的rank
    BANGUMI_CACHE      = "bangumi"      //番剧
    BANGUMI_LIST_CACHE = "bangumi_list" //新番放松表
    SORT_TOP_CACHE     = "sort-"        //具体某个类别rank(这个在index_cache中要用到)
    LIVE_INDEX_CACHE   = "live_index"   //直播rank
)
```

<!--more-->

2. 全局变量定义：  
```go
var (
    ProdLevels = []log.Level{
        log.InfoLevel,
        log.NoticeLevel,
        log.WarnLevel,
        log.ErrorLevel,
        log.PanicLevel,
        log.AlertLevel,
        log.FatalLevel,
    }
)
```
这里主要定义了release模式下用到的日志级别。(PS:类库引用的是"github.com/go-playground/log")  

3. 结构体定义
```go
type BiliBiliApplication struct {
    Router *gin.Engine        //路由
    Corn   *CornService       //初始化服务
    Conf   *Config            //配置信息
    Client *client.BCli       //客户端
    Cache  *CacheManager      //缓存管理
}
```
这里定义了Bilibili-go主体程序的结构。

##### 初始化rank数据并注册任务

application.go中使用了conformTask方法进行了数据的初始化以及任务注册  
```go
//application.go
conformTask(app)
corn.Start()
```
conformTask方法如下：  
```go
//application.go
func conformTask(app *BiliBiliApplication) {
    app.Corn.RegisterTask(&IndexInfoTask{CornTask: CornTask{Name: "index_info", Duration: 2 * time.Hour}, app: app})
    app.Corn.RegisterTask(&BangumiInfoTask{CornTask: CornTask{Name: "bangumi_info", Duration: 6 * time.Hour}, app: app})
    app.Corn.RegisterTask(&BangumiListTask{CornTask: CornTask{Name: "bangumi_list", Duration: 6 * time.Hour}, app: app})
    app.Corn.RegisterTask(&TopRankTask{CornTask: CornTask{Name: "top_rank", Duration: 2 * time.Hour}, app: app})
    app.Corn.RegisterTask(&LiveIndexTask{CornTask: CornTask{Name: "alive_index", Duration: 2 * time.Hour}, app: app})
}
```
从方法名上看是调用了CornService中的RegisterTask(注册任务)的方法，那么，我们看一下这个RegisterTask方法到底做了些什么吧。  
```go
//corn.go
//execute task
func exec(f CornTaskImpl) {
    log.Info("invoke task, taskname: ", f.GetName())
    defer func() {
        if r := recover(); r != nil {
            log.Error(r)
        }
    }()

    if err := f.Run(); err != nil {
        f.Failure(err)
    } else {
        f.Success()
    }
    log.Info("run task end, taskname: ", f.GetName())
}

//这里就是那个名为注册任务的方法
func (c *CornService) RegisterTask(task CornTaskImpl) {
    task.SyncLastRunTime()
    exec(task)
    c.tasks = append(c.tasks, task)
}
```
不难发现，实际上这个注册任务的方法就是将task执行了一下，然后放到c.tasks数组里。

而之后的corn.Start()方法：  
```go
//corn.go
func (c *CornService) syncTaskList(nowTime time.Time) {
    for _, task := range c.tasks {                          //循环任务列表
        //Unix timestamp => duration
        between := time.Duration(nowTime.Unix()-task.GetLastRun().Unix()) * time.Second
        if between >= task.GetDuration() {                  //当时间间隔大于设定的时间周期则执行,并修改LastRun的值
            task.SyncLastRunTime()
            exec(task)
        }
    }
}

func (c *CornService) loop() {
    for {
        select {
        case <-c.done:
            log.Info("corn loop stopped....")
            return
        case nowTime := <-c.ticker.C:          //这里通过计时器获取到当前时间
            go c.syncTaskList(nowTime)
        }
    }
}

func (c *CornService) Start() {
    go c.loop()
}
```
corn.Start()方法开启了一个loop线程，这个线程简单的来说就是每隔一分钟执行一次syncTaskList方法。    


到此，bilibili-go这个小项目的学习也基本上告一段落了，剩下的内容基本上都是数据获取和处理的部分。这个项目虽然很小，但是对多线程的学习，go的基础都有很大的帮助，在此也感谢WhiteBlue大大，(*^_^*)

