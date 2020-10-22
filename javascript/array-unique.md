# 数组重复

一

```javascript
Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{ return a-b})
```

二

```javascript
[...new Set(arr.toString().split(","))].map(Number).sort((a,b)=>{ return a-b})
```

三

```javascript
const deepFlatten = arr => [].concat(...arr.map(v => (Array.isArray(v) ? deepFlatten(v) : v)))
deepFlatten(arr).filter((item, index, arr) => arr.indexOf(item) === index)
```

