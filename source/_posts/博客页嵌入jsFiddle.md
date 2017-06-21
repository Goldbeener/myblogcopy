---
title: 博客页嵌入jsFiddle
date: 2017-06-07 22:43:45
tags:
    - blog
---
写博客的时候，示例代码如果能够直接执行，简单粗暴的看到效果，那必然是极好的。     
在阅读别人的技术博客时，发现大都数人选择使用的都是jsFiddle，因此也找来相关资料，在自己的博客页面上也嵌入jsFiddle试试。      
1、进入[jsFiddle官网](https://jsfiddle.net/)，可以看到如下的样子  

<!-- more -->

![jsFiddle offical website](博客页嵌入jsFiddle/site.png)

页面四个区域，右下角时结果输出区域，其它三个区域写对应的代码     
左上角有四个按钮，依次是运行、保存、格式化、       
2、在页面上的对应区域写上代码之后，点击保存按钮，会得到一个链接；     
3、在博文中插入以下代码，就可以在博文中得到之前写的代码的内容和显示效果      
```js
<iframe
    style="width: 100%; height: 300px;"
    src=""
></iframe>
```
