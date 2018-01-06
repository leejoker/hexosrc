---
title: 折腾hexo系列之在jacman里集成gitment评论系统
date: 2017-10-11 00:09:08
tags: 
    - 折腾hexo  
---

### 从disqus到gitment
其实就易用性和使用效果上讲，disqus还是很好用的，只需要注册一个账号，在主题里配置一下就可以了，然而，disqus最大的问题还是在于~~翻墙~~，咳，科学上网。今天这个问题我的一个小伙伴也跟我吐槽了，毕竟不是每个人都会去弄个vpn啊，ss啊之类的东西。因此，回家之后果断搞起。  

<!--more--> 

### 接下来就是正片了 

#### 注册OAuth Application  
[点此注册] [1]  
填写示例如下图所示：  
![hexo_gitment1.png](https://ooo.0o0.ooo/2017/10/11/59dcf56b6812c.png)  
填写的重点主要在“Authorization callback URL”，这里应该填你的blog的域名（比如我的就是https://leejoker.github.io）  
注册好之后会获得一个client id和client secret，这个就是用来配置gitment评论与你 GitHub账户关联的信息 。（这个没记住也不要紧，之后也可以在github的settings/Developer settings中找到滴XD）
[1]: https://github.com/settings/applications/new        "注册OAuth Application"  

*** 

#### 在主题中配置gitment
我使用的是jacman主题，网上配置gitment的教程大多都是next主题的，因此，特地折腾了一番才配好。当然，使用next主题的同学请自行百度/谷歌教程。  

##### 创建用来存储评论的repo  

在GitHub上创建一个repo用来存储评论信息，记得勾上初始化（一开始没勾，结果怎么都不能初始化评论，我真傻，真的QAQ）

##### 修改jacman评论的布局文件
jacman评论的布局文件在“themes\jacman\layout\_partial\post”目录下，打开comment.ejs,将以下代码覆盖原有代码（没错，就是覆盖）。
   
```html
<section id="comments" class="comment">
  <div id="blog_comment"></div>
  <link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
  <script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
  <script>
  var gitment = new Gitment({
    owner: '你的github用户名',
    repo: '你用来存储评论信息的repo',
    oauth: {
      client_id: '之前注册生成的client_id',
      client_secret: '之前注册生成的client_secret',
    },
  })
  gitment.render('blog_comment')
  </script>
</section>

```

这里有一点需要注意的就是“blog_comment”这个id，之前[作者的blog] [2]中的代码用的是“container”作为id的，但是jacman这个主题用来包裹整个文本的div的id就是container，因此，必须修改id才行，否则评论就会出现在正文的正上方(╯‵□′)╯︵┻━┻
[2]: https://imsun.net/posts/gitment-introduction/          "Gitment：使用 GitHub Issues 搭建评论系统"

***

#### 初始化评论

将配置好的blog发布到github上之后，打开你的任意一篇文章，在最下面点击初始化评论按钮即可。不过这里很不幸的是，你每一篇blog都要手动初始化一下，很蛋疼，目前还没有找到解决的办法，希望后续能解决这个问题。