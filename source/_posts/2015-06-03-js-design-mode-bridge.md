---
layout: post
title: javascript桥梁模式
date: 2015-06-03
categories: [javascript, 设计模式]
---

桥模式:

桥梁模式可以用来弱化它与使用它的类和对象之间的耦合，就是将抽象与其实现隔离开来，以便二者独立变化；这种模式对于JavaScript中常见的时间驱动的编程有很大益处，桥梁模式最常见和实际的应用场合之一是时间监听器回调函数。

可能我们平时写代码就用到了桥模式，比如看下面的例子：

    var btn = document.getElementById("btn");
    
    btn.onclick = function(){
	   bridgeHandle();
    }

    function bridgeHandle(){
        var msg = btn.value;
        sendRequest(msg);
    }

    // 这里情况，比如ajax请求
    function sendRequest(msg){
        console.log(msg);
    }

在上面的代码中，bridgeHandle就是一个桥梁，原来的sendRequest应该可以写在onclick事件里面，那么这样的写法有什么好处呢？个人认为有以下几点。

1、 实现解耦：把原来要通过点击来触发的逻辑代码抽离出来，成为一个单独的部分；     
2、方便做单元测试：我们可以单独调用sendRequest方法来测试ajax请求(假设)的返回等是否是我们预期的；   
3、功能模块化，符合现在前端发展的趋势，便于自己和他人维护。


 在桥模式中，还有一个概念叫“特权函数”，我们都知道在面向对象程序设计中，类的私有成员变量或者私有方法是不能被外部访问或者调用的，但是特权函数给我们提供了这一方便的接口。

	function PublicClass(){
	    var name = "张三";
	    //  私有属性
	
	    this.getter = function(){
	        return name;
	    };
	    //  访问私有属性getter,特权函数
	
	    function _privateMethod(){
	        return "我是私有方法！";
	    }
	
	    this.bridgeMethod = function(){
	        return privateMethod();
	    };
	
	}
	
	var class = new PublicClass();
	console.log(class.getter());	//	张三
	console.log(class.bridgeMethod());	//	我是私有方法！

在上面的例子中，PublicClass中有name这个私有成员变量和_privateMethod这个私有方法，如果在没有特权函数的情况下我们是不能访问和调用的的，但是通过模式，我们就完成了对私有成员变量和私有方法的访问和调用。

桥模式也可以把多个单元组织在一起，再看下面：

    function Class1(a,b,c){
        this.a = a;
        this.b = b;
        this.c = c;
    }
    //	第一个class

    function Class2(d,e){
        this.d = d;
        this.e = e;
    }
    //	第二个class

    function BridgeClass(a,b,c,d,e){
        this.class1 = new Class1(a,b,c);
        this.class2 = new Class2(d,e);
    }

假设Class1和Class2都实现了比较复杂的逻辑，在其他一个class中我们需要这个类的实例，这时候，就可以像上面的例子一样，定义一个BridgeClass，传入Class1和Class2需要的参数，分别实例化，作为自己的属性。这样就完成了不同单元的组织。