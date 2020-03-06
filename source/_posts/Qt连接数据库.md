---
title: Qt连接数据库
date: 2020-03-02 00:00:20
tags:
---

## qt5.9连接oracle数据库

qt msvc好像有32位跟64位的，而mingw只有32位的，之前安装的oracle数据库是64位的，所以折腾了半天都没编译好连接数据库的驱动，只好又去下载了32位的oracle 11g。

<!-- more -->
下面开始尝试编译驱动
+ 参考教程
+ https://blog.csdn.net/ac_huang/article/details/36477039#t2
+ https://blog.csdn.net/linux12a/article/details/77278207
+ 将原有的QMAKE_USE += oci改为QMAKE_LFLAGS +=xxxx/oci.dll
+ 添加 INCLUDEPATH += F:\app\stt\product\11.2.0\dbhome_1\OCI\include
+ 添加LIBPATH += F:\app\stt\product\11.2.0\dbhome_1\OCI\lib\MSVC
+ 注释include($$shadowed($$PWD)/qtsqldrivers-config.pri)
+ 添加include(./configure.pri)
+ 清除 构建 重新构建
+ 将:\plugins下面的“ sqldrivers ”文件夹复制到Qt的..\Qt\Qt5.8.0\5.8\mingw53_32\plugins下面即可