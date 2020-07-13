# 规范

## 目录结构

根据具体业务规范目录

```yaml
静态资源目录: assets
本地数据模拟目录: mocks
公共函数方法目录: utils
业务函数方法目录: helpers
单元测试目录: test
icon目录: icons
公共混合函数目录: mixin
路由目录: router
公共组件目录: components
页面目录: pages
业务组件目录：views
配置目录： config
服务api接口管理目录： api
vuex 状态管路目录： store
.env*: 取环境变量
```

## 语法约束

### 组件化命名规则

1. 按照功能命名：比如头部就是 Header,就是头部导航栏，按照页面来分组件：比如文章列表 NewsItem，即可用于文章列表，也可以用在详情页的内容推荐
3. 组件的文件名始终是单词大写开头 如：(PascalCase)
4. 在声明 prop 的时候，其命名应该始终使用 驼峰命名法
5. 组件名应该是完整单词而不是缩写

### vue 规范

1. 总是用 key 配合 v-for
2. 不要把 v-if 和 v-for 同时用在同一个元素上。
3. Object.freeze 方法来冻结一个不会有任何改变的对象，减少组件初始化的时间
4 .每个组件 export default {} 内的方法顺序一致，方便查找对应的方法。
按照 data、props、钩子、watch、computed、components
5. props里加数据类型，是否必传，以及默认值，便于排查错误，让传值更严谨
6. 使用定时器，要在beforeDestroy()生命周期内清除定时器

### CSS 命名

随着项目模块增多，防止因为不同页面或者组件中定义的 CSS 冲突，推荐使用: BEM命名规范，分别代表着:Block(块)、Element(元素)、Modifier(修饰符)

### 组件封装

- 组件文件就近化原则
- 组件设计单一原则


## 联网公司前端规范

[京东凹凸实验室代码规范](https://guide.aotu.io/docs/index.html)
[clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript?utm_source=gold_browser_extension)
[Airbnb团队](https://github.com/airbnb/javascript)
[百度fex团队](https://github.com/fex-team/styleguide)

