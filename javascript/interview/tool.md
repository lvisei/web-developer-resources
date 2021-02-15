# JS 面试常见手写工具



## 1. 节流 throttle

```javascript
function throttle(fn, threshhold = 250) {
  let last, timerId;

  return function(...args) {
    let now = Date.now();
    if (last && now - last < threshhold) {
      clearTimeout(timerId);
      timerId = setTimeout(() => {
        fn.apply(this, args);
      }, threshhold)
    } else {
      last = now;
      fn.apply(this, args);
    }
  }
}

// 使用
const task = () => { console.log('run task') }
const throttleTask = throttle(task, 250)
window.addEventListener('scroll', throttleTask)
```



## 防抖 debounce

```javascript
function debounce(fn, interval = 250) {
  let timerId = null;

  return function(...args) {
    clearTimeout(timerId);
    timerId = setTimeout(() => {
      fn.apply(this, args)
    }, interval)
  }
}

// 使用
const task = () => { console.log('run task') }
const debounceTask = debounce(task, 1000)
window.addEventListener('scroll', debounceTask)
```



## 2. 柯里化

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



## 3. 数据缓冲器分片处理

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



## 4. imageLazyLoad

```javascript
// <img src="default.png" data-src="https://xxxx/real.png">
function isVisible(el) {
  const position = el.getBoundingClientRect()
  const windowHeight = document.documentElement.clientHeight
  const topVisible = position.top > 0 && position.top < windowHeight;
  const bottomVisible = position.bottom < windowHeight && position.bottom > 0;
  return topVisible || bottomVisible;
}

function imageLazyLoad() {
  const images = document.querySelectorAll('img')
  for (let img of images) {
    const realSrc = img.dataset.src
    if (!realSrc) continue
    if (isVisible(img)) {
      img.src = realSrc
      img.dataset.src = ''
    }
  }
}

// 使用
window.addEventListener('scroll', throttle(imageLazyLoad, 1000))
```



## 5. flatten

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

// 使用
const source = [1, 2, [3, 4, [5, 6]], 7]
console.log(arr.flatten(source));
```



## 6. objectFlat

```javascript
function objectFlat(obj = {}) {
  const res = {}
  function flat(item, preKey = '') {
    Object.entries(item).forEach(([key, val]) => {
      const newKey = preKey ? `${preKey}.${key}` : key
      if (val && typeof val === 'object') {
        flat(val, newKey)
      } else {
        res[newKey] = val
      }
    })
  }
  flat(obj)
  return res
}

// 使用
const source = { a: { b: { c: 1, d: 2 }, e: 3 }, f: { g: 2 } }
console.log(objectFlat(source));
```



## Promise

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

// 使用
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



## EventEmitter

```javascript

class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, callback) {
    const callbacks = this.events[event] || [];
    callbacks.push(callback);
    this.events[event] = callbacks;

    return this;
  }

  off(event, callback) {
    const callbacks = this.events[event];
    this.events[event] = callbacks && callbacks.filter(fn => fn !== callback);

    return this;
  }

  emit(event, ...args) {
    const callbacks = this.events[event];
    callbacks.forEach(fn => {
      fn(...args);
    });

    return this;
  }

  once(event, callback) {
    const wrapFun = function(...args) {
      callback(...args);

      this.off(event, wrapFun);
    };
    this.on(event, wrapFun);

    return this;
  }
}

// 使用
const eventBus = new EventEmitter()
const task1 = () => { console.log('task1'); }
const task2 = () => { console.log('task2'); }
eventBus.on('task', task1)
eventBus.on('task', task2)
setTimeout(() => {
  eventBus.emit('task')
}, 1000)
```



