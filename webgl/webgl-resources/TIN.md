# TIN/Mesh



## 栅格数据生成 TIN 思路

- [software recommendations - Generating TIN from elevation data in Python - Geographic Information Systems Stack Exchange ](https://gis.stackexchange.com/questions/173365/generating-tin-from-elevation-data-in-python) - Generating TIN from elevation data in Python

- [convert - Seeking tool to generate Mesh from DTM? - Geographic Information Systems Stack Exchange ](https://gis.stackexchange.com/questions/121561/seeking-tool-to-generate-mesh-from-dtm) - convert - Seeking tool to generate Mesh from DTM

- [master maps: Creating a TIN from a raster DEM ](https://blog.mastermaps.com/2016/09/creating-tin-from-raster-dem.html) - Creating a TIN from a raster DEM

- [Workflow from DEM to QM · Issue #6 · loicgasser/quantized-mesh-tile ](https://github.com/loicgasser/quantized-mesh-tile/issues/6) - Workflow from DEM to QM



## 栅格数据生成 TIN, 在 three.js 使用，博客教程系列

- [master maps: Converting terrain data to a WebGL-friendly format ](https://blog.mastermaps.com/2013/10/terrain-building-with-threejs-part-1.html)
- [master maps: Terrain building with three.js ](https://blog.mastermaps.com/2013/10/terrain-building-with-threejs.html)





## Grid 转 TIN（.obj/.stl）

- [heremaps/tin-terrain: A command-line tool for converting heightmaps in GeoTIFF format into tiled optimized meshes. ](https://github.com/heremaps/tin-terrain) - A command-line tool for converting heightmaps in GeoTIFF format into tiled optimized meshes.
- [jdugge/GridToTIN: Python implementation of Garland and Heckbert's terrain approximation algorithm ](https://github.com/jdugge/GridToTIN)

- [phstl](https://github.com/anoved/phstl) - Convert GDAL rasters (like GeoTIFF heightmaps) to 3D STL surfaces.



##  Grid 转 terrain

[gdal2cesium](https://github.com/giohappy/gdal2cesium) - A Python+GDAL Cesium heightmap/terrain generator



## Martini/Delatin

### Martini

> Terrain RGB 
>
> Encodes raw elevation values into a PNG, using the red, green, and blue channels for a high bit depth. The terrarium encoding has 3-millimeter precision and the mapbox encoding (not yet implemented) has 10-centimeter precision.
>
> encoding png: Either 'mapbox' or 'terrarium', the two main RGB encodings for elevation values

- [kylebarron/pymartini: A Cython port of Martini for fast RTIN terrain mesh generation ](https://github.com/kylebarron/pymartini) - python martini

- [engelsjk/gmartini: A Go port of Martini for RTIN terrain mesh generation ](https://github.com/engelsjk/gmartini) - Go martini
- https://github.com/flywave/go-martini - Go martini
- [martini aframe - CodeSandbox ](https://codesandbox.io/s/martini-aframe-8b2i8?file=/src/martini-rtin.js:1413-1414) - JS martini code
- https://observablehq.com/@mourner/martin-real-time-rtin-terrain-mesh -  JS martini blog

### Delatin

- [delatin](https://github.com/mapbox/delatin) - JS delatin
- [pydelatin](https://github.com/kylebarron/pydelatin) - python  delatin



## Quantized Mesh

- [quantized-mesh](https://github.com/CesiumGS/quantized-mesh) - Specification for streaming massive terrain datasets for 3D visualization.

- [loicgasser/quantized-mesh-tile: Quantized-Mesh encoder/decoder and topology builder ](https://github.com/loicgasser/quantized-mesh-tile) - Python encoder/decoder and topology builder for terrain tiles.

- [quantized-mesh-viewer](https://github.com/heremaps/quantized-mesh-viewer) - Render custom quantized mesh tiles in Cesium.js and debug individual tiles using THREE.js renderer.

- [quantized-mesh-decoder](https://github.com/heremaps/quantized-mesh-decoder) - JavaScript decoder for the Quantized Mesh format

- [quantized-mesh-encoder](https://github.com/kylebarron/quantized-mesh-encoder) - A fast Python Quantized Mesh encoder
- [Fast-Quadric-Mesh-Simplification](https://github.com/sp4cerat/Fast-Quadric-Mesh-Simplification) - C++ Mesh triangle reduction using quadrics



## 知名的栅格数据处理软件

- [MeshLab](https://www.meshlab.net/) - MeshLab是立体网状处理软件，自由软件

- [SAGA GIS](http://www.saga-gis.org/) - 自动地理科学分析系统是地理信息系统计算机程序，用于编辑空间数据
- [GRASS GIS](https://grass.osgeo.org/) - 一个免费、开放源代码的地理信息系统，可用于处理栅格、拓扑矢量、影像和图表数据
- [GDAL](http://www.gdal.org/) - GDAL



## Mesh(.stl) 编辑软件

 - [Meshmixer](https://www.meshmixer.com/) - [Extrude a Surface with Meshmixer](https://github.com/anoved/phstl/blob/master/demo/meshmixer.md)

- [Blender](https://www.blender.org/) - [Extrude a Surface with Blender](https://github.com/anoved/phstl/blob/master/demo/blender.md)



## Convert 3D models to GLTF

- [Convert 3D models to GLTF | BlackThread.io ](https://blackthread.io/gltf-converter/)

- [Wasm Convert STL to glb](https://github.com/MyMiniFactory/stl2gltf)



## GeoTIFF

- [geotiff.js](https://github.com/geotiffjs/geotiff.js) - geotiff.js is a small library to parse TIFF files for visualization or analysis. It is written in pure JavaScript, and is usable in both the browser and node.js applications.



## Golang

- [gdal](https://github.com/lukeroth/gdal) - Go (golang) wrapper for GDAL, the Geospatial Data Abstraction Library

- [flywave/go-3dtile: 3dtile For GO ](https://github.com/flywave/go-3dtile) - 3dtile For GO

- [qmuntal/gltf: Go library for d/encoding glTF 2.0 files ](https://github.com/qmuntal/gltf)

- [go-tin](https://github.com/flywave/go-tin)

- [simplify](https://github.com/fogleman/simplify) - 3D mesh simplification 