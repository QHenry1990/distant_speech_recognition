# Spatial signal processing toolkit (btk2.0)

Open source C++ and Python libraries to facilitate research and development 
for spatial signal processing

Please see http://distantspeechrecognition.sourceforge.net/ for project details.

2024.12.05编译说明：

环境必须是2.7，可以使用conda环境

其次有几个依赖库，swig, sndfile, gsl是必备的。

可以采用conda的形式安装sndfile, swig

gsl库可以采用./configure --prefix=`pwd`/release; make; make install;的形式安装到指定目录；

在最外侧的cmakelists.txt只需要注意几个路径即可

在build执行最基本的cmake ..; make; make install;即可

在python环境下执行import btk20; import btk20.feature as feat;如果正常则没有问题





