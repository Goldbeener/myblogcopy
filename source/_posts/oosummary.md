---
title: OO Summary
tags: ['js','oo']
---
面向对象，首先你得有一个对象。（单身狗受到一万点暴击，掩面而去。。。）     
那么如何找到一个对象呢？去非诚勿扰还是缘来是你？   
不用辣么麻烦，下面这六种方式可以给你制造一个鲜活的对象。拿走不谢！

<!-- more -->

## 对象生成大法

### Object 实例

``` js
var obj = new Object();
```

这种方法是最基础的，通过创建一个Object对象实例生成一个对象。可以，不多哔哔。

### 对象字面量

``` js
var obj = {};
```

这种方式是通过对象字面量的方式创建一个对象，简单粗暴，所见即所得。       
上面这两种方法都是比较基础，对于那些一个对象就可以满足的，是不错的选择；然而，很多情况下一个对象是满足不了的（怎么感觉好污啊）；在创建多个对象的时候，就会产生大量的重复代码，这就很难受了。代码又不是按字数算钱的。      
这时候就需要下面这几种方式，让你快速生成大量对象，想要几个要几个，方便高效。

### 工厂模式

``` js
function createBeauty (name,age){
    var obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.sing = function(){
        alert('I am waiting for you !')
    };
    return obj;
}

//使用方法    
var beauty1 = createBeauty('Lucy',18);
var beauty1 = createBeauty('Lily',16);
```
在这种模式下，就好像工厂里已经生产了很多的对象，但是出于无意识状态下；你只需要给她们起个名字、点个技能，这些对象就嗖嗖醒来，供你差遣。     
但是这种模式也有问题，一是对象的识别问题；工厂批量生产的，你不知道这个对象从哪来到哪去，也就是找不到对象的源，使用instanceof 找不着爹；二是这种对象往往在生产之后需要特定的几项因素才能使用，少了的话内部会出错。

### 构造函数模式

``` js
function Beauty (name,age){
    this.name = name;
    this.age = age;
    this.sing = function(){
        alert('I am waiting for you !');
    }
}

//使用方法
var beauty1 = new Beauty('Lucy',18);
var beauty2 = new Beauty('Lily',16);
```

这种模式就相当于，提前生产了一个母体；需要对象的时候现生一个；就解决了对象从哪来的问题。     
所有的对象使用 instanceof 都是指向  Beauty 的
