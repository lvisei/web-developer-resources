# 初探地理数据可视化工具 kepler.gl

## 1. 写在前面

最近在看 Ubur 的 `Deck.gl`，间接性接触到它们开源的一个地理数据分析可视化工具 `kepler.gl`，之前也有看到，没有怎么玩过，恰好最近有点时间，来玩一玩这个地理数据可视化工具。

下面就以探索的方式展开简单玩玩 `kepler.gl`。

### 1.1 开源可视化库

> 可能工作的原因可能比较熟悉地理相关库，这里就举例经常看到用到的一些开源可视化库与工具。

### 1.1.1 简单分类

#### 图表库

- 基础图表

  - [ECharts](https://github.com/apache/echarts)

  - [Chart.js](https://github.com/chartjs/Chart.js)

  - [AntV-G2/F2](https://github.com/antvis/)

  - [Vega](https://github.com/vega/vega)

  - [Rough.js](https://github.com/rough-stuff/rough)

  - ...more

- 关系图和流程图

  - [mermaid](https://github.com/mermaid-js/mermaid)
- [sigma.js](https://github.com/jacomyal/sigma.js)
  - [AntV-G6/X6](https://github.com/antvis/)
- [dagre](https://github.com/dagrejs/dagre)
  - ...more

#### 地理库

- 2D
- [Leaflet](https://github.com/Leaflet/Leaflet)
  
- [OpenLayers](https://github.com/openlayers/openlayers)
  
- [Mapv](https://github.com/huiyan-fe/mapv)
  
- [AntV-L7](https://github.com/antvis/L7)
  
- 3D

  - [Mapbox GL JS](https://github.com/mapbox/mapbox-gl-js)
- [Cesium](https://github.com/CesiumGS/cesium)
  - [Deck.gl](https://github.com/visgl/deck.gl)
- [maptalks.js](https://github.com/maptalks/maptalks.js)
  - ...more

#### 数据驱动框架

- [D3](https://github.com/d3/d3)
- [d3-geo](https://github.com/d3/d3-geo)
  - ...more

#### 渲染库

- 2D
  - [Snap.svg](https://github.com/adobe-webplatform/Snap.svg)
    
  - [Fabric.js](https://github.com/fabricjs/fabric.js)
    
  - [PixiJS](https://github.com/pixijs/pixi.js)
    
  - ...more

- 2/3D
  - [p5.js](https://github.com/processing/p5.js)
    
  - [Sprite.js](https://github.com/spritejs/spritejs)

- 3D
  - [Three.js](https://github.com/mrdoob/three.js)
    
  - [Babylon.js](https://github.com/BabylonJS/Babylon.js)

### 1.1.2 简单关系图

![开源的数据可视化库](https://tva1.sinaimg.cn/large/008eGmZEgy1gnn1geja7zj319y0l5ado.jpg)

### 1.2 kepler.gl 了解

> kepler.gl 是 Uber 开源，面向大规模数据集的强大开源地理数据分析工具，基于 [deck.gl](https://github.com/visgl/deck.gl) 构建的 React 组件，高性能，用于大规模地理数据集的可视化分析探索。
>
> --- [kepler.gl](https://github.com/keplergl/kepler.gl) 

提到 `deck.gl` 就自然要了解一下地理空间可视化框架 [Vis.gl](https://vis.gl/) 的[生态](https://vis.gl/frameworks/)：

- [deck.gl](https://deck.gl/) - A high-performance WebGL 2 rendering framework for big data visualizations that integrates perfectly with reactive applications.
- [react-map-gl](https://visgl.github.io/react-map-gl/) - A React wrapper around Mapbox GL which works seamlessly with deck.gl.
- [React-vis](https://github.com/uber/react-vis)- A composable, deeply customizable charting library
- [luma.gl](https://luma.gl/) - A comprehensive set of WebGL 2 components targeting high-performance rendering and GPGPU computing.
- [loaders.gl](https://loaders.gl/) - a suite of framework-independent loaders for file formats focused on visualization of big data, including point clouds, 3D geometries, images, geospatial formats as well as tabular data.
- [nebula.gl](https://nebula.gl/) - High-Performance, 3D-enabled GeoJSON editing deck.gl and React

![Catalog](https://tva1.sinaimg.cn/large/008eGmZEgy1gpmy6i2zgsj30v20p0473.jpg)

了解大致关系情况后，就搭建 kepler.gl，来玩一玩看看。

## 2. 搭建 kepler.gl

#### 2.1 生成项目、安装依赖

生成项目使用 [create-react-app](https://github.com/facebook/create-react-app) 脚手架工具

```bash
npx create-react-app kepler.gl-taste
cd kepler.gl-taste
```

安装 `kepler.gl`相关依赖，`kepler.gl` 使用 [Redux](https://redux.js.org/) 管理组件状态，这里需要安装 `redux, react-redux`

```bash
npm install --save kepler.gl redux react-redux react-virtualized styled-components
```

安装 `react-virtualized` 需要使用到 `AutoSizer` 组件，方便自动调整适配屏幕

#### 2.2 添加相关代码

使用 Redux 创建状态管理 `store.js`

```js
import { createStore, combineReducers, applyMiddleware, compose } from "redux";
import keplerGlReducer from "kepler.gl/reducers";
import { enhanceReduxMiddleware } from "kepler.gl/middleware";

const initialState = {};

const customizedKeplerGlReducer = keplerGlReducer.initialState({
	// TODO: customize initial state
});

const reducers = combineReducers({
  keplerGl: customizedKeplerGlReducer,
	// TODO: app reducer
  // app: appReducer
});

export const middlewares = enhanceReduxMiddleware([
  // Add other middlewares here
]);

export const enhancers = [applyMiddleware(...middlewares)];

// using createStore
export default createStore(reducers, initialState, compose(...enhancers));
```

挂载 KeplerGl 组件 `app.js`

```js
import AutoSizer from "react-virtualized/dist/commonjs/AutoSizer";
import KeplerGl from "kepler.gl";
import "./App.css";

const MAPBOX_TOKEN = process.env.REACT_APP_MAPBOX_TOKEN; // eslint-disable-line
const MAPBOX_API_URL = "https://api.mapbox.com";

function App() {
  return (
    <div className="App">
      <AutoSizer>
        {({ height, width }) => (
          <KeplerGl
            mapboxApiAccessToken={MAPBOX_TOKEN}
            id="map"
            width={width}
            height={height}
            mapboxApiUrl={MAPBOX_API_URL}
          />
        )}
      </AutoSizer>
    </div>
  );
}

export default App;
```

挂载 APP 组件，注入 store，`index.js`

```js
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";
import store from "./store";
import App from "./App";
import "./index.css";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

更多相关代码查看 [kepler.gl-taste](https://github.com/liuvigongzuoshi/kepler.gl-taste)

#### 2.3 部署

之前一般选择 Vercel 与 travis-ci 来持续集成部署，现在 `Github Actions` 也比较好用了，这里就使用 `Github Actions` 来做持续集成。

![Github Actions](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn0w1moawj311h0p8adf.jpg)

新建工作流，配置 Actions [deploy-gh-page.yml](https://github.com/liuvigongzuoshi/kepler.gl-taste/blob/main/.github/workflows/deploy-gh-page.yml)

```yaml
name: deploy gh-pag CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  deploy:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [14.x]

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v2
      with:
        node-version: ${{ matrix.node-version }}
    - run: npm install
    - run: npm run build
    
    - name: deploy
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.ACCESS_TOKEN }}
        publish_dir: ./build
```

部署完成之后，访问部署后的[地址](https://liuvigongzuoshi.github.io/kepler.gl-taste/)，下就开始来玩玩 kepler.gl

![demo](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn42o0zkgj31lu0u07wh.jpg)

## 3. 使用 kepler.gl

### 3.1 拾取要分析的数据

这里就以带地理信息的天气数据来玩玩 `kepler.gl` 看看效果，数据就使用[国家气象科学数据中心](https://data.cma.cn/)收集的地面站点数据。不过使用数据之前需要注册帐号，审核通过之后就可以使用了。

![国家气象科学数据中心](https://tva1.sinaimg.cn/large/008eGmZEgy1gpmx0f8fnej31n10nqe5u.jpg)

这里就以四川地面气象站逐小时观测资料为演示数据，选取要观测要素，数据检索完成后，申请数据下载。

![数据检索](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn7k9fvxgj31ef0l2gsz.jpg)

审核通过后下载数据，数据格式如下图所示：

![逐小时观测资料](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn7e5vqv4j31ep053q4f.jpg)

可以看到数据只要站点编号，缺少站点信息数据，再次下载站点数据，数据格式如下图所示：

![站点数据](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn7m9zs8nj30b8067mxo.jpg)

下面进行数据合并处理

### 3.2 处理数据

编写 `JS` 脚本处理两个 `CSV` 数据格式，将只要站点编号的文件注入站点信息，为了方便处理这里导出为 Json 文件。

```js
const fs = require("fs")

const parseToJson = (data) => {
	const lines = data.trim().split(/[\r?\n]{1,2}/)
	const rows = lines.map((line) => line.trim().split(','))
	const headRow = rows.slice(0, 1)[0]
  const bodyRows = rows.slice(1)

  const headMap = headRow.reduce((pre, cur, index) => {
      pre.set(index, cur)
      return pre
    }, new Map())

  const records = bodyRows.map((row) => {
      const initialValue = {}
      const record = row.reduce((pre, cur, index) => {
        const key = headMap.get(index)

        if (key) {
          const vaule = Number(cur)
          if (cur === '' || Number.isNaN(vaule)) {
            pre[key] = cur
          } else {
            pre[key] = vaule
          }
        }
        return pre
      }, initialValue)
      return record
    })

  return records
}

const Station_Id_C = fs.readFileSync('./Station_Id_C.csv').toString();
const SrouceData = fs.readFileSync('./SrouceData.csv').toString();

const srouceData = parseToJson(SrouceData)
const stationJson = parseToJson(Station_Id_C)

const stationMap = new Map()

for (item of stationJson) {
		stationMap.set(item.Station_Id_C, item)
}

for (item of srouceData) {
	const station = stationMap.get(item.Station_Id_C)
	item.Lat = station.Lat
	item.Lon = station.Lon
	item.Alt = station.Alt
	item.Name = station.Name
}

fs.writeFileSync('result.json', JSON.stringify(srouceData))
```

### 3.3 使用数据

#### 3.3.1 操作示例

![站点数据分布 gif](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn8dt3p2eg30m80bjb2i.gif)

#### 3.3.2 各气象站点海拔高度分布

![各气象站点海拔高度分布](https://tva1.sinaimg.cn/large/008eGmZEgy1gpnazcyyktj31m30u04qp.jpg)

#### 3.3.3 各气象站点中午气温分布

![各气象站点中午气温分布](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn8qdp3mxj31ly0u0x6f.jpg)

#### 3.3.4 各气象站点中午气压热力图

![各气象站点中午气压热力图](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn99k4p7fj31lr0u0e81.jpg)

#### 3.3.5 各气象站点聚合分布图

![各气象站点聚合分布图](https://tva1.sinaimg.cn/large/008eGmZEgy1gpn9ebh693j31m00u0u09.jpg)

#### 3.3.6 各气象站点栅格化最大风速三维视图

![各气象站点栅格化最大风速三维视图](https://tva1.sinaimg.cn/large/008eGmZEgy1gpna1g2wmzj31lv0u0tx8.jpg)

#### 3.3.7 各气象站点体感温度三维视图

![各气象站点体感温度三维视图](https://tva1.sinaimg.cn/large/008eGmZEgy1gpnamm6a72j31m00u0e45.jpg)


## 4. 写在后面

上面主要利用气象站点的气象数据并结合带有的经纬度的地理数据进行简单数据可视化分析查看，因点数据的局限性，没有尝试 `kepler.gl` 还支持的线、弧线、面、三维模型等可视化功能。如果对上面的功能感觉兴趣，可在官网查看更多炫酷的[案例](https://kepler.gl/)：

| [Polygon](https://kepler.gl/demo/sfcontour) - 海拔等高线     | [Polygon](https://kepler.gl/demo/nyc_census) - 区域人口      | [Arc](https://kepler.gl/demo/ukcommute) - 通勤起终点         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![海拔等高线](https://tva1.sinaimg.cn/large/008eGmZEgy1gpnp0djq3ej31hc0u0b2b.jpg) | ![区域人口](https://tva1.sinaimg.cn/large/008eGmZEgy1gpnp3kfi73j31hc0u01ky.jpg) | ![通勤起终点](https://tva1.sinaimg.cn/large/008eGmZEgy1gpnp5gnt9wj31hc0u0x6q.jpg) |

除此之外也有供比较详细的[用户手册](https://docs.kepler.gl/docs/user-guides/j-get-started)，对无编码经验的同学也能快速上手制作一份地理数据可视化解决方案。

### 参考资料

- [kepler.gl api-reference](https://docs.kepler.gl/docs/api-reference)
- [Github Actions 部署前端项目](https://www.zoo.team/article/use-git-actions)
- [vis-academy](https://github.com/uber-common/vis-academy/tree/master/src/demos/kepler.gl)
- [中国气象科学数据中心](https://data.cma.cn/)



文中链接较多建议[原文地址](https://github.com/liuvigongzuoshi/blog/blob/master/README.md#可视化相关)查阅。