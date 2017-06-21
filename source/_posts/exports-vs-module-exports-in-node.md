---
title: exports vs module.exports in node
date: 2017-05-16 09:47:49
tags: 'node'
---

我们知道，在node编译过程中，会对每个模块文件进行头尾包装；加上module、exports、require、__filename、__dirname等变量；         
例如下面这个模块文件，      
``` js
const test = require('./test.js');
exports.res = test.add(1,2);
```
<!-- more -->

在经过编译包装之后会变成如下的文件：
``` js
(function(exports,require,module,__dirname,__filename){
    const test = require('./test.js');
    exports.res = test.add(1,2);
})
```

这也是为什么在模块文件中，我们并没有对require、exports等变量进行定义；却可以使用。

现在知道了每个文件模块都会有exports、module等变量；       
在初始化中他们的值是这样的:
``` js
module.exports = {};
exports = module.exports;     //初始exports是module.exports的一个引用
```

另外还需要注意的一个问题是，模块文件导出的对象是module.exports上面挂载的内容；也就是说通过require（）方法引入的对象对应的是目标模块文件中的module.exports上的对象；       

知道了以上几点，就可以理解exports和module.exports的区别了；
正常情况下：
``` js
//test.js模块文件
exports.add = function(a,b){
    return a + b;
};
//这种情况下，exports = module.exports 的引用指向没有改变；在exports上面挂载方法，那么module.exports也会得到相同的方法；那么就可以对外导出add这个方法；
```

如果直接在exports上面挂载内容，这时候引用的时就会出错；
``` js
//错误的test.js模块文件
exports = function(a,b){
    return a + b;
}
//这种情况下，exports的指向改变；exports = module.exports的引用断裂；在exports上面添加的方法；module.exports并不会得到；这样对外导出的module.exports依然等于 {}；require（）之后取不到方法会报错。 
```

