# Vue 开发小技巧

## 一、组件相关

1、生命周期：监听子组件生命周期 `created` 、`updated` 等，可使用 `@hook` 监听组件生命周期。

```vue
<template>
    <Child @hook:mounted="childMounted" />
</template>
```

2、动态注册hook：方便摧毁实例化的对象 。

```javascript
export default {
  mounted() {
    const picker = new Pickaday({
      // ...
    });

    this.$once("hook:beforeDestroy", () => {
      picker.destroy();
    })
  }
};
```



3、 数据劫持：`vue`中`data`的数据默认便会进行双向数据绑定，若是将大量的和渲染无关的数据直接放置在`data`中，将会浪费双向数据绑定时所消耗的性能，将这些和渲染无关的数据进行抽离并配合`Object.freeze`进行处理。

```javascript
const tableList = Object.freeze([
  { name: '张四', gender: 'male', age: '26' },
  { name: '李三', gender: 'fmale', age: '24' }
])
```

4、数据更新：一次性更新多个数据时，可使用如下方法一起赋值。

```javascript
const {name, age, gender} = resData
this = Object.assign(this, {name, age, gender})
```



## 二、路由相关

1、使用 `props` 将组件和路由解耦

```javascript
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true }
  ]
})
```


