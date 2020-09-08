# Tools



## throttle

```javascript
function throttle(fn, threshhold) {

    var last, timerId;

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

    var timerId = null;



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

    ctx || (ctx = window);

    ctx.fn = this;



    let args = [];

    let r = eval(`ctx.fn(${args})`);

    delete ctx.fn;



    return r;

  }
```



### apply

```javascript
Funtion.prototype.apply = function(ctx, args) {

    ctx || (ctx = window);

    ctx.fn = this;



    let r = eval(`ctx.fn(${args})`)

    delete ctx.fn;



    return r;

  }
```



### bind

```javascript
Funtion.prototype.bind = function(obj) {

    if(type of this !== 'function') {

      return;

    }



    var _self = this;

    var args = [].slice.call(arguments, 1);

    return function() {

      return _self.apply(obj, args.concat([].slice.call(arguments)))

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



## 数据缓冲器分片处理

```javascript
function multistep(steps, args, callback){
    var tasks = steps.concat();

    setTimeout(function(){
        var task = tasks.shift();
        task.apply(null, args || []);   //调用Apply参数必须是数组

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

