---
title: thinking
author: helaine
date: 2022-02-18 21:32:15
tags: ['前端','vue']
categories: 前端组件化，模块化，工程化
---

# 前端组件化、模块化、工程化
{% note info, 这一part主要关于前端组件化，模块化和工程化思想。 %}

## 前端组件化
**vue封装公共组件**
一个大型项目里往往会复用到很多公共组件，比如弹窗，卡片，空状态展示和表单等等，所以如何封装公共组件是非常重要的，公共组件应该具备高性能，低耦合的特性，其实就是尽量减少公共组件的副作用，让他不要影响到需要引用到公共组件的组件。
**耦合**：理解为就是组件之间的连接。
**什么是低耦合**：低耦合就是要求在我们的软件系统中，某元素不要过度依赖于其它元素，在这里就体现在组件之间的连接减少，但并不是完全没有，因为单独模块的开发还是要依赖于公共组件，尽量不要让单独模块的功能写在公共组件内。
**封装公共组件三大要素**：
* props参数
	* 每个公共组件的应用场景不同，当父组件对公共组件传递参数，用props的时候最好添加验证规则。公共组件最好不要生成数据，生成了也只能自己内部使用，减少对父组件的影响。
* 自定义事件
	* 将这些事件的处理方法都放到父组件中，公共组件只作为中转站。
* slot插槽
	* 在公共组件留一个slot插槽，因为公共组件虽然应用场景很多，但是可能会有细微差别，我们可以自定义插槽，对一些特殊地方可以进行单独编写。


**其他可选要素**：     
		在编写组件的时候，可以在 `<style>`标签中添加 scoped，让标签中的样式只对当前组件生效，但是一味的使用 scoped，肯定会产生大量的重复代码，所以在开发的时候，应该避免在组件中写样式，当全局样式写好之后，再针对每个组件，通过 scoped 属性添加组件样式。

## 前端模块化

{% title h3, 为什么需要模块化 %}

在js的演变过程中，js需要处理越来越复杂的逻辑，比如ajax的出现，spa页面，node的实现，nodejs可以编写复杂的后端程序，由于之前js语言设计的不全面，存在很多弊端，不能很好的处理越来越复杂的需求。
模块化的发展历程
* 通过sctipt脚本引入。会产生以下问题：
  * 通过函数封装，会污染全局变量，可能会和别的模块产生变量名冲突，项目复杂的时候维护成本也很高。
* 通过对象封装。
  * 可以把模块封装在一个对象内，调用模块引用对应的文件，解决变量名冲突，但是内部可以被外部改写。
* 通过立即执行函数。
  * 将模块封装成立即执行函数，通过在函数内部返回值暴露给外部。模块外部不能修改我们没有暴露的变量和函数，但是我们必须知道每个模块返回的对象的命名，没有统一规范，可能会任意命名，模块名称相同等等。


{% title h3, 什么是模块化 %}
* 事实上模块化开发最终的目的是将程序划分成一个个小的结构；
* 这个结构中编写属于自己的逻辑代码，有自己的作用域，不会影响到其他的结构；
* 这个结构可以将自己希望暴露的变量、函数、对象等导出给其结构使用；
* 也可以通过某种方式，导入另外结构中的变量、函数、对象等；
**这种结构就是模块**

{% title h3, CommonJs 规范 %}

**node**——commonjs在服务端的一个代表
在node中，每个js文件就是一个独立的模块，有自己的作用域，每个模块只有暴露自己的变量函数对象，才能被其他模块访问。
* 模块成员的导出：exports，module.exports
* 模块成员的导入：require

#### exports/module.exports
exports是一个对象，可以在exports中添加很多属性，属性会被暴露可以访问。
在CommonJs中没有module.exports的概念，node通过Module的类来实现模块，每个模块都是Module的实例——module。在node中，真正用于导出的是module.exports。
```
let exports={
}

let module={
	exports:exports
}
//module对象的exports属性是exports对象的一个引用。
//所以如果module.exports和exports同时存在，以module.exports的值为准
```

事实上module.exports=exports 
module还有很多其他的属性
```
module.id	           //带有绝对路径的模块文件名
module.filename      //模块的文件名，带有绝对路径
module.loaded	       //表示模块是否已经完成加载
module.parent		     //返回一个对象，表示调用该模块的模块。
module.children      //返回一个数组，表示该模块要用到的其他模块。
```
#### require
**require加载顺序**
CommonJs是同步加载的。加载的顺序和代码出现的顺序有关，require第一次加载模块会执行整个文件的js代码，返回接口数据，在内存生成该模块对应的module对象，模块被多次引入的时候，commonjs只会在第一次加载的时候运行。这是因为每个模块对象module都有一个属性：loaded。为false表示还没有加载，为true表示已经加载。以后再加载会从缓存中取出第一次加载生成的module对象，除非手动清除。

**require查找规则** [链接](https://nodejs.org/dist/latest-v14.x/docs/api/modules.html#modules_all_together)
![require加载流程](1.jpg)

{% title h3, CommonJs规范缺点 %}
common加载模块是同步的，只有模块加载完毕才能被运行，在服务器端没有问题，但是在浏览器会有很多问题。
* 浏览器加载文件需要先从服务器下载文件在加载运行。
* 同步的代码加载可能会导致后续的模块无法运行，导致页面假死。
所以在浏览器中我们是用另外的规范
**——AMD，异步模块定义。**
AMD 是提前执行，在定义模块的时候，就熬声明其依赖的模块。
**——CMD，通用模块定义。**
CMD 是延迟执行，用到某个模块的，再去require。 

{% title h3, ES Module %}
浏览器和服务器通用的模块方案。
ES module设计思想是静态，在编译的时候就确定模块的依赖关系，以及输入输出的变量。
* 编译加载效率比Commonjs模块方式高
* 静态分析，可以拓宽js语法，引入类型检验通过静态分析实现的功能。
**es6自动启用严格模式，顶层this指向undefined。**
浏览器加载ES module：默认情况下浏览器可以同步加载js脚本，也可以添加defer和async属性进行异步加载。
浏览器加载ES module，需要添加type。
`<script src="main.js" type="module"></script>`

#### export导出

```
//1.直接在想导出的对象前面加export关键字
export const name='123'
export function foo(){}

//2.将想导出的内容放入export的{}中
export {
	name,
	function
}

//3.导出的时候可以添加标识符
export {
	name as fName,
	foo as fFoo
}
```

#### import导入
import导入模块不可以被放入逻辑代码中，但是如果需要动态加载模块，可以使用import()函数。
```
//1.import {标识符} from '模块'
import {name,foo} from './modules/index.js'

//2.导入时给标识符起名字
import {name as fName,foo as fFoo} from './modules/index.js'

//3.将模块功能放到一个模块功能对象上
import * as foo from './modules/index.js'
console.log(foo.name)
console.log(foo.age)
```

#### export 和import 结合
**a模块的内容被b模块导入后直接导出**
```
//b文件，a的模块导入后直接导出
export { a的内容 } from './a.js';
//导出的时候可以改变名字
export { a的内容 as a的别名 } from './a.js';

//c文件需要引入b文件中a的模块
import { a的内容 } from './b.js';
```

{% pbg yellow , 原因%}：
* 在开发和封装一个功能库时，通常我们希望将暴露的所有接口放到一个文件中；
* 这样方便指定统一的接口规范，也方便阅读；
* 这个时候，我们就可以使用export和import结合使用；

#### default默认导出
export default的本质就是输出一个叫做default的变量或方法
* 默认导出export不需要指定名字
* 导入时不需要使用{},也可以自己指定名字
* 一个模块内默认只有一个默认导出

{% title h3, ES module和CommonJs的区别 %}
|CommonJs|ES module|
|Common运行时加载，并且同步|ES module编译（解析）时加载的，并且异步|
|CommonJs通过module.exports导出对象，一旦一个变量修改对象属性，其他地方都会修改	|ESmodule通过export导出的是变量本身的值的引用|

* 运行时加载意味着是js引擎在 执行js代码的过程中 加载模块；所以require可以与变量、表达式等运行时代码结合使用
* 编译时（解析）时加载，意味着import不能和运行时相关的内容放在一起使用：
  * 比如from后面的路径需要动态获取；
  * 比如不能将import放到if等语句的代码块中；
  * 所以我们有时候也称ES Module是静态解析的，而不是动态或者运行时解析的；
参考来源: [链接一](https://mp.weixin.qq.com/s?__biz=Mzg5MDAzNzkwNA==&mid=2247484285&idx=1&sn=bc37c00b5c58ff42355b1b01ad9b9c0d&chksm=cfe3f082f8947994b5475d365c4460ac0d4b36d6eec68b5dd7c15fc3a0ef1b88f33b776f3f05&scene=178&cur_album_id=1567316742547226629#rd)，[链接二](https://tenloy.github.io/2021/06/18/Web-Module.html)


