---
title: Javascript中的模块规范
date: 2017-05-16 22:47:04
tags: ['js','模块规范']
comments: true
---

随着各种前端库和框架被开发出来，以及node的大放异彩，Javascript从最初的辅助脚本语言变迁到应用开发级别上。但是Javascript由于自身弱语言的特质，没有类及模块的概念，在开发大型应用上会先天不足。因此对于js来说合理的模块机制是非常重要的。      

在js规范的发展过程中，CommonJs规范的提出是具有重要意义的。

CommonJs规范涵盖了很多内容，包括模块、二进制、Buffer、包管理等等。  

<!-- more -->     

其中对模块的定义包含三个部分：      
* 模块引用     
* 模块定义      
* 模块标识     

模块引用就使用require()方法      
``` js
var m1 = require('module1');
```

模块定义，模块可以是使用上述的require方法引入其它模块；另外上下文还提供了exports对象用以对外导出内容；同时还提供了module对象来代表本模块。例如一个求和的模块可以如下：         
``` js
//add.js
var sth = require('sth');     //本例没有使用
exports.add = function(a,b){
    var sum = 0,
        args = arguments,
        l = arguments.length;
    for(var i = 0; i < l; i++){
        sum += args[i];
    }
    return sum;
}
```

模块标识就是require()方法的参数，可以是小驼峰的字符串、相对路径、绝对路径、没有后缀的js文件名。

CommonJ规范使用require()方法引入依赖模块，这个方法是同步加载的，加载过程中会阻塞后续代码的执行。对于服务器来说文件存储在本地硬盘，同步读取的速度很快，几乎不会对后续代码产生影响，因此CommonJs规范可以在服务器端使用。但是对于浏览器来说，文件需要从服务器获取，读取的速度取决于网速的快慢，如果使用这种同步加载模块的方式在遇到网速慢的情况下，那么浏览器会因为读取部分模块而阻塞页面的渲染，这在前端是不允许的。因此，CommonJs是不适合用在浏览器端的。

因此针对浏览器的情况，提出了异步加载模块的规范，也就是AMD规范。     
AMD规范也是采用require方法加载模块，但是异步加载。模块的加载不影响后续代码的执行，加载完毕后通过回调函数执行依赖该模块的代码。         
``` js
//主模块
/*
require([],fn)  方法接收两个参数，
[]   依赖模块数组
fn   回调函数  数组有几项，就有几个形参，分别代表数组中的依赖模块
*/
require(['module1','module2'],function(m1,m2){
    var m = m1.fn();
    var n = m2.key;
    .......
});


//作为依赖项的模块的定义方式
define(id,['dep1','dep2'...],function(d1,d2...){
    //module code 
})
/*
接收三个参数
id  模块标识，可选 ，没有的话以文件名引入
[]  依赖项数组
fn  回调函数   数组有几项，就有几个形参，分别代表数组中的依赖模块
*/
```

除了AMD规范之外，还有CMD规范，也可以用作浏览器端的模块规范

``` js
//CMD
define(function(require，exports，module){
  //依赖就近，在需要依赖模块的地方随时调用require（）引入
  var dep1 = require('dep1');
  ....
  var dep2 = reqire('dep2');
  
  ....
  //向外导出的方法、属性
  exports.dosth = function(){....};
  return {}
})
```

CommonJs模块规范为服务器端js模块指明了方向，同步加载模块；
AMD/CMD模块规范为浏览器端js模块指明了方向，异步加载模块；
但是目前js已经可以同时运行于浏览器、服务器上，前后端开发语言统一，那么我们自然希望同一个模块既可以服务于浏览器也可以服务于服务器；那么就要求这个模块在浏览器端时要符合AMD/CMD规范，在服务器端时要符合CommonJs规范；因此就产生了一种通用的UMD模块规范，可以同时兼容CommonJs、AMD/CMD等；
 
``` js
(function(name,definition){
    var hasDefine = typeof define === 'function',
        hasExports = typeof module !== 'undefined' && module.exports;
        
    if(hasDefine){
        // AMD/CMD环境
        define(definition);
    }else if(hasExports){
        // node 环境  普通node模块
        module.exports = definition();
    }else{
        //否则的话就将模块的执行结果挂载到window对象上，浏览器环境下this指向window
        this[name] = definition();
    }

})('moduleName',function(){
    //module code
})
```




