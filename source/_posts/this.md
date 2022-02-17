---
title: 认识this并手写call，apply和bind
author: helaine
date: 2022-02-10 22:00:22
tags: ['前端','面试']
categories: this相关
---

{% title h2, 认识this %}
{% note info,  在全局作用域中，不同的环境的this有不同的指向。 %}
    浏览器——>window;
    node——>{}
**ps**:在node环境中，每个js脚本都被当成一个module,module——>加载——>编译——>找到一个函数——>执行这个函数.call({})
* this是执行上下文中的一条记录。
* this是动态绑定的，在函数执行的时候才绑定。
* this指向跟函数所处的位置没有关系，跟函数被调用的方式有关。

{% title h2, this的绑定规则 %}
**1.默认绑定？函数独立调用的时候！**
{% pbg yellow, 指向全局作用域 %}
```
//案例一
function foo1(){
    console.log(this);
}
function foo2(){
    console.log(this)
    foo1()
}
function foo3(){
    console.log(this)
    foo2()
}
foo3()
//三个window对象

//案例二
var obj = {
  name: "hhx",
  foo: function () {
    console.log(this);
  },
};
var bar = obj.foo;
bar();
//window对象

//案例三
function foo(){
	console.log(this)
}
var obj = {
  name: "hhx",
  foo: foo
};
var bar = obj.foo;
bar();
//window对象

//案例五
function foo(){
	function bar(){
  	console.log(this)
  }
  return bar
}
var fn=foo()
fn() 
//window对象
```
**2.隐式绑定？通过对象调用！**
{% note warning, 必须在调用的对象内部有一个对函数的引用，没有引用的话，在调用的时候，会报找不到该函数的错误。 %}
eg：obj.fn()  
```
var obj={
	foo:function(){
  	console.log(this.name)
  }
}
var obj2={
	name:'obj2',
  bar:obj.foo
}
obj2.bar()
//打印的是name:obj2
```
**3.显式绑定？call apply bind**
{% pbg yellow, foo直接调用和call/apply调用的不同在于this的绑定不同foo %},{% pbg yellow, 直接调用指向的是全局对象 %}

```
function foo(){
	console.log('函数被调用了',this.name)
}
var obj={
	name:'hhx'
}
foo.call(obj)
foo.apply(obj)
//hhx
//call和apply在执行函数的时候可以指定this的绑定对象，显式绑定

//call、apply的区别
//call传参的时候用逗号隔开
//apply通过数组来传参数

function foo(){
	console.log(this)
}

//foo.call('a')
var newFoo=foo.bind('a')
newFoo()  
//通过bind绑定this到字符串a上，会产生一个新的函数需要调用一下。
//默认绑定和显示绑定冲突的时候，显示绑定的优先级更高。
```
**4.new绑定？使用new关键字！**
1.创建一个新对象
2.新对象执行prototype连接
3.把新对象绑定到函数调用的this上
4.如果函数没有返回其他对象，表达式会返回这个新对象
```
//通过new关键字构造一个函数，这个时候this是在调用构造器时创造出来的对象
function Person(name,age){
		this.name=name
  	this.age=age
}
var p1=new Person('hhx',18)
console.log(p1.name,p1.age)
```

{% title h2, this和内置函数的关系 %}
```
//案例一
setTimeout(function(){
	console.log(this)
},2000)
//这里面的this指向window

//案例二 监听点击事件
box.onclick=function(){
	console.log(this)
}
//相当于 box.onclick()
//打印结果是box这个元素

box.addEventListener('click',function(){
	console.log(this)
})
//相当于显示绑定 call 打印结果是box这个元素

//案例三
var team=['abc','dem']
team.forEach(function(){
		console.log(this)
},'123')
//结果 ‘123’ ‘123’
//有些函数内部会有参数可以让你去绑定相对应的this
//如果默认不写绑定的就是全局window对象
```
{% title h2, this绑定规则的优先级 %}
1.独立调用优先级最低。
2.显示绑定的优先级高于隐式绑定，包括apply，call，bind。
3.new绑定优先级高于隐式绑定。
4.new的优先级高于显示绑定，new关键字不能和apply，call一起使用。
{% pbg yellow, 例子 %}
```
function foo(){
	console.log(this)
}
var bar=foo.bind('aaa')
var obj=new bar()
console.log(obj)
//打印结果是foo()
```
{% note warning, 有些语法超过我们的规则之外 %}
**1.忽略显示绑定**
```
function foo(){
	console.log(this)
}
foo.apply(null)
foo.apply(undefined)
//如果显示绑定绑定的是null，undefined的时候，会自动将this绑定成全局对象。
```
**2.间接函数引用**
```
var obj1={
	foo:function(){
  	console.log(this)
  }
}

var obj2 = {
  name:'obj2'
};
(obj2.bar=obj1.foo)()
//在这里认为是独立函数的运用==foo()
//指向window
```
{% pbg yellow, ps如果一行没有加分号 %},{% pbg yellow, 意味着代码还没有结束 %},{% pbg yellow, 会跟下面的代码连起来一起执行 %}。
```
function foo(el){
	console.log(el,this.id)
}

var obj={
	id:'hhx'
}
[1,2,3].forEach(foo,obj)
//会直接报错
//需要添加括号或者把123数组赋值给一个新数组 var nums=[1，2，3]
//结果是1 hhx 2 hhx 3 hhx
```
**3.箭头函数**
&nbsp;&nbsp;&nbsp;&nbsp;3.1 箭头函数不能绑定{% pbg yellow,this %}，{% pbg yellow,arguments %}，{% pbg yellow,不能使用构造函数（new） %}。
&nbsp;&nbsp;&nbsp;&nbsp;3.2 简写规则：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果一个箭头函数，只有一行代码，并且返回一个对象？怎么简写
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;var bar=() =>  ({ name:'hhx', age : '13'})  利用小括号～
&nbsp;&nbsp;&nbsp;&nbsp;3.3 箭头函数的this，根据外层作用域来查找this。
```
//定义对象的时候并没有定义作用域,定义函数的时候会有作用域
var name='hhx'
var obj={
  name:'123'
	foo:()=> console.log(this.name)
	foo1:function(){
  	return function(){
    	console.log(this.name)
    }
  }
	foo2:function(){
  	return ()=>{
    	console.log(this.name)
    }
  }
}
var obj2={name:'456'}
obj.foo() // hhx 打印的是window
obj.foo1()() //hhx 打印的是window 把它放做独立函数调用，因为相当于里面的函数单独调用执行
obj.foo2()() //123 箭头函数不绑定this，上级作用域的this是obj
obj.foo2.call(obj2)() //456 上层作用域绑定了一个obj2
obj.foo2().call(obj2) //123 上层作用域找到obj
//this 1:52:25
```

{% title h2, 手写绑定this函数 %}
{% title h3, 实现call %}
**实现call思路**
1.首先要获取到需要被执行的函数
2.对thisArg转换成对象类型 ，防止传入的是非对象类型，null和undefined的话默认为window
3.对额外参数进行接收并处理
4.调用需要被执行的函数
5.将最终的结果返回出去
```
Function.prototype.selfCall=function(thisArg,...args){
    //首先要获取到需要被执行的函数
    //通过this来获取函数
    var fn=this //this指向被调用的函数
    //调用被执行函数
    //fn()
    thisArg = thisArg ? Object(thisArg) : window
    thisArg.fn=fn
    var result=thisArg.fn(...args)
    //如果直接执行是默认调用 会把this绑定到window上，所以可以通过中间值把函数绑定到thisArg上
    //会导致对象上多出一个fn属性
    delete thisArg.fn
    //上面的...args是对参数的简写，表示除第一个参数外其他的所有参数
    //下面的...args是值扩展运算符，就不用一一传入参数内容
    return result
}
function foo(){
	console.log('执行了foo函数',this)
}


//默认进行了隐式绑定
foo.selfCall()
```

{% title h3, 实现apply %}
**实现思路和call一样**
```
Function.prototype.selfApply=function(thisArg,args){
	//因为apply传入的是数组，只有一个参数，所以不需要。。。
    //在call用的是...args,默认没有参数，该args为空[]
    var fn=this
    thisArg =
        thisArg !== null && thisArg !== undefined ? Object(thisArg) : window;
    //进行优化，如果传入的是0
    thisArg.fn=fn
    //需要对传进来的参数进行判断
    args=args||[]
    var result=thisArg.fn(args)
    delete thisArg.fn
    return result
}

function foo(){
	console.log('执行了foo函数',this)
}

foo.selfApply()
```

{% title h3, 实现bind %}
```
Function.prototype.selfBind=function(thisArg,...args){
	//1.获取到真实需要调用的函数
    var fn=this
    //2.绑定this
    thisArg =
        thisArg !== null && thisArg !== undefined ? Object(thisArg) : window;
    return function(...arg1){
        //3.将函数放到thisArg中调用
        thisArg.fn=fn
        //4.对两个传入的参数进行合并
        var finalArgs=[...args,...arg1]
        var result=thisArg.fn(...finalArgs)
        delete thisArg.fn
        //5.返回结果
        return result
  }
}
function foo(){
	console.log('执行了foo函数',this)
}
var bar=foo.selfBind()
bar()

```

