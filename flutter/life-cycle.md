# Flutter 生命周期

## 生命周期图

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk3od639nbj30ns0ipq42.jpg) | ![](https://tva1.sinaimg.cn/large/0081Kckwgy1gk3og7kspfj30ix0dx0vn.jpg) |



## 生命周期函数

### `createState`

该函数为 StatefulWidget 中创建 State 的方法，当 StatefulWidget 被调用时会立即执行 createState 。

### `initState` 

该函数为 State 初始化调用，因此可以在此期间执行 State 各变量的初始赋值，同时也可以在此期间与服务端交互，获取服务端数据后调用 setState 来设置 State。

### `didChangeDependencies` 

该函数是在该组件依赖的 State 发生变化时，这里说的 State 为全局 State ，例如语言或者主题等，类似于前端 Redux 存储的 State 。

### `build `

主要是返回需要渲染的 Widget ，由于 build 会被调用多次，因此在该函数中只能做返回 Widget 相关逻辑，避免因为执行多次导致状态异常。

### `reassemble` 

主要是提供开发阶段使用，在 debug 模式下，每次热重载都会调用该函数，因此在 debug 阶段可以在此期间增加一些 debug 代码，来检查代码问题。

### `didUpdateWidget `

该函数主要是在组件重新构建，比如说热重载，父组件发生 build 的情况下，子组件该方法才会被调用，其次该方法调用之后一定会再调用本组件中的 build 方法。

### `deactivate `

在组件被移除节点后会被调用，如果该组件被移除节点，然后未被插入到其他节点时，则会继续调用 dispose 永久移除。

### `dispose` 

永久移除组件，并释放组件资源



## 四个主要阶段

### 初始化阶段

- createState
- initState

### 组件创建阶段

- didChangeDependencies 
- build

### 触发组件再次 build

- didChangeDependencies
- setState
- didUpdateWidget

### 组件销毁阶段

- deactivate
- dispose