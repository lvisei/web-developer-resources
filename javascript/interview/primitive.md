# JS 面试常见手写原生方法



## 1. 如何实现一个深浅拷贝？

#### 浅拷贝

```js
const shallowClone = (target) => {
  if (typeof target === 'object' && target !== null) {
    const cloneTarget = Array.isArray(target) ? []: {};
    for (let prop in target) {
      if (target.hasOwnProperty(prop)) {
          cloneTarget[prop] = target[prop];
      }
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

#### 深拷贝

```js
let obj1 = { a:{ b:1} }
function deepClone(obj) { 
  let cloneObj = {}
  for(let key in obj) {                 //遍历
    if(typeof obj[key] ==='object') { 
      cloneObj[key] = deepClone(obj[key])  //是对象就再次调用该函数递归
    } else {
      cloneObj[key] = obj[key]  //基本类型的话直接复制值
    }
  }
  return cloneObj
}

let obj2 = deepClone(obj1);
obj1.a.b = 2;
console.log(obj2);   //  {a:{b:1}}
```



## 2. 手写 new 方法 ？

1. 创建一个空对象，作为将要返回的对象实例。
2. 将这个空对象的原型，指向构造函数的`prototype`属性。
3. 将这个空对象赋值给函数内部的`this`关键字。
4. 开始执行构造函数内部的代码。



```js
function _new(/* 构造函数 */ constructor, /* 构造函数参数 */ ...args) {
  // 创建一个空对象，继承构造函数的 prototype 属性
  let context = Object.create(constructor.prototype);
  // 执行构造函数
  let result = constructor.apply(context, args);
  // 如果返回结果是对象，就直接返回，否则返回 context 对象
  return (typeof result === 'object' && result != null) ? result : context;
}

// 实例
var actor = _new(Person, '张三', 28);
```



## 3. 手写 create、instanceof 方法？

#### instanceof 

 ```js
function myInstanceof(left, right) {
  // 这里先用typeof来判断基础数据类型，如果是，直接返回false
  if(typeof left !== 'object' || left === null) return false;
  // getProtypeOf是Object对象自带的API，能够拿到参数的原型对象
  let proto = Object.getPrototypeOf(left);
  while(true) {                  //循环往下寻找，直到找到相同的原型对象
    if(proto === null) return false;
    if(proto === right.prototype) return true;//找到相同原型对象，返回true
    proto = Object.getPrototypeof(proto);
  }
}

console.log(myInstanceof(new Number(123), Number));    // true
console.log(myInstanceof(123, Number));                // false
 ```

#### create

```js
function myCreate(proto) {
  function F() {}
  F.prototype = proto
  
  return new F()
}
```





## 4. 面向对象实现继承的方式？



## 5. 手写 call、bind、apply ？

#### call

```javascript
Function.prototype.call = function(context, ...args) {
  	// 判断调用对象是否为函数
    if (typeof this !== "function") {
      throw new TypeError("Error");
    }
		// 判断 context 是否传入，如果未传入则设置为 window
    context = context || window;
		// 将调用函数设为对象的方法
    context.fn = this;
  	// 调用函数
  	let result = context.fn(...args);
  	// 将属性删除
  	delete context.fn;
  	return result;
  }
```

#### apply

```javascript
Funtion.prototype.apply = function(context, args) {
	  // 判断调用对象是否为函数
  	if (typeof this !== "function") {
    	throw new TypeError("Error");
  	}
		// 判断 context 是否传入，如果未传入则设置为 window
    context || (context = window);
	  // 将函数设为对象的方法
    context.fn = this;
 		// 调用方法
		let result = context.fn(args);
  	// 将属性删除
  	delete context.fn;
 	 	return result;
  }
```

#### bind

```javascript
Funtion.prototype.bind = function(context, ...args) {
		// 判断调用对象是否为函数
    if(type of this !== "function") {
			throw new TypeError("Error");
    }
    let fn = this;
  
    return function(..._args) {
      return fn.apply(context, args.concat(_args))
    }
  }
```



## 6. 手写 reduce ？

```javascript
Array.prototype.reduce = function(func, initState) {
  const arr = this
  const callback = func
  let init = initState

  arr.forEach(function(value, index){
      init=callback(init, value)
  })
  return init
}
```



## 7. 手写 Promise ？

<details>
<summary>Promise</summary>

```js
class MyPromise {
  state = "PENDING"
  value = undefined

  resolvedCallbacks = []
  rejectedCallbacks = []

  constructor(fn) {
    fn(this.resolve.bind(this), this.reject.bind(this))
  }

  resolve(value) {
    if (this.state === 'PENDING') {
      this.state = "RESOLVED"
  	  this.value = value
	    this.resolvedCallbacks.forEach(cb => cb(this.value))
    }
  }

  reject(value) {
    if (this.state === 'PENDING') {
      this.state = "REJECTED"
   		this.value = value
		  this.rejectedCallbacks.forEach(cb => cb(this.value))
    }
  }

  then(onFulfilled, onRejected) {
    if (this.state === 'PENDING') {
      this.resolvedCallbacks.push(onFulfilled)
      this.rejectedCallbacks.push(onRejected)
    }
    
    if (this.state === 'RESOLVED') {
      onFulfilled(this.value);
    }
    
    if (this.state === 'REJECTED') {
      onRejected(this.value);
    }
    return this
  }

  catch (callback) {
    if (this.state === 'PENDING') {
      this.rejectedCallbacks.push(callback)
    }
    
    if (this.state === 'REJECTED') {
      callback(this.value);
    }
    return this
  }
}

// 使用
let promise = new MyPromise((resolve, reject) => {
  setTimeout(() => {
    const rand = Math.ceil(Math.random(1 * 1 + 6) * 6)
    if (rand > 2) {
      resolve("Success")
    } else {
      reject("Error")
    }
  }, 1000)
})

promise
  .then(function(response){
    console.log(response)
  })
  .catch(function(error){
    console.log(error)
  })
```

</details>



<details>
  <summary>Promise.all()</summary>

```js
function MyPromiseAll(arr) {
  let res = []
  return new Promise((resolve, reject) => {
    for (let i = 0; i < arr.length; i++) {
      Promise.resolve(arr[i])).then(data => {
          res[i] = data;
          if (res.length === arr.length) {
            resolve(res)
          }
        }).catch(error => {
          reject(error)
        })
    }
  })
}
```
</details>

<details>
  <summary>Promise.race()</summary>

```js
function MyPromiseRace(arr) {
  return new Promise((resolve, reject) => {
    for (let i = 0; i < arr.length; i++) {
    	arr[i].then(resolve, reject)
    }
  })
}
```
</details>
## 8. EventEmitter 订阅监听模式 ?

```js
class EventEmitter {
  constructor() {
    this.events = {};
  }
  on (eventName, callback) {
    if(!this.events[eventName]) {
      this.events[eventName] = [callback];
    } else {
      this.events[eventName].push(callback);
    }
  }

  emit(eventName, ...args) {
    this.events[eventName].forEach(fn => fn.apply(this, args));
  }

  once(eventName, callback) {
    const fn = () => {
      callback();
      this.remove(eventName, fn);
    }
    this.on(eventName, fn)
  }

  remove(eventName, callback) {
    this.events[eventName] = this.events[eventName].filter(fn => fn != callback);
  }
}
```