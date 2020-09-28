# Tools



## throttle

```javascript
function throttle(fn, threshhold) {
    let last, timerId;
    threshhold || (threshhold = 250);

    return function() {
      var now = Date.now();
      if(last && now - last < threshhold) {
        clearTimeout(timerId);
        timerId = setTimeout(() => {
          fn.apply(this, arguments);
        }, threshhold)
      } else {
        last = now;
        fn.apply(this, arguments);
      }
    }
  }
```



## debounce

```javascript
function debounce(fn, interval) {
    let timerId = null;

    return function() {
      clearTimeout(timerId);
      timerId = setTimeout(() => {
        fn.apply(this, arguments)
      }, interval)
    }
  }
```



## call、bind、apply

### call

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



### apply

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



### bind

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



## reduce

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



## 柯里化

- 参数不固定

```javascript
function currying(fn) {

  const argArr = [];

  let closure = function(...args) {

    if(args.length > 0) {

      argArr = [...argArr, ...args];

      return closure;

    }
    return fn(...argArr);

  }
  return closure;

}
```

- 参数固定长度

```javascript
function currying(fn) {
  return function (...args) {
    return args.length >= fn.length
      ? fn.apply(this, args)
      : currying(fn.bind(this, ...args));
  }
}
```


## 数据缓冲器分片处理

```javascript
function multistep(steps, args, callback){
    let tasks = steps.concat();

    setTimeout(function(){
        let task = tasks.shift();
        task.apply(null, args || []);   //调用 Apply 参数必须是数组

        if(tasks.length > 0){
            setTimeout(arguments.callee, 25);
        }else{
            callback();
        }
    }, 25);
}
```



## flatten

```javascript
/**
* [1,2,[3,4]] -> [1,2,3,4]
*/

let arr = [1,2,[3,4, [5,6, [7, [8, 9, 10]]]]]

function flatten(arr) {
    return arr.reduce(function(acc, next){
        let isArray = Array.isArray(next)
        return acc.concat(isArray ? flatten(next) : next)
    }, [])
}

if (!Array.prototype.flatten) {
    Array.prototype.flatten = function() {
        return flatten(this)
    }
}
console.log(arr.flatten());
```



## promise

```javascript
class CustomPromise {
  state = "PENDING"
  value = undefined
  thenCallbacks = []
  errorCallbacks = []

  constructor(action) {
    action(this.resolver.bind(this), this.reject.bind(this))
  }

  resolver(value) {
    this.state = "RESOLVED"
    this.value = value
    this.thenCallbacks.forEach((callback) => {
      callback(this.value)
    })
  }

  reject(value) {
    this.state = "REJECTED"
    this.value = value
    this.errorCallbacks.forEach((callback) => {
      callback(this.value)
    })
  }

  then(callback) {
    this.thenCallbacks.push(callback)
    return this 
  }

  catch (callback) {
    this.errorCallbacks.push(callback)
    return this 
  }
}

// example
let promise = new CustomPromise((resolver, reject) => {
  setTimeout(() => {
    const rand = Math.ceil(Math.random(1 * 1 + 6) * 6)
    if (rand > 2) {
      resolver("Success")
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

