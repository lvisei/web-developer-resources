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
function _new(/* 构造函数 */ constructor, /* 构造函数参数 */ params) {
  // 将 arguments 对象转为数组
  var args = [].slice.call(arguments);
  // 取出构造函数
  var constructor = args.shift();
  // 创建一个空对象，继承构造函数的 prototype 属性
  var context = Object.create(constructor.prototype);
  // 执行构造函数
  var result = constructor.apply(context, args);
  // 如果返回结果是对象，就直接返回，否则返回 context 对象
  return (typeof result === 'object' && result != null) ? result : context;
}

// 实例
var actor = _new(Person, '张三', 28);
```



## 3. 手写 Create 方法？

 

## 4. 面向对象实现继承的方式？



## 5. 手写 call、bind、apply ？

#### call

```javascript
Function.prototype.call = function(cxt, ...args) {
  	// 判断调用对象是否为函数
    if (typeof this !== "function") {
      throw new TypeError("Error");
    }
		// 判断 context 是否传入，如果未传入则设置为 window
    ctx || (ctx = window);
		// 将调用函数设为对象的方法
    ctx.fn = this;
  	// 调用函数
  	let result = cxt.fn(...args);
  	// 将属性删除
  	delete cxt.fn;
  	return result;
  }
```

#### apply

```javascript
Funtion.prototype.apply = function(ctx, args) {
	  // 判断调用对象是否为函数
  	if (typeof this !== "function") {
    	throw new TypeError("Error");
  	}
		// 判断 context 是否传入，如果未传入则设置为 window
    ctx || (ctx = window);
	  // 将函数设为对象的方法
    ctx.fn = this;
 		// 调用方法
		let result = cxt.fn(args);
  	// 将属性删除
  	delete cxt.fn;
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
  
    return function() {
      return fn.apply(context, args.concat(...arguments))
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

