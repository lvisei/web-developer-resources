# Tool

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

