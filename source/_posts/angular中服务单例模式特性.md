---
title: angular中服务单例模式特性
date: 2017-05-18 22:16:29
tags: ['Angular','service','Singleton pattern']
---
设计模式这种高大上的东西，对于小白来说一般都是鸡犬相闻不相往来。没想到今天在做一个表单验证拓展的时候与单例模式来了一次偶遇，不亦乐乎，文以载之。

之前做项目的时候，见到公司的大神写过表单验证的小插件，心向往之，刚好今天在sf上看到了一个应用策略模式优雅的实现表单验证的例子，便拿来练手。如下：

<!-- more -->

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="bootstrap.min.css">
    <script src="jquery.js"></script>
    <script src="bootstrap.min.js"></script>
</head>
<body>
    <form action="https://localhost:8080/" id="validateForm" method="post">
        <div class="form-group">
            <label for="userName">请输入用户名：</label>
            <input type="text" class="form-control" id="user" name="userName"/>
        </div>
        <div class="form-group">
            <label for="pwd">请输入密码</label>
            <input type="password" class="form-control" id="pwd" name="password"/>
        </div>
        <div class="form-group">
            <label for="phone">请输入电话号码：</label>
            <input type="tel" class="form-control" id="phone" name="phoneNumber"/>
        </div>
        <div class="form-group">
            <label for="email">请输入邮箱地址：</label>
            <input type="text" class="form-control" id="email" name="emailAddress"/>
        </div>
        <button type="submit" calss="btn btn-default">Submit</button>
    </form>

    <script>
        let validateForm = document.querySelect('#validateForm');

        //策略类
        const strategies = {
            isNonEmpty(value,errorMsg){
                return value === '' ? errorMsg : void 0;
            },
            minLength(value,length,errorMsg){
                return value.length < length ? errorMsg : void 0;
            },
            isMobile(value errorMsg){
                return !/^1(3|5|7|8|9)[0-9]{9}$/.test(value) ? errorMsg : void 0;
            },
            isEmail(){
                return !/^\w+([+-.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$/.test(value) ? errorMsg : void 0;
            }
        };
        //validate类
        class Validator {
            constructor(){
                this.cache = [];
            }
            add(dom,rules){
                for(let rule of rules){
                    let strategyAry = rule.strategy.split(':');
                    let errorMsg = rule.errorMsg;
                    this.cache.push(() => {
                        let strategy = strategyAry.shift();
                        strategyAry.unshift(dom.value);
                        strategyAry.push(errorMsg);
                        return strategies[strategy].apply(dom,strategyAry);
                    })
                }
            }
            start(){
                for(let validateFunc of this.cache){
                    var errorMsg = validateFunc();
                    if(errorMsg){
                        return errorMsg;
                    }
                }
            }
        }

        //客户端调用
        const validateFunc = () => {
            let validate = new Validator();
            validate.add(validateForm.userName,[
                {
                    strategy: 'isNonEmpty',
                    errorMsg: '用户名不能为空!'
                },
                {
                    strategy: 'minLength:6',
                    errorMsg: '用户名不能少于6位!'
                }
            ]);
            validate.add(validateForm.password,[
                {
                    strategy: 'isNonEmpty',
                    errorMsg: '密码不能为空!'
                },
                {
                    strategy: 'minLength:6',
                    errorMsg: '密码不能少于6位!'
                }
            ]);
            validate.add(validateForm.phoneNumber,[
                {
                    strategy: 'isNonEmpty',
                    errorMsg: '电话号码不能为空!'
                },
                {
                    strategy: 'isMobile',
                    errorMsg: '电话号码格式不正确!'
                }
            ]);
            validate.add(validateForm.emailAddress,[
                {
                    strategy: 'isNonEmpty',
                    errorMsg: '邮箱地址不能为空!'
                },
                {
                    strategy: 'isEmai',
                    errorMsg: '邮箱地址格式不正确!'
                }
            ]);
            return validate.start();
        }
        validateForm.addEventerLister('submit',function(event){
            let errorMsg = validateFunc();
            if(errorMsg){
                alert(errorMsg);
                event.preventDefault();
            }
        },false);
    </script>
</body>
</html>
```

以上是使用策略模式实现表单验证的过程；如果新加验证要求，只需要在策略类里面添加对应的方法即可。         
这样在调用时就相当于简单的配置，只需要指明待验证项、验证要求以及错误提示语即可。      
这样便利的表单验证让人难以把持，于是我打算把她移植到我们正在做的项目中。我们的项目是以ionic作为前端技术的，因此这套验证方法应该以服务的形式出现在项目中。       
分析上例中，策略类和验证类是公用的，因此应该把她们放在服务中。          
第一次我是这样写的：


``` js
appService.service('validator',function(){
    //策略类
    const strategies = {
        isNonEmpty(value,errorMsg){
            return value === '' ? errorMsg : void 0;
        },
        minLength(value,length,errorMsg){
            return value.length < length ? errorMsg : void 0;
        },
        isMobile(value errorMsg){
            return !/^1(3|5|7|8|9)[0-9]{9}$/.test(value) ? errorMsg : void 0;
        },
        isEmail(){
            return !/^\w+([+-.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$/.test(value) ? errorMsg : void 0;
        }
    };
    //validate类
    class Validator {
        constructor(){
            this.cache = [];
        }
        add(dom,rules){
            for(let rule of rules){
                let strategyAry = rule.strategy.split(':');
                let errorMsg = rule.errorMsg;
                this.cache.push(() => {
                    let strategy = strategyAry.shift();
                    strategyAry.unshift(dom.value);
                    strategyAry.push(errorMsg);
                    return strategies[strategy].apply(dom,strategyAry);
                })
            }
        }
        start(){
            for(let validateFunc of this.cache){
                var errorMsg = validateFunc();
                if(errorMsg){
                    return errorMsg;
                }
            }
        }
    }

    this.validator = new Validator();
})
```
这个服务对外暴露出Validator的一个实例；然后前端就直接调用这个实例的add、start方法，进行验证，美滋滋。       
然而理想是丰满的，现实是骨感的；这种用法出现了一个问题：每次只有第一个不符合条件的表单项会被校验出来；补充完第一项，哪怕第二项也不符合条件但也不会校验出来。这就尴尬了，夸她两句也不能这么傲娇啊，只校验出一个就不干了。     
没办法，只能找原因啊，只有第一次有效后面的就没效了，我念叨着这句话，突然想到好像看到过书里说angular的service都是单例模式，联想到这个，我做了对比，发现了一点：     
原例中，在每次点击校验的时候都会生成一个验证类的实例；    
而我的服务版只是提供了一个固定的验证类实例，每次点击都是用的这个验证类实例；

于是我对服务做了改动，把最后一句代码换为：
```
return Validator;
```
我把这个策略类整个暴露出来，这样每次调用时，生成一个新的实例，在实例上进行验证，果然 ALL IS WELL。