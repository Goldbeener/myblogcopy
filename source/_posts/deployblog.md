---
title: Hexo + github 个人博客搭建
---
一直有搭建个人站的想法，可是一直没有找到趁手的家伙什，所以迟迟不能付诸行动。最近学完了node，注意到了hexo这个东西，

## Quick Start

### 创建一篇新的文章

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

<!-- more -->

### 本地预览

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### 生成静态文件

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### 部署到公网

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

### 设置favicon

在主题配置文件里面配置favicon选项：   
``` javascript
favicon: path   
```   

把所使用的favicon图标放置再合适的位置，结合主题配置文件的root选项，确定path的具体路径。    
比如root是默认的路径，然后favicon图标放置在source下的img文件夹下；那么path的值就是/img/favicon.ico   
如果favicon图标放置在主题配置文件同级下，那么path的值就是/favicon.ico    
在设置favicon的时候，刚开始没有找准路径，可以去github上博客对应的仓库去看看，在仓库里面找到favicon，然后它相对主干的位置就可以用来作为path的值.     







