# Vue 开发小技巧

## 一、生命周期相关

1、监听子组件生命周期 `created` 、`updated` 等，可使用 `@hook` 监听组件生命周期。

```vue
<template>
    <Child @hook:mounted="childMounted" />
</template>
```

## 二、路由相关

1、解析路由传值

