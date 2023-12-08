# QGIS在Linux和Windows上的编译
# 2023年11月更新

在 Arch Linux 及 Windows 11 上都编译了 QGIS ，一些总结：都使用的 CMakePresets.json 文件来控制编译选项，包括 CMake 查找路径，可执行程序环境变量，编译开关，编辑工具平台，目标程序架构，Release/Debug 类型等等。避免了像以前一样需要调整 configonly.bat，msvc-env.bat package-nightly.bat 等文件，CMakePresets.json 文件直接作用于 CMakeLists.txt 文件。

安装 Visual Studio 2022 时勾选 C++ CMake tools for Windows，就不用额外安装 CMake 及 Ninja。

## Linux上的编译调试
### 编译及调试
+ OS: Debian 11 On VMware Host by Windows 11

参照INSTALL.md文档中[“3. Building on GNU/Linux”](https://github.com/qgis/QGIS/blob/master/INSTALL.md#3-building-on-gnulinux)章节的说明，即可编译成功。

为方便在Visual Studio Code中调试，可在VS Code中安装 C/C++、CMake Tools等扩展，然后打开源代码所在文件夹，VS Code便会自动进行配置，底部状态栏中有编译调试相关选项。
### 远程调试
由于调试时单步执行太慢，可能是受限于虚拟机性能影响，便想采用VS Code Remote模式调试，需要解决以下几个问题：
+ 在windows上ssh到虚拟机中的Linux
+ VS Code上连接到Linux，并启动Linux中编译好的QGIS

Windows 11/10 中自带ssh程序，如何连上VMware中的Linux呢？**需将虚拟机的网络类型由NAT改为Bridge**，然后在Linux中通过ip addr命令显示IP地址，ssh username@ipaddr即可连接。

Windows中的VS Code安装Remote-SSH扩展，根据提示操作即可。启动调试QGIS后，图形界面并没有显示，提示无法在非gui中运行，此时是我太naive。需要X Server。

安装VcXsrv，启动XLaunch，在Extra seetings界面中，**选中Disable access control**，否则会提示没有权限。

此时在VS Code Remote模式下启动调试QGIS，会有多个Terminal窗口，在输出无法运行的那个窗口中输入如下命令：
~~~bash
export DISPLAY="192.168.0.101:0.0"
~~~
其中“192.168.0.101”是X Server所在的ip地址，即windows的ip地址，测试一下xclock，时钟显示即成功。

然而，单步调试依然很慢。

### Windows上编译调试
此前按照INSTALL.md上[“4. Building on Windows”](https://github.com/qgis/QGIS/blob/master/INSTALL.md#4-building-on-windows)上是无法编译成功的，毕竟该文档也已经写明了，它是一份过期的文档。
***Consider this section as example. It tends to outdate***，
多次Google，加上多次实验，虽然最后略有瑕疵，依然有几个VS工程没有成功编译，但不影响主程序的运行，调试速度也正常，可称得上成功了。

#### 环境
+ OS：windows 11 21H2 build 22000.537
+ SDK: 10.22000.0
+ VS: 2022 Community

#### Steps

安装Cygwin，并在其中安装flex，bison。

安装OSGeo4W，使用测试版本，地址http://download.osgeo.org/osgeo4w/testing/osgeo4w-setup.exe，安装qgis-dev-deps。

安装CMake。

下载pkg-config，地址https://sourceforge.net/projects/pkgconfiglite/files/，解压exe，后面在CMakeLists.txt中设置路径。

克隆代码。

下载msvc-dev.bat，地址https://github.com/jef-n/OSGeo4W/tree/workflows/src/qgis-dev/osgeo4w，替换代码中ms-windows/osgeo文件夹下同名文件，修改如下：
~~~bat
添加 set PF64=%PROGRAMFILE%
修改 VCSDK=10.0.22000.0
修改 OSGEO4W_ROOT=C:\OSGeo4W
修改 %PF86%\Microsoft Visual Studio\2019\%%e 为 %PF64%\Microsoft Visual Studio\2022\%%e
由于qgis-dev-deps默认安装的是grass8，修改grass7位grass8，即
if exist %OSGEO4W_ROOT%\bin\grass78.bat set GRASS7=%OSGEO4W_ROOT%\bin\grass78.bat
改为
if exist %OSGEO4W_ROOT%\bin\grass80.bat set GRASS8=%OSGEO4W_ROOT%\bin\grass80.bat
~~~

修改 package-nightly.cmd，如下：
~~~bat
+ BUILDDIR改为与源代码目录同级的目录，即set BUILDDIR=%CD%\\..\\..\\..\build-%PACKAGENAME%-%ARCH%
+ 删除 call gdal-dev-env.bat
去掉 proj，gdal路径中的 apps/dev/ 改为
+ -D PROJ_LIBRARY=%O4W_ROOT%/lib/proj.lib
+ -D PROJ_INCLUDE_DIR=%O4W_ROOT%/include
+ -D GDAL_LIBRARY=%O4W_ROOT%/lib/gdal_i.lib
+ -D GDAL_INCLUDE_DIR=%O4W_ROOT%/include
~~~

修改 configonly.bat
+ CMAKEGEN=Visual Studio 14 2015 Win64 改为 CMAKEGEN=Visual Studio 17 2022

修改CMakeLists.txt
+ 添加 set(PKG_CONFIG_EXECUTABLE "pathto/pkg-config.exe")

## 编译
ms-windows/osgeo 目录下打开终端，执行configonly.bat

打开生成的qgis.sln

第一步先编译qgis_core工程。qgis_core以来qgis_core_autogen工程，qgisexpression_text.cpp.rule编译失败，提示init_fs_encoding相关错误，google找到原因，未设置PYTHONHOME环境变量
~~~
PYTHONHOME=C:\OSGeo4W\apps\Python39
~~~
qgis_core_autogen.rule编译失败，把 C:\OSGeo4W\apps\Qt5\bin;C:\OSGeo4W\bin; 加入path

zzz-db_manager-16-depend编译失败，提示from PyQt5.QtCore import QDir... DLL load failed，原因是依赖vs2019 runtime？？
安装MSVC142 VS2019 C++ x64/x86 build tools 并重启电脑

编译成功后，复制一份qgis.sln,去掉test相关工程并保存。
## tips

修改grass版本后清空编译目录，不然会缓存grass地址，cmake找不到grass
