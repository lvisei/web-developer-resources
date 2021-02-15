# 数组在面试中常见问题



## 数据扁平化的几种方式？

1. 普通的递归实

```js
var a = [1, [2, [3, 4, 5]]];

function flatten(arr) {
  let result = [];
  for(let i = 0; i < arr.length; i++) {
    if(Array.isArray(arr[i])) {
      result = result.concat(flatten(arr[i]));
    } else {
      result.push(arr[i]);
    }
  }
  return result;
}

flatten(a);  //  [1, 2, 3, 4，5]
```

2. 利用 reduce 函数迭代

```js
var arr = [1, [2, [3, 4]]];
function flatten(arr) {
    return arr.reduce(function(prev, next){
        return prev.concat(Array.isArray(next) ? flatten(next) : next)
    }, [])

}
console.log(flatten(arr));//  [1, 2, 3, 4，5]
```

3. 扩展运算符实现

```js
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
    while (arr.some(item => Array.isArray(item))) {
        arr = [].concat(...arr);
    }
    return arr;
}
console.log(flatten(arr)); //  [1, 2, 3, 4，5]
```

4. 通过 split 和 toString 两个方法

```js
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
    return arr.toString().split(',');
}
console.log(flatten(arr)); //  [1, 2, 3, 4，5]
```

5. ES6 flatten

```js
var arr = [1, [2, [3, 4]]];

function flatten(arr) {
  return arr.flat(Infinity);
}
console.log(flatten(arr)); //  [1, 2, 3, 4，5]
```

6. JSON.stringify

```js
let arr = [1, [2, [3, [4, 5]]], 6];

function flatten(arr) {
  let str = JSON.stringify(arr);
  str = str.replace(/(\[|\])/g, '');
  str = '[' + str + ']';
  return JSON.parse(str); 
}
console.log(flatten(arr)); //  [1, 2, 3, 4，5]
```




## 数组去重的几种方式？

1. Array.from

```javascript
Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{ return a-b})
```

2. 扩展运算符

```javascript
[...new Set(arr.toString().split(","))].map(Number).sort((a,b)=>{ return a-b})
```

3. Array.filter

```javascript
const deepFlatten = arr => [].concat(...arr.map(v => (Array.isArray(v) ? deepFlatten(v) : v)))
deepFlatten(arr).filter((item, index, arr) => arr.indexOf(item) === index)
```

