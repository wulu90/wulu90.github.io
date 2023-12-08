## CMakePresets.json 

### 编译QGIS
安装 Cygwin 和 OSGEO4W

```json
"environment": {
    "Cygwin_ROOT": "E:/cygwin64",
    "O4W_ROOT": "E:/osgeo4w",
    "PATH": "$penv{PATH};$env{O4W_ROOT}/bin;$env{O4W_ROOT}/apps/Qt5/bin;$env{O4W_ROOT}/apps/gdal-dev/bin"
},
"cacheVariables": {
    "CMAKE_CXX_COMPILER": "cl",
    "CMAKE_C_COMPILER": "cl",
    "CMAKE_CXX_STANDARD": "17",
    "GEOS_LIBRARY": "$env{O4W_ROOT}/lib/geos.lib",
    "CMAKE_PREFIX_PATH": "$env{Cygwin_ROOT}/bin;$env{O4W_ROOT};$env{O4W_ROOT}/lib;$env{O4W_ROOT}/apps/gdal-dev;$env{O4W_ROOT}/apps/grass/grass83;$env{O4W_ROOT}/apps/Python39;$env{O4W_ROOT}/Qt5"
},
"condition": {
    "type": "equals",
    "lhs": "${hostSystemName}",
    "rhs": "Windows"
},
"architecture": {
    "value": "x64",
    "strategy": "external"
},
"toolset": {
    "value": "host=x64",
    "strategy": "external"
}
```

environment.PATH 用于调试或启动可执行程序（exe）时，传递给该进程的环境变量，该 exe 可在 PATH 包含的路径中寻找dll，即不用将所有的依赖 dll 全部拷贝到 exe 所在目录；
+ $env{O4W_ROOT}/bin， 包含 zlib，zstd，sqlite，spatialite，hdf，tiff,
+ $env{O4W_ROOT}/apps/Qt5/bin，
+ $env{O4W_ROOT}/apps/gdal-dev/bin，

cacheVariables.CMAKE_PREFIX_PATH，用于 cmake 的 findpackage 方法在查找对应库是寻找的路径

+ $env{Cygwin_ROOT}/bin; 查找flex，bison
+ $env{O4W_ROOT}; 
+ $env{O4W_ROOT}/lib;
+ $env{O4W_ROOT}/apps/gdal-dev;
+ $env{O4W_ROOT}/apps/grass/grass83;
+ $env{O4W_ROOT}/apps/Python39;
+ $env{O4W_ROOT}/Qt5

architecture 编译结果的架构

toolset 编译器的架构，