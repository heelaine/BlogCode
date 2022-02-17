---
title: promise
author: helaine
date: 2022-02-13 01:13:47
tags: ['前端','js','面试']
categories: ['promise']
---

{% note info, 这一part主要关于js中的promise，包括promise的基本api和用法，还有如何手写一个promise %}

# Promise
当我们进行网络请求的时候，一般进行的都是异步操作，封装的请求函数如果想要拿到请求结果，不能用return，因为异步操作获取不到结果，之前我们经常使用回调函数来进行解决。
```
//伪代码
function requestData(url,successCb,failureCb){
  setTimeout(()=>{
  	if(){
      //成功,就走成功的回调
      successCb()
     }else{
     	//失败，就走失败的回调
       failureCb()
     }
  },3000)
}

requestData('hhx',(res)=>{
	成功的处理
},(res)=>{
	失败的处理
})
```
{% title h2, 回调函数的弊端 %}
* 如果是我们自己封装的请求的函数，要自己设计成功回调和失败回调
* 如果使用的是别人封装和第三方库，必须看别人的源码才能知道使用方法
—————所以这个时候就诞生了**promise**
1.promise规范好了所有的代码编写逻辑，它会返回一个promise，可以直接使用。
2.promise是一个类，当返回回调数据的时候，就可以创建一个promise对象，通过new创建promise对象，需要传入一个回调函数，称之为{% pbg yellow, executor %}，异步请求代码会被放入{% pbg yellow, executor %}中。
* 这个回调函数会被立即执行，并且传入另外两个回调函数resolve，reject
* 当调用resolve，执行promise对象的**then方法**传入的回调函数
* 当调用reject，执行promise对象的**catch**方法传入的回调函数

**三个状态，状态一旦确定就无法更改。**
```
//伪代码
function foo(){
	return new Promise((resolve,reject)=>{
    //进行异步请求的时候在这里面写，比如网络请求
    //resolve()
  	reject()
  })
}
const fooPromise=foo()
//then方法传入的回调函数，会在promise执行resolve函数时，被回调
fooPromise.then(()=>{})
//catch方法传入的回调函数，会在promise执行reject函数时，被回调
fooPromise.catch(()=>{})

//then方法可以传入两个回调，一个成功一个失败

//模拟网络请求
function foo(url){
	return new Promise((resolve,reject)=>{
    setTimeout((url)=>{
  	if(url==='hhx'){
      //成功,就走成功的回调
      let name=['123','456']
      resolve(name)
     }else{
     	//失败，就走失败的回调
       let err='请求失败'
       reject(err)
     }
  },3000)
  	reject()
  })
}
const fooPromise=foo()

fooPromise.then((res)=>{
  console.log(res)
},(err)=>{
	console.log(err)
})

```
{% title h2, resolve的详解 %}
**resolve参数**
1.普通的值或者对象
2.传入一个promise，那么当前的promise的状态会由当前传入的promise来决定，相当于状态进行了移交
3.如果传入一个对象，并且这个对象有个then方法，会直接执行then方法，状态由then来改变
```
const newPromise =new Promise((resolve,reject)=>{

})

//传入一个promise
new Promise((resolve,reject)=>{
  
	resolve(newPromise)
}).then(()=>{},()=>{})

//传入一个对象，并且这个对象有then方法
new Promise((resolve,reject)=>{
  const obj={
  	then:function(resolve,reject){
    	resolve()
    }
  }
	resolve(obj)
}).then((res)=>{
	console.log(res)
},(err)=>{
	console.log(err)
})
```

{% title h2, promise对象方法 %}
{% title h3, then方法 %}
{% pbg yellow ,promise.then() %}
    对象的方法，其实是放在**Promise.prototype.then()**
1.同一个promise可以多次调用then方法，当我们的resolve方法被回调的时候，所有的then方法传入的回调函数都会被调用
2.then方法传入的回调函数可以有返回值，{% pbg yellow, 他的返回值是新的promise %}——promise链式调用的实质     
    2.1如果返回普通值，那么普通的值会被作为一个新的promise的resolve值，做对应的回调，如果没有返回默认返回值是undefined     
    2.2如果返回promise，那么返回的promise的状态会决定下一个then里面的状态     
    2.3如果返回一个对象，并且该对象实现了thenable，那么thenable里面状态会决定下一个then里面的状态
```
const promise =new Promise((resolve,reject)=>{
	resolve('hahaha')
})

promise.then(res=>{
	console.log(res)
})
promise.then(res=>{
	console.log(res)
})
//hahaha hahaha

promise.then(res=>{
  return 'aaa'
}).then(res=>{
	console.log(res)
})
//aaa

promise.then(res=>{
  return {
  	then:function(resolve,reject){
    	resolve(1111)
    }
  }
}).then(res=>{
	console.log(res)
})
//1111
```
{% title h3, catch方法 %}
**catch方法的本质就是then方法的语法糖。**
then方法有两个参数，第一个是成功的回调，第二个是失败的回调，catch相当于执行第二个参数的回调
1.当executor(主函数)抛出异常的时候，也是会调用错误捕获的回调函数
2.通过catch方法来传入错误捕获的回调函数
3.拒绝捕获的问题
4.catch方法的返回值也是一个promise
```
const promise=new Promise((resolve,reject)=>{
	reject('请求失败')
  //throw new Error('请求失败')
})

//1.
promise.then(undefined,(err)=>{
	console.log(err)
  //当抛出错误的时候，err就是错误信息
})

//2.
promise.catch(err=>{
	console.log(err)
})
//或者
promise.then(res=>{
}).catch(err=>{
})
//catch默认情况下针对的是第一个promise而不是then返回的新的promise
//如果第一个promise没有异常，第二个promise有异常，catch也会捕获return
//出来的promise，如果第一个第二个peomise都有异常会先捕获第一个异常

//3.
promise.then()
promise.catch()
//会报错，因为then并没有针对reject进行处理
//可以通过链式调用。then。catch的方法或者then里面写两个函数的方法来解决

```

{% title h3, finally方法 %}
* 无论promise是什么状态都会执行。
* finally方法不接受参数。

{% title h2, promise类方法 %}
### resolve方法
**特点：**把普通对象转化成promise对象
```
const promise=Promise.resolve({name:'hhx'})
promise.then(res=>{
	console.log(res)
})
```
### reject方法
**特点：**无论传入什么值都是一样的
```
const promise=Promise.reject('失败')
//相当于
const promise=new Promise((resolve,reject)=>{
	reject('失败')
})
```
### all方法
1.首先创建多个promise
2.然后把所有的promise都变成fulfilled，再拿到结果
**特点：**
1.拿到的结果按照顺序返回
2.如果有一个变成rejected，那么整个都变成rejected
```
const p1=new Promise((resolve)=>{
	resolve(111)
})
const p2=new Promise((resolve)=>{
	resolve(222)
})
const p3=new Promise((resolve)=>{
	resolve(333)
})

Promise.all([p1,p2,p3,'aaa']).then(res=>{
	console.log(res)
}).catch(err=>{
	console.log(err)
})
//[111,222,333,'aaa']
```
### allSettled方法
**特点：**等到所有promise都有结果，无论是成功还是失败。
```
const p1=new Promise((resolve)=>{
	resolve(111)
})
const p2=new Promise((reject)=>{
	reject(222)
})
const p3=new Promise((resolve)=>{
	resolve(333)
})

Promise.allSettled([p1,p2,p3]).then((res)=>{
	console.log(res)
}).catch((err)=>{
	console.log(err)
})
//返回值一个数组里面有对象
[
  {status:'fulfilled',value:111},
  {status:'rejected',reason:222},
  {status:'fulfilled',value:333},
]
```
### race方法
**特点：**只要有一个promise执行变成fulfilled，那么就结束。如果有一个拒绝，那么就返回拒绝的结果。
```
Promise.race([p1,p2,p3]).then((res)=>{
	console.log(res)
}).catch((err)=>{
	console.log(err)
})
```
### any方法
**特点：**至少等到一个promise变成fulfilled，如果都拒绝，等到所有执行promise在执行catch。
```
Promise.any([p1,p2,p3]).then((res)=>{
	console.log(res)
}).catch((err)=>{
	console.log(err,err.errors)
  //可以通过errors拿到对应的错误
})
```
{% title h2, 手写promise %}
### 简单粗暴版
1.类的设计：通过class的方法
2.保存promise的状态，保存reject和resolve的参数
3.解决then的第一个回调函数，解决then的第二个回调函数
4.绑定定时器避免还没有调用then的时候，就不能执行onFulfilled和onreject函数，但是定时器是宏任务，promise是微任务，所以可以用{% pbg yellow, queueMicrotask%}。
```
const PROMISE_STATUS_PENDING='pending'
const PROMISE_STATUS_FULFILLED='fulfilled'
const PROMISE_STATUS_REJECTED='rejected'

class DiyPromise{
	constructor(executor){
    this.status=PROMISE_STATUS_PENDING  //promise的状态
    this.value=undefined	 //保存reject和resolve里面的参数
    this.reason=undefined

  	const resolve=(value)=>{
      if(this.status === PROMISE_STATUS_PENDING){
        setTimeout(()=>{
          this.status=PROMISE_STATUS_FULFILLED
          this.value=value
          console.log('resolve调用')
          this.onFulfilled(this.value)
        },0)
      }
    }
    const reject=(reason)=>{
    	if(this.status === PROMISE_STATUS_PENDING){
        setTimeout(()=>{
        	this.status=PROMISE_STATUS_REJECTED
          this.reason=reason
          console.log('reject调用')
          this.onRejected(this.value)
        },0)
      }
    }
    executor(resolve,reject)
  }
  then(onFulfilled,onRejected){
  	//on一般表示在什么状态就执行对应的函数
    this.onFulfilled=onFulfilled
    this.onRejected=onRejected
  }
}

const promise =new DiyPromise((resolve,reject)=>{
	reject()
    resolve()
})
```
### 改良版1.0
**方法的优化**
1.实现then多次调用
2.确定promise状态之后再次调用then
3.promise的then的链式调用  ——>then方法返回promise
```
//then不能调用多次
const PROMISE_STATUS_PENDING='pending'
const PROMISE_STATUS_FULFILLED='fulfilled'
const PROMISE_STATUS_REJECTED='rejected'

class DiyPromise{
	constructor(executor){
    this.status=PROMISE_STATUS_PENDING  //promise的状态
    this.value=undefined	 //保存reject和resolve里面的参数
    this.reason=undefined
    this.onFulfilledFns=[] //把回调的方法放数组里然后遍历去执行
    this.onRejectedFns=[] 
 
  	const resolve=(value)=>{
      if(this.status === PROMISE_STATUS_PENDING){
        //添加微任务
        queueMicrotask(()=>{
          //如果状态不等于pending就不执行
          if(this.status!==PROMISE_STATUS_PENDING) return 
          this.status=PROMISE_STATUS_FULFILLED
          this.value=value
          console.log('resolve调用')
          this.onFulfilledFns.forEach(fn=>{
          	fn(this.value)
          })
        })
      }
    }
    const reject=(reason)=>{
    	if(this.status === PROMISE_STATUS_PENDING){
        //添加微任务
        queueMicrotask(()=>{
          //如果状态不等于pending就不执行
          if(this.status!==PROMISE_STATUS_PENDING) return 
        	this.status=PROMISE_STATUS_REJECTED
          this.reason=reason
          console.log('reject调用')
          this.onRejectedFns.forEach(fn=>{
          	fn(this.reason)
          })
        })
      }
    }
    try{
      executor(resolve,reject)
    }catch(err){
      reject(err)
    }
    
  }
  then(onFulfilled,onRejected){ 
    return new DiyPromise((resolve,reject)=>{
    	//1.如果then调用的时候，状态已经确定下来,比如在定时器里调用promise。then方法
      if(this.status===PROMISE_STATUS_FULFILLED && onFulfilled){
        try{
        	const value =onFulfilled(this.value)
          resolve(value)
        }catch(err){
        	reject(err)
        }     
      }
      if(this.status===PROMISE_STATUS_REJECTED && onRejected){
        try{
        	const reason = onRejected(this.reason)
          resolve(reason)
        }catch(err){
        	reject(err)
        }     
      }

      //2.当状态是pending的时候
      //在传入的时候进行参数的判断，未空的时候进行处理，将成功的回调和失败的回调放到数组里
      if(this.status===PROMISE_STATUS_PENDING){
          this.onFulfilledFns.push(()=>{
            try{
              const value =onFulfilled(this.value)
              resolve(value)
            }catch(err){
              reject(err)
            }  
          })
          this.onRejectedFns.push(()=>{
          	try{
              const reason =onRejected(this.value)
              resolve(reason)
            }catch(err){
              reject(err)
            }  
          })
      }
    })
  }
}

const promise =new DiyPromise((resolve,reject)=>{
	reject()
  resolve()
})
```
**因为try，catch重复代码太多可以进行封装抽取**
### 改良版2.0
1.封装try和catch
2.实现对象方法和类方法
```
const PROMISE_STATUS_PENDING='pending'
const PROMISE_STATUS_FULFILLED='fulfilled'
const PROMISE_STATUS_REJECTED='rejected'

//工具函数
function execFunctionWithCatchErr(execFn,value,resolve,reject){
  try{
    const result =execFn(value)
    resolve(result)
  }catch(err){
    reject(err)
  } 
}

class DiyPromise{
	constructor(executor){
    this.status=PROMISE_STATUS_PENDING  //promise的状态
    this.value=undefined	 //保存reject和resolve里面的参数
    this.reason=undefined
    this.onFulfilledFns=[] //把回调的方法放数组里然后遍历去执行
    this.onRejectedFns=[]  
    
  	const resolve=(value)=>{
      if(this.status === PROMISE_STATUS_PENDING){
        //添加微任务
        queueMicrotask(()=>{
          //如果状态不等于pending就不执行
          if(this.status!==PROMISE_STATUS_PENDING) return 
          this.status=PROMISE_STATUS_FULFILLED
          this.value=value
          console.log('resolve调用')
          this.onFulfilledFns.forEach(fn=>{
          	fn(this.value)
          })
        })
      }
    }
    const reject=(reason)=>{
    	if(this.status === PROMISE_STATUS_PENDING){
        //添加微任务
        queueMicrotask(()=>{
          //如果状态不等于pending就不执行
          if(this.status!==PROMISE_STATUS_PENDING) return 
        	this.status=PROMISE_STATUS_REJECTED
          this.reason=reason
          console.log('reject调用')
          this.onRejectedFns.forEach(fn=>{
          	fn(this.reason)
          })
        })
      }
    }
    try{
      executor(resolve,reject)
    }catch(err){
      reject(err)
    }
    
  }
  then(onFulfilled,onRejected){
    //catch封装，当then里没有第二个参数的时候，由catch接收err
    onRejected=onRejected|| (err=>{throw err})
    //当执行finally中间有rejected的时候，catch用的是then方法接受的第一个参数不是undefined，要把value给return出去
    onFulfilled=onRejected|| (value=>{return value})
    
    return new DiyPromise((resolve,reject)=>{
    	//1.如果then调用的时候，状态已经确定下来,比如在定时器里调用promise。then方法
      if(this.status===PROMISE_STATUS_FULFILLED && onFulfilled){
        execFunctionWithCatchErr(onFulfilled,this.value,resolve,reject)
      }
      if(this.status===PROMISE_STATUS_REJECTED && onRejected){
        execFunctionWithCatchErr(onFulfilled,this.reason,resolve,reject)    
      }

      //2.当状态是pending的时候
      //在传入的时候进行参数的判断，未空的时候进行处理，将成功的回调和失败的回调放到数组里
      if(this.status===PROMISE_STATUS_PENDING){
        if(onFulfilled){
          this.onFulfilledFns.push(()=>{
            execFunctionWithCatchErr(onFulfilled,this.value,resolve,reject)
          })
        }
   			if(onRejected){
        	this.onRejectedFns.push(()=>{
          	execFunctionWithCatchErr(onFulfilled,this.reason,resolve,reject) 
          })
        } 
      }
    })
  }
  //catch方法的封装
  catch(onRejected){
  	return this.then(undefined,onRejected)
  }
  //finally方法的封装
  finally(onFinally){
  	this.then(()=>{
      onFinally()
    },()=>{
    	onFinally()
    })
  }
  
  //resolve和reject方法
  static resolve(value){
  	return new DiyPromise((resolve)=>resolve(value))
  }
  static reject(reason){
  	return new DiyPromise((resolve,reject)=>reject(reason))
  }
  
  //all方法
  static all(promises){
    //什么时候执行resolve和reject
  	return new DiyPromise((resolve,reject)=>{
    	const values=[]
      promises.forEach(promise=>{
      	promise.then(res=>{
        	values.push(res)
          if(values.length===promises.length){
          	resolve(values)
          }
        },err=>{
        	reject(err)
        })
      })
    })
  }
  //allSettled
  static allSettled(promises){
  	return new DiyPromise((resolve)=>{
    	const results=[]
      promises.forEach(promise=>{
      	promise.then(res=>{
        	results.push({status:PROMISE_STATUS_FULFILLED,value:res})
          if(results.length===promises.length){
          	resolve(results)
          }
        },err=>{
        	results.push({status:PROMISE_STATUS_REJECTED,value:err})
          if(results.length===promises.length){
          	resolve(results)
          }
        })
      })
    })
  }
  
  //race
  static race(promises){
  	return new DiyPromise((resolve,reject)=>{
      
      //笨办法
      promises.forEach(promise=>{
      	promise.then(res=>{
          resolve(res)
        },err=>{
        	reject(err)
        })
      }) 
      //回调它本身
      promises.forEach(promise=>{
      	promise.then(resolve,reject)
      })
    })
  }
  
  //any
  static any(){
    const reasons=[]
  	return new DiyPromise((resolve,reject)=>{
      promises.forEach(promise=>{
      	promise.then(resolve,err=>{
      		reasons.push(err)
        	if(reasons.length===promises.length){
        		reject(new AggregateError(reasons))
        	}
      	})
       })
    }) 
  }   	
}

const promise =new DiyPromise((resolve,reject)=>{
	reject(111)
  //resolve(222)
})
promise.then(res=>{
	console.log('res1',res)
  return 'aaa'
},(err)=>{
	console.log('err1',err)
  return 'bbb'
}).then(res=>{
	console.log('res2',res)
},(err)=>{
	console.log('err1',err)
  return 'bbb'
})

promise.then(res=>{
	console.log(res)
}).catch(err=>{
	console.log(err)
}).finally(()=>{
	console.log('finally')
})
```
{% title h2, promise手写总结 %}
1.promise的规范： [promisea+规范官网](https://promisesaplus.com/)。
2.promise类的设计：class 的方式也可以使用function的方式
3.构造函数的规划：
```
constructor(){
    //定义状态
    //定义resolve，reject回调
    //resolve执行微任务队列，改变状态，获取value，then传入执行回调
    //reject执行微任务队列，改变状态，获取reason，then传入执行回调

    //try catch
    executor(resolve,reject)
}
```
4.then方法的实现
```
then(onFulfilled,onRejected){
    //this.onFulfilled=onFulfilled
    //this.onRejected=onRejected  ——最简单的实现

    //1.判断穿进的参数是否有值，给地默认值
    //2.返回promise resolve /reject
    //3.判断之前的promise的状态是否确定 确定的话直接执行
    //4.添加到数组中push(（）=>执行 onFulfilled/onRejected 直接执行代码)
}
```
5.catch方法
```
catch(onRejected){
    return this.then(undefined,onRejected)
}
```
6.finally方法
```
finally(onFinally){
    return this.then(()=>{
        onFinally()
        },()=>{
        onFinally()
    })
}
```
7.reject/resolve
8.all/allSettled 
**重点：要知道搭配resolve和reject什么时候执行**
all：
1.所有的都有结果 
2.有一个reject
**allSettled：所有的都有结果，并且一定执行resolve**
9.race/any
race：只要有结果，就返回
any：
1.必须等到一个resolve结果
2.都没有resolve，所有的都是reject
