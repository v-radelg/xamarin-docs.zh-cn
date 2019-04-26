---
title: 在 Xamarin.iOS 中引用本机库
description: 本文档介绍如何将本机 C 库链接到 Xamarin.iOS 应用程序。 它介绍了如何构建通用的本机库和访问 C 方法从C#。
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: 7ed8fc18624f46abd4a9fc293d8c33a1722da7dd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424616"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>在 Xamarin.iOS 中引用本机库

Xamarin.iOS 支持本机 C 库和 Objective-C 库链接。 本文档介绍如何将本机 C 库与 Xamarin.iOS 项目的链接。 为 OBJECTIVE-C 的库进行相同的信息，请参阅我们[绑定 Objective C 类型](~/ios/platform/binding-objective-c/index.md)文档。

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>构建通用的本机库 （i386、 ARMv7 和 ARM64）

通常最好为每个受支持的平台为 iOS 开发生成本机库是 (i386 模拟器和 ARMv7/ARM64 的设备本身)。 如果你为你的库已有的 Xcode 项目，这是非常简单，以执行操作。

若要生成本机库的 i386 版本，请从终端运行以下命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

这将导致本机静态库下`MyProject.xcodeproj/build/Release-iphonesimulator/`。 复制 （或移动） 到安全供以后使用，为其指定唯一名称的位置的库存档文件 (libMyLibrary.a) (如**libMyLibrary i386.a**)，以便它不能与同一个库，你将支持 arm64 和 armv7 版本冲突后续版本。

若要生成的本机库 ARM64 版本时，运行以下命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

这一次生成本机库将位于`MyProject.xcodeproj/build/Release-iphoneos/`。 再次重申，复制 （或移动） 到一个安全位置，其重命名为类似于此文件**libMyLibrary arm64.a** ，以便它不会发生冲突。

现在生成 ARMv7 版本的库：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

复制 （或移动） 生成的库文件到同一个位置移动其他 2 个版本的库，其重命名为类似**libMyLibrary armv7.a**。

若要使通用二进制，可以使用`lipo`工具如下所示：

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

这将创建`libMyLibrary.a`这将是一个将适合使用所有的 iOS 开发目标的通用 (fat) 库。


### <a name="missing-required-architecture-i386"></a>缺少所需体系结构 i386

如果收到`does not implement methodSignatureForSelector`或`does not implement doesNotRecognizeSelector`时尝试使用 iOS 模拟器，你的库中的 Objective C 库可能在运行时输出中的消息的未编译目标为 i386 体系结构 (请参阅[构建通用本机库](#building_native)上面部分)。

若要检查给定库支持的体系结构，请在终端使用以下命令：

```bash
lipo -info /full/path/to/libraryname.a
```

其中`/full/path/to/`是已使用的库的完整路径和`libraryname.a`是有问题的库的名称。

如果必须在源到库，你将需要编译和捆绑 i386 体系结构中，如果你想要在 iOS 模拟器上测试应用。

### <a name="linking-your-library"></a>链接你的库

您使用任何第三方库必须与你的应用程序以静态方式链接。 

如果你想要静态链接库时所获取的 Internet 或使用 Xcode 生成的"libMyLibrary.a"，您需要做一些事情：

-  库放入项目
-  配置 Xamarin.iOS 以链接到库
-  从库中访问的方法。


向**放入项目的库**，选择项目从解决方案资源管理器并按**命令 + 选项 + a**。 导航到 libMyLibrary.a 并将其添加到项目。 出现提示时，告知 Visual Studio for Mac 或 Visual Studio 将其复制到项目。 之后将其添加，libFoo.a 查找项目中，右键单击它，并设置**生成操作**到**none**。

向**配置 Xamarin.iOS 以链接到库**，需要在最终可执行文件 （不在库自身，但最终的程序） 的项目选项中将添加**iOS 生成**的额外参数 （这些是项目选项的一部分）"-gcc_flags"选项后跟带引号的字符串，其中包含所有额外库所需的程序，例如：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

上面的示例中将链接**libMyLibrary.a**

可以使用`-gcc_flags`用于指定要传递给用于执行可执行文件的最后一个链接 GCC 编译器命令行任何的参数组。 例如，此命令行，还引用 CFNetwork framework:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

如果本机库包含C++还必须传递-cxx 标志"额外的参数"中，以便让 Xamarin.iOS 知道要使用正确的编译器的代码。 有关C++以前的选项如下所示：

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>从 C 中访问 C 方法&#35;

有两种类型的本机库可在 iOS 上：

-  共享库的操作系统的一部分。

-  随附于你的应用程序的静态库。


若要访问的那些之一中定义的方法，您可以使用[Mono 的 P/Invoke 功能](https://www.mono-project.com/docs/advanced/pinvoke/)这是在.NET 中，大概是将使用的相同技术：

-  确定你想要调用的 C 函数
-  确定它的签名
-  确定它所在的库
-  编写相应的 P/Invoke 声明

使用 P/Invoke 时需要指定了要链接的库路径。 使用 iOS 共享库，可以硬编码路径，也可以使用我们已在中定义的方便常量时我们`Constants`，这些常量应涵盖的 iOS 共享库。

例如，如果你想要调用从 c： 驱动器中具有此签名的 Apple 的 UIKit 库 UIRectFrameUsingBlendMode 方法

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

P/Invoke 声明如下所示：

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Constants.UIKitLibrary 是只是定义为一个常量"/ System/Library/Frameworks/UIKit.framework/UIKit"，入口点让我们根据需要指定外部名称 (UIRectFramUsingBlendMode) 能够同时公开不同的名称在C#，则较短 RectFrameUsingBlendMode。

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>访问 C Dylib

如果有需要使用 C dylib 中删除在 Xamarin.iOS 应用程序中，没有额外的设置，需要先调用一些`DllImport`属性。

例如，如果我们有`Animal.dylib`与`Animal_Version`我们将在我们的应用程序中调用的方法，我们需要以尝试使用它之前通知 Xamarin.iOS 库的位置。

若要执行此操作，编辑`Main.CS`文件，并使其看起来如下所示：

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

其中`/full/path/to/`是 dylib 中删除已使用的完整路径。 利用此代码，我们可以然后链接到`Animal_Version`方法，如下所示：

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>静态库

只能在 iOS 上使用静态库，因为没有要链接的没有外部共享的库因此 DllImport 特性中的路径参数需要使用特殊名称`__Internal`（请注意双下划线字符开头的名称） 而不是路径名称中。

这会强制 DllImport 查找符号引用当前程序，而不是尝试将其加载从共享库中的方法。

