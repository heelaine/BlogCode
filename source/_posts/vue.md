---
title: vue
author: helaine
date: 2022-02-13 23:44:48
tags: ['前端', 'vue', '面试']
categories: vue
---

{% note info, 这一part主要涉及到vue的基本面试问题 %}
{% title h2 , render和template %}

**render和template的区别**
* Render是一种编译方式，render中有一个函数h，这个h是createElement方法，会把单文件组件进行虚拟dom的创建，然后通过render进行解析
* Template也是一种编译方法，但是template最终还是会通过render的方式进行编译，render的性能比template性能好，且render的函数优先级大于template render会先执行。
{% u 在vue源码中，template会被转化成render函数。 %} 

{% title h2 , v-if/v-show %}

* **v-if和v-show**
    * v-if 通过控制dom节点存在控制元素的显隐  v-show通过设置dom的display样式
    * v-if 是真正的条件渲染 切换的时候有一个局部编译和卸载的过程，会销毁和重建内部的事件监听和子组件 v-show只是简单的css切换
    * v-if是惰性的 如果初始条件为假 就什么也不做 如果条件为真才会进行局部编译 编译被缓存之后 切换的时候会局部卸载 v-show不管怎么样都会被编译 然后被缓存 保留dom元素
    * v-if支持template语法 v-show不支持template语法
    * 如果频繁切换用v-show v-if有更高的切换消耗 v-show有更高的初始渲染消耗
* **v-if和v-for**
    * v-if和v-for不推荐一起使用，因为v-for的优先级比v-if高，v-if会重复运行到每个v-for循环中，对每个列表进行条件渲染，可以部分渲染节点，但是不适合跳过循环的执行。
    **解决办法**：可以把v-if添加到父级元素上或者通过计算属性过滤掉不需要的某一项。

{% title h2 , computed %}

{% pbg yellow, Computed %}是计算属性， 计算一个新属性并将该属性挂在到实例上， 要引用的地方像data一样引用就行。可以不在data里声明，直接在template中绑定，默认挂载在data里。
**注意事项**
* 支持缓存 当属性内数据发生变化才会重新计算 性能开销比较小
* 不支持异步操作
* 基于data或者父组件prop中通过计算得到的属性
* 结果必须return出来
* 初始化就可以被检测到并且进行计算
* 默认走get方法 数据变化时调动set方法

**使用场景** :一个属性依赖于其他属性用computed，负责简单的逻辑。

```
//eg 
<script>
export default{
	computed:{
    formData(){
    	get(){
      	return this.value
      }
      set(obj){
      	this.value=obj
      }
    },
    getChange(){
    	return xxx
    }
  }
}
```

{% title h2 , watch %}

{% pbg yellow, Watch %}是监听属性，监听数据的新旧变化。
**注意事项**c
* 不支持缓存，数据发生变化就会直接发生变化
* 支持异步操作
* 函数接受两个参数 一个new一个old
* 监听的属性必须是data里声明的或者父组件传递的数据

**使用场景**： 一条数据会影响到多条数据的时候 需要异步操作的时候 负责复杂逻辑的时候
Watch还有很多属性默认使用的handler函数
```
//eg 
<script>
export default{
	watch:{
   	name:{
    	handler(newName, oldName) {
      this.fullName = newName + ' ' + this.lastName;
    },
      immediate: true,
      //
      deep:true
    }
    //或者
    'name.firstName':{
    	handler(newName, oldName) {
      this.fullName = newName + ' ' + this.lastName;
    },
      immediate: true,
      //
      deep:true
  	}
  }
}
</script>
```
* immediate属性： 由于watch一开始绑定的时候不会执行，只有数据发生变化的时候才会执行 如果想要在绑定的时候就执行 可以设置这个属性 默认false，比如当父组件给子组件动态传值的时候子组件props首次获取父组件传来的默认值也需要执行监听函数的时候，组件创建时立即执行。
* deep属性：{% pbg yellow , 深度监听 %}。当对对象进行数据监听的时候，并不会监听到对象内部属性的变化，属性变化不会触发对象的监听函数，但是设置deep的时候监听起会层层往下遍历给对象的所有属性都加上监视器，导致性能开销很大可以通过对对象的某个属性设置监听。

{% title h2 , Vue 插槽 %}
什么是插槽？
* 插槽就是用slot包裹的标签。  <slot></slot>

什么时候用插槽？
* 开发中，需要把相同的公共标签抽出来封装成公共组件，然后在不同的模块引入，但是有时候，每个模块的需求不同，对组件样式的需求也不一样，可能需要我们在组件内部进行改写，但是如果组件内部被改写，那么引入该组件的模块都会被改写，如果我们只想改写单独模块内的组件，可以使用“插槽”。


```
//eg
<button type="submit">
  确认
</button>
```

* **默认插槽**
    * 当我们想要组件内部大部分都渲染确认，单独组件渲染其他文本的时候，我们可以使用默认插槽。不提供插槽内容就默认渲染原来的内容，提供插槽内容就渲染提供的数据。

```
 <!-- 公共组件 submit-button -->
<button type="submit">
  <slot>submit</slot>
</button>

 <!-- 单独模块 -->
<submit-button></submit-button> 这个时候会默认渲染submit
<submit-button>save</submit-button> 提供内容就会渲染成save

1.
 <!-- 插槽可以插入什么 -->
<button type="submit">
  <slot></slot>
</button>  如果插槽内什么都不写就会直接渲染单独模块加的内容

 <!-- 单独模块 -->
<submit-button>
	hello world
</submit-button> 直接渲染hello world的按钮

2.
 <!-- 插槽可以添加html模版代码 可以添加其他组件 公共组件插槽可以获取到单独模块的内容 -->
<submit-button>
  <span>我是span标签</span>
  你好
</submit-button>

<submit-button>
  <table></table> 引入table组件
  你好
</submit-button>

<submit-button>
  你好 {{data}}
</submit-button>  可以渲染模版内的数据
```

* 如果公共组件内部没有写slot，那么在模块内改写组件内部内容会被直接抛弃
* 父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。
* 具名插槽
    * 我们有时候会在一个组件内使用多个插槽，为了渲染不同的插槽效果，可以给他们命名，通过name。

```
<!--公共组件 -->
<template lang="pug">
    .wrap
        button 
            slot 保存
        .header
            slot(name='header')
        .main
            slot(name='main')
        .footer
            slot(name='footer')
</template>

<!--单独模块 -->
<template lang="pug">
  			<!--如果没有写会默认写入没有name的插槽里 -->
        submit-button 确认  
            template(v-slot:header) 这是页头
            template(v-slot:main) 这是内容
            template(v-slot:footer) 这是页脚
</template>
```
**v-slot只能添加在template上，具名插槽可以使用缩写，用#来代替**

* 作用域插槽
    * 单独模块需要访问公共模块里的数据，但是每个模版的内容都只在自己的作用域里编译，这歌时候我们一样让数据作为slot的一个属性进行绑定。

```
<!--单独模块 -->
<template lang="pug">
    .wrap
        .header
            slot(name='header')
        .main
            slot(name='main')
        .footer
            slot(name='footer')
        .content(:content='content' name='div') {{content}}
</template>
<script>
  export default {
    data(){
    	return{
        data:{
          content:'这是公共组件的内容',
          title:'这是公共组件的标题'
        }
      	
      }
    }
  }
</script>
```

**想要拿到单独模块里的数据 需要绑定到v-bind:content='content'**

```
<!--公共组件 -->
<template lang="pug">
	 template(v-slot:default='slotProps') {{slotProps.content}}
</template>
```
在template定义需要拿到哪个插槽的数据，通过v-slot:name='slotProps'进行定义(v-slot="slotProps") 数据都放在slotProps里

当然也有简单写法：解构赋值
```
<!--公共组件 -->
<template lang="pug">
	 template(v-slot:div='{data}') {{data.title}} {{data.content}}
</template>
or更简写
<!--公共组件 -->
<template lang="pug">
	 template(#div='{data}') {{data.title}} {{data.content}}
</template>
```
但是不能template(#='{data}') {{data.title}} {{data.content}} ，因为#的后面必须要求参数



