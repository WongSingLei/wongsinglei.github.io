---
title: Windows 10 + Visual Studio 2015 安装 OpenCV 3.4.0 + contrib
tags: [配置]
categories: 计算机视觉
date: 2019-04-05 10:33:53
modified: 2019-04-05 12:37:56
---

## 下载 OpenCV 及 opencv_contrib

下载 [OpenCV](https://opencv.org/releases.html)，并下载对应版本的 [contrib](<https://github.com/opencv/opencv_contrib/releases>)。对于 Windows，可以下载 `Win pack` 的 `.exe` 文件进行安装。安装后 OpenCV 后，解压 opencv_contrib-3.4.0，并创建一个 build 文件夹（也可以叫其他名字）用于存放之后编译的内容。**注意**： contrib 的版本必须与 OpenCV 对应。本文以 OpenCV 3.4.0 为例，OpenCV 安装于 `E:\Program Files\opencv340`，opencv_contrib 也解压于该目录下。该目录文件结构如下：
```
|
--- build	// 编译文件夹
|
--- opencv	// OpenCV 文件夹
|
--- opencv_contrib-3.4.0	// contrib 文件夹
```



## 下载安装CMake

下载 [CMake](<https://github.com/Kitware/CMake/releases>)，如果已经安装过 CMake，可以跳过这一步。笔者使用的是 CMake 3.10.0 ，之前使用 CMake 3.11.1 时总是 Configure 和 Generate 正常，而使用 VS 编译时失败。故如果编译失败不妨试试更换 CMake 版本。

## 编译 OpenCV

打开 CMake，选择 OpenCV 文件夹和 build 文件夹。

{% asset_img cmake1.jpg CMake %}

点击 `Configure` ，选择 `Visual Studio 14 2015 Win64` 作为生成器。并勾选 `BUILD_opencv_world` ，如果没有勾选这个选项，之后会编译出多个 lib 文件，添加链接库时会比较麻烦。在 `OPENCV_EXTRA_MODULES_PATH` 添加 contrib 的路径。再次点击 `Configure`，如果没有标红点击 `Generate`，Generate done 后点击 `Open Project`，或者直接在 build 文件夹下，打开 `OpenCV.slh`。

在 VS 2015 下生成解决方案（可以使用快捷键 Ctrl + Shift + B），然后漫长的编译开始了。编译时间视配置而定，笔者因为环境较复杂，需要编译的组件较多，编译了一个多小时。然后在解决方案资源管理器中的 CMakeTargets 下的 INSTALL 右键下选择仅用于项目，仅生成 INSTALL。

## 配置

编译结束后，就该进行项目配置了。在上述操作后，在 build 文件夹下会生成一系列文件及文件夹，较为重要的是 install 文件夹。其中重要的文件结构如下：

```
|
--- include
	|
	--- opencv
	|
	--- opencv2
|
--- x64
	|
	--- vc14
		|
		--- bin	// PATH 环境变量路径，dll 文件路径
		|
		--- lib
```

先在环境变量 **PATH** 中添加 `E:\Program Files\opencv340\build\install\x64\vc14\bin`，然后在 VS 2015 中新建一个空的 Win32 控制台应用程序，在属性管理器中的 **Debug | 64** 下双击 `Microsoft.Cpp.x64.user`，打开属性页。在 VC++ 目录选择**包含目录**添加

E:\Program Files\opencv340\build\install\include

E:\Program Files\opencv340\build\install\include\opencv

E:\Program Files\opencv340\build\install\include\opencv2

在**库目录**添加

E:\Program Files\opencv340\build\install\x64\vc14\lib

然后选择链接器，输入，附加依赖项，添加 

opencv_world340d.lib

配置完成。