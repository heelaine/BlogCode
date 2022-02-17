---
title: es6-es12
author: helaine
date: 2022-02-13 19:00:03
tags: ['js','前端']
categories: es6——es12
---
# es6到es12的回顾
{% note info, 这一part主要回顾一下ECMA规范中从es6到es12的变化，包括一些比较常用的方法和api。 %}

## es6
{% title h3, 字面量增强 %}
* 当key和value相同的时候，可以进行简写
* 方法的简写
* 属性名的计算
```
//1.当key和value相同的时候，可以进行简写
var name='123'
var age=12
var obj={
	name,
  age
}

var obj1={
  //2.方法的简写
	foo(){},
  //3.计算属性名
  [name+'123']:'牛啊牛啊'
}
```

{% title h3, 解构 %}
#### 对象解构
```
var obj={
	name:'hhx',
    age:18
}
//=====>
var {name,age}=obj //和数组不同，没有顺序的限制，通过key来查找
var {name:newName}=obj //把原来的name的value赋值给新的newName
```

#### 数组解构
```
var arr=['abc','bca','cab']
//====>
var [item1,item2,item3]=names
var [,itema,itemb]=names //只解构后面两个元素
var [item,...newItem]=names //剩余参数，获取第一个数组和后面的新数组
var [itema,itemb,itemc,itemd='bbb']=names //解构的默认值，itemd在names中不存在，默认为‘bbb’

```

{% title h3, let和const %}
* let和const定义的变量{% pbg yellow,不能重复定义 %}。
* let和const{% pbg yellow , 没有作用域提升 %}，但是let和const的变量在执行上下文创建阶段已经被创建不能被访问，除非被绑定求值。
* var定义全局的时候会定义在{% pbg yellow , window %}里，let和const不会，let和const会被定义在{% pbg yellow, variable map%}里。
* es5没有块级作用域，只有全局作用域和函数作用域。块级作用域对var无效，对let/const/function/class有效。但是不同的浏览器有不同的实现，有些浏览器会让function没有块级作用域。**{} / if / switch / for都有块级作用域**。
    * 全局作用域: 定义在全局的变量的集合。
    * 函数作用域: 定义在函数内部的变量的集合。
    * 块级作用域: es6新声明的，被{}包裹的代码块。**let和const定义的变量会被限制在块级作用域内**。
    * 暂时性死区: 在let和const声明变量之前不能访问变量

```
//eg : 比较常见的面试题
const btn=document.getElementTagNames('button')
for(var i=0;i<btn.length;i++){
	btns[i].onclick=function(){
  	console.log(i)
  }
}
//这个时候打印的i访问的全局作用域，i已经变成最终的数字了
//改良版1.0
const btn=document.getElementTagNames('button')
for(var i=0;i<5;i++){
  (function(n){
  	console.log(n)
  })(i)
}
//打印的时候会找上级作用域就是立即执行函数，立即执行函数把i值穿进去
for(let i=0;i<btn.length;i++){
	btns[i].onclick=function(){
  	console.log(i)
  }
}
//访问的i是块级作用域中的i
```

{% title h3, for of和for of %}
 **for in: 循环只遍历可枚举属性，一般用来遍历对象**
```
var obj = {a:1, b:2, c:3};
    
for (let key in obj) {
  console.log(key);
}
// a
// b
// c
```

**for of: 遍历可迭代对象，一般用来遍历数组**
```
var arr = [4,5,6];
for (let key of arr) {
  console.log(key);
}
// 4
// 5
// 6
```

{% title h3, 字符串模版 %}
* 拼接字符串`${}`
* 在${}中可以进行简单计算，$(age*2)或者在$(change())调用函数
* 标签模版字符串：调用函数的方式
```
function foo(m,n){
	console.log(m,n)
}
const name='hhx'
foo`hello${name}world`
//['hello','world']  hhx  
//第一个参数是模块字符串完整的字符串被切成多块放到数组中
//第二个参数事模版字符串中 第一个${}
```

{% title h3, 参数默认值 %}
```
function foo(m,n){
	//如果没有参数m，n，默认是undefined
  //如果需要默认值 直接让m=‘’ n=‘’
}
function foo(m='',n=''){
	//如果没有参数m，n，默认是undefined
  //如果需要默认值 直接让m=‘’ n=‘’
}
function foo({name='hhx',age=12}={}){
	//解构赋值默认参数
}
//函数的length就是参数的长度，默认值最后放在参数的后面，因为默认值以及后面的参数都不计算在lenght之内
```

{% title h3, 函数的剩余参数 %}
...params:将剩余的参数放到参数中，放到一个数组里面。
{% title h3, 箭头函数的补充 %}
1.没有显示原型
2.没有arguments
3.没有this，会去上层作用域里找
{% title h3, 展开语法 %}
函数调用，数组构造的时候，字面量对象构造的时候
```
const names=['abc','cba','bac']
const name=['why']
//函数调用时
function foo(x,y,z){
	console.log(x,y,z)
}
//1.foo.apply(null,name)
//2.foo(...names) 把names一个一个对应穿进去
//3.foo(...name） 把字符串一个一个切开 w h y
//构造数组时
const newNames=[...names,...name]
//'abc','cba','bac','w','h','y'
//构造字面量对象的时候
const obj={name:'123',age:12}
const info={...obj,address:'555'}
```
{% pbg yellow , ps：展开运算符其实进行的是浅拷贝 %}   


{% title h3 , symbol的基本使用 %}
对象的属性名都是字符串，很可能重复造成属性名的冲突，比如添加新的属性和值的时候不知道内部情况，可能会覆盖掉内部的属性。
利用symbol可以生成一个独一无二的值，symbol函数每次创建出来的值都是独一无二的。
```
const s1=Symbol('描述符')
const s2=Symbol()
//s1不等于s2
//symbol值作为key
const obj={
	[s1]:'abc',
    [s2]:'cba'
}
//1.新增属性
obj[s3]='bac'
//也可以用obj.defineProperty
//2.获取属性值
console.log(obj[s1],obj[s2])
//3.使用symbol作为key的属性名，不能用遍历/obj.keys来获取symbol值,可以用getOwnPropertySymbols
console.log(Object.getOwnPropertySymbols(obj))
//4.用symbol.for(key)的方式创造出来的是相等的
const sa=Symbol.for('aaa')
const sb=Symbol.for('aaa')
console.log(sa===sb)
//5.用symbol.for(key)的方式创造出来的key是相等的
const key=Symbol.keyFor(sa)
const sc=Symbol.for(key)
console.log(sa===sc)
```

{% title h3 , set: 新增的数据类型 %}
常见的属性：
* size 长度
* add(元素)
* delete(元素)
* has(元素)
* clear() :把set清0
* 遍历：forEach 和for of
```
//1.创建set结构
const set = new Set()
set.add(10)
set.add(20)
//2.添加set{10,20}
set.add({})
set.add({})
const obj={}
set.add(obj)
set.add(obj)
//3.上面两个是不同的对象，保存不同的地址,下面两个增加的是同一个对象，指向同一个地址
//4.数组去重，支持展开运算符
const arr=[33,10,26,30,33,26]
const arrSet=new Set(arr)
const newArr=[...arrSet]
console.log(newArr)
```

{% title h3 , weakset：新增的数据类型 %}
* 内部不能重复，只能存放对象类型。
* 弱引用，如果没有其他引用对某个对象进行引用的话，那么gc可以对该对象进行回
```
const weakSet=new WeakSet()
//强引用：set
//弱引用：weakSet
let obj={
	name:'why'
}
weakSet.add(obj)
weakSet.delete(obj)
weakSet.has(obj)
//weakSet不能遍历，存储到weaSet的对象是没办法获取的

//weakSet的运用场景
const personSet=new WeakSet()
class Person{
	construtor(){
  	personSet.add(this)
  }
  running(){
    if(!personSet.has(this)){
    	throw new Error('不能通过非构造方法创建的对象调用running方法')
    }
      console.log('running',this)
   }
}
const p=new Person()
p.running()
p.running.call({name:'why'})
//running Person{}
//running {name:'why'}
//如果不希望通过call或者其他方法来进行调用，可以用weakSet

```

{% title h3 , map：存储键值对 %}
**之前的键只能用字符串表示，可以用map把对象作为key**
```
const obj1={name:'111'}
const obj2={name:'222'}
const map=new Map()
map.set(obj1,'aaa')
map.set(obj2,'bbb')

//map构造方法
const map2=new Map([[obj1,'aaa'],[obj2,'bbb']])
//[key,value]

//常见的map方法
map.size
map.set('key','value')
map.get(map.get('key')) //查看对应属性的值
map.has('key') //判断是否有属性
map.delete('key') 
map.clear()
//支持for of和forEach遍历
for(const [key,value] of map){
	console.log(key,value)
}
```

{% title h3 , weakMap:新增数据类型 %}
* 弱引用
* key只能是对象，不能使用基本数据类型，以键值对存在
* 不能进行遍历
```
//常见方法
weakMap.set()
weakMap.get()
weakMap.has()
weakMap.delete()

//应用场景vue3响应式原理
const obj1={
	name:'why',
  age:18
}
const obj2={
	name:'why',
  sex:boy
}
function obj1NameFun1(){}
function obj1NameFun2(){}
function obj2NameFun1(){}
function obj2NameFun2(){}
//对象属性发生改变——》改变template——》重新执行render函数——》diff算法——〉渲染
//最重要的就是把这函数方法和对象关联起来
//1.创建weakMap
const weakMap=new WeakMap()

//2.收集依赖结构
//2.1对obj1手机数据结构
const obj1Map=new Map()
obj1Map.set('name',[obj1NameFun1,obj1NameFun2])
weakMap.set(obj1,obj1Map)

//2.2对obj2收集数据结构
const obj2Map=new Map()
obj2Map.set('name',[obj2NameFun1,obj2NameFun2])
weakMap.set(obj2,obj2Map)

//如果obj.name发生改变，可以通过proxy，defineProperty来监听
obj1.name='james'
const targetMap=weakMap.get(obj1)
const fns=targetMap.get('name')
fns.forEach(item=>item())
```

## es7
{% title h3 , includes %}
array：includes
* 可以判断是否有NaN
* 结果是布尔值

{% title h3 , * * %}  

**指数运算方法：3* *3 //27**

## es8
{% title h3 , 对象方法  %}
* Object.keys() :**可以获取对象的属性**
* Object.values() :**可以获取对象的属性值**
也可以传入数组，字符串，传入的字符串会解析成一个一个字母在一个新数组中
* object.entries() :**可以获取到一个数组，数组中会存放可每枚举属性的键值对数组**

```
const obj={
	name:'hhx',
  age:12
}
console.log(Object.entries(obj))
//1.传入的是对象
//[['name','hhx'],['age',12]]
//可以通过forEach遍历的方式获取数组每一项进行操作

//2.传入的是数组
console.log(Object.entries(['123','456']))
//[['0','123'],['1','456']]
```

{% title h3 , String Padding %}
**String Padding :字符串填充**
```
//1.padStart
const message ='hello'
//长度填充到几位，没有第二个参数就是默认空格填充
message.padStart(15,'*')
//2.padEnd
message.padEnd(20,'-')

//使用场景
const cardNumber='1234123412341234123'
const lastNumber=cardNumber.slice(-4)
lastNumber.padStart(cardNumber.length,'*')
```

{% title h3 , Object.getOwnPropertyDescriptors  %}
Object.getOwnPropertyDescriptors ：获取对象所有的属性描述符

## es9
* async 
* iterators 
* 展开运算符 
* promise finally

## es10
{% title h3 , flat和flatMap  %}

    * flat 降维数组 
    * flatMap 常应用于函数式编程

```
//flat
const nums=[10,20,[2,1],90,[20,[10,6]],3]
nums.flat()  //默认情况下降维深度是1

//flatMap，使用映射函数map映射每个元素，将结果压缩成一个新数组
const nums2=[10,20,30]
const nums3=nums2.flatMap(item => {
	return item*2 
})
//和map结果一样
//应用场景
const message=['hello world','hello hhx','hello 123']
const words=message.flatMap(item =>{
	return item.split('')
})
console.log(words)
```

{% title h3 , Object.fromEntries()  %}
**把entries或者可迭代格式的重新变成obj格式**

```
//应用场景
const queryString='name=why&age=10&address=nqwer'
const queryParams=new URLSearchParams(queryString)
console.log(queryParams)

for (const params of queryParams){
	console.log(param)
} //遍历queryParams
const paramObj=Object.fromEntries(queryParams)
console.log(paramObj)
```

{% title h3 , trimStart和trimEnd  %}
```
const message='    iaf  '
console.log(message.trim())  //	去除全部空格
console.log(message.trimStart())  //去除头部空格
console.log(message.trimEnd())  //去除尾部空格
```

## es11
{% title h3, BigInt %}
**BigInt:之前只能表示Number.MAX_SAFE_INTEGER大小的数字,超过大小就会导致运算出错，es10引入BigInt进行大数计算**

```
const bigInt=428938751743923851058n
//末尾加上n表示是一个大数，进行其他操作的时候其他数也需要加上n
console.log(bigInt+10n)

const num=100
console.log(bigInt+BigInt(num))

//把大数转化成number类型
const smallNum=Number(bigInt)
console.log(smallNum)
```

{% title h3, 空值合并运算 %}
**空值合并运算: ??**
```
const foo
const bar=foo || 'default value'
//||的弊端 会把0，‘’当作空置
const bar=foo ?? 'default value' 
//??会明确只判断foo是不是undefined或者null
```

{% title h3, 可选链 %}
**可选链：判断对象中的属性是否存在**
```
const obj={
	name:'hhx',
  friends:{
  	name1:'lilei',
    name2:'hanmeimei'
  }
}

console.log(obj?.friend?.name)
```

{% title h3, 其他 %}
* 全局对象
  * 浏览器：window
  * node：global
  * es11新增了关键字：globalThis，在不同的环境下都可以获取全局对象
* for in标准化 for key in items 
* Dynamic Import 动态导入
* Promise.allSettled
* import meta

## es12

{% title h3 , 监听对象的销毁过程 %}
```
const finalRegistry=new FinalizationRegistry((value)=>{
	console.log("注册的finalRegistry对象被销毁",value)
})
let obj={name:'hhx'}
//注册
finalRegistry.register(obj,'可以绑定一些值')
obj=null
//gc是不定时销毁垃圾的，所以该finalRegistry函数不会立即执行

//weakRef
```

{% title h3 , 逻辑赋值运算 %}
* !!= 逻辑或
* &&= 逻辑与
* ??= 逻辑空
```
let message	= undefined
message = message||'default'
message ||='default value'

let info	={
	name:'hhx'
}
info=info&&info.name
info &&= info.name

let message = ''
message ??='default'
```

