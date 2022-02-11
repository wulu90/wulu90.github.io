GIS数据的坐标系分为两种，地理坐标系和投影坐标系，地理坐标系是基于椭球的，那么它是如何在GIS软件中二维显示的呢？

ArcGIS采用了一种称为简易圆柱 (Plate Carrée) 投影的方法，即将十进制经纬度直接当作XY坐标

中文搜索：经纬度直投、经纬度等间隔直投、空投影

Reference
+ https://desktop.arcgis.com/zh-cn/arcmap/latest/map/projections/about-geographic-coordinate-systems.htm
+ https://desktop.arcgis.com/zh-cn/arcmap/latest/map/projections/plate-carree.htm
+ https://pro.arcgis.com/zh-cn/pro-app/latest/help/mapping/properties/plate-carree.htm
+ https://proj.org/operations/projections/eqc.html
+ https://en.wikipedia.org/wiki/Equirectangular_projection