VS调试时，如何显示QT数据类型，如QString，QList呢，安装Qt Visual Studio Tools是最直接的。

其实Qt Tools也是在 %UserProfile%\Documents\Visual Studio 2022\Visualizers中放入了两个文件qt5.natvis，qt6.natvis。那么直接放入此文件，不安装Qt Tools也是可以的。

链接：

+ [https://www.cnblogs.com/Braveliu/p/13951178.html](https://www.cnblogs.com/Braveliu/p/13951178.html)

+ [https://github.com/aleksey-nikolaev/natvis-collection](https://github.com/aleksey-nikolaev/natvis-collection)

+ [https://wiki.qt.io/IDE_Debug_Helpers](https://wiki.qt.io/IDE_Debug_Helpers)

+ [https://docs.microsoft.com/en-us/visualstudio/debugger/create-custom-views-of-native-objects](https://docs.microsoft.com/en-us/visualstudio/debugger/create-custom-views-of-native-objects)

+ [https://devblogs.microsoft.com/cppblog/using-visual-studio-2013-to-write-maintainable-native-visualizations-natvis](https://devblogs.microsoft.com/cppblog/using-visual-studio-2013-to-write-maintainable-native-visualizations-natvis/)
