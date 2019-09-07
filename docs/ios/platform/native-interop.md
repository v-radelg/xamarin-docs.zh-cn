---
title: 引用 Xamarin 中的本机库
description: 本文档介绍如何将本机 C 库链接到 Xamarin iOS 应用程序。 其中介绍了如何生成通用本机库和访问 C 方法C#。
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/28/2016
ms.openlocfilehash: 16e6d66cd41ead7a4d234cf45bb73e53e41aa5eb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769571"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>引用 Xamarin 中的本机库

Xamarin.iOS 支持本机 C 库和 Objective-C 库链接。 本文档介绍如何将本机 C 库与你的 Xamarin iOS 项目链接在一起。 有关为目标 C 库执行相同操作的详细信息，请参阅我们的[绑定目标-c 类型](~/ios/platform/binding-objective-c/index.md)文档。

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>构建通用本机库（i386、ARMv7 和 ARM64）

通常需要为 iOS 开发的每个受支持平台构建本机库（适用于模拟器的 i386 和设备本身的 ARMv7/ARM64）。 如果你已经为库提供了 Xcode 项目，这实际上是不必要的。

若要生成本机库的 i386 版本，请从终端运行以下命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

这将导致下面`MyProject.xcodeproj/build/Release-iphonesimulator/`的本机静态库。 将库存档文件（libMyLibrary）复制（或移动）到不安全的位置以供以后使用，并为其提供唯一名称（如**libMyLibrary**），以便它不会与将在下一次生成的同一库的 arm64 和 armv7 版本冲突。

若要生成本机库的 ARM64 版本，请运行以下命令：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

这次，生成的本机库将位于中`MyProject.xcodeproj/build/Release-iphoneos/`。 再次将此文件复制（或移动）到一个安全的位置，将其重命名为类似于**libMyLibrary-arm64**的内容，这样就不会发生冲突。

现在生成库的 ARMv7 版本：

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

将生成的库文件复制（或移动）到同一位置，并将库的其他两个版本重命名为 " **libMyLibrary-armv7**"。

若要创建通用二进制文件，可以使用如下`lipo`所示的工具：

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

这将`libMyLibrary.a`创建一个通用（fat）库，适用于所有 iOS 开发目标。

### <a name="missing-required-architecture-i386"></a>缺少所需的体系结构 i386

如果尝试在 iOS 模拟器`does not implement methodSignatureForSelector`中`does not implement doesNotRecognizeSelector`使用目标-C 库时在运行时输出中获取或消息，则可能是因为没有为 i386 体系结构编译库（请参阅[构建通用本机库](#building_native)"部分）。

若要检查给定库支持的体系结构，请在终端中使用以下命令：

```bash
lipo -info /full/path/to/libraryname.a
```

其中`/full/path/to/`是所使用的库的完整路径， `libraryname.a`是相关库的名称。

如果你有源到库，如果你想要在 iOS 模拟器上测试你的应用，则还需要对其进行编译并将其绑定到 i386 体系结构。

### <a name="linking-your-library"></a>链接库

所使用的任何第三方库都需要与应用程序静态链接。 

如果要以静态方式链接从 Internet 获取的或使用 Xcode 构建的库 "libMyLibrary"，则需要执行以下操作：

- 将库导入项目
- 配置 Xamarin 以链接库
- 从库中访问方法。

若要将**库导入项目**，请从 "解决方案资源管理器" 中选择项目，并按**命令 + 选项 + a**。 导航到 libMyLibrary，并将其添加到项目。 出现提示时，请告知 Visual Studio for Mac 或 Visual Studio 将其复制到项目中。 添加后，在项目中找到 libFoo，右键单击该项目，然后将 "**生成操作**" 设置为 "**无**"。

若要**配置 Xamarin 以链接库**，请在最终可执行文件（而不是库本身，而是最终程序）的项目选项上，在**IOS 生成**的额外参数（即项目选项的一部分）中添加 "-gcc_flags"选项后跟一个带引号的字符串，其中包含程序所需的所有额外库，例如：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

上面的示例将链接**libMyLibrary**

可以使用`-gcc_flags`指定要传递到用于执行可执行文件的最终链接的 GCC 编译器的任何命令行参数集。 例如，此命令行还引用了 CFNetwork 框架：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

如果本机库包含C++代码，还必须在 "额外参数" 中传递-.cxx 标志，以便 Xamarin 知道使用正确的编译器。 前面C++的选项如下所示：

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>从 C 访问 C 方法&#35;

IOS 上提供两种本机库：

- 作为操作系统一部分的共享库。

- 随应用程序一起提供的静态库。

若要访问其中任何一个中定义的方法，请使用[Mono 的 P/Invoke 功能](https://www.mono-project.com/docs/advanced/pinvoke/)，这是在 .net 中使用的相同技术，这一点大致为：

- 确定要调用的 C 函数
- 确定其签名
- 确定其所在的库
- 编写适当的 P/Invoke 声明

使用 P/Invoke 时，需要指定要链接的库的路径。 使用 iOS 共享库时，可以对路径进行硬编码，也可以使用我们在中`Constants`定义的便利常量，这些常量应涵盖 iOS 共享库。

例如，如果你想要从 Apple 的 UIKit 库中调用 UIRectFrameUsingBlendMode 方法，该方法在 C 中具有此签名：

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

P/Invoke 声明如下所示：

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

UIKitLibrary 只是一个定义为 "/System/Library/Frameworks/UIKit.framework/UIKit" 的常数，使用入口点，可以在中C#公开不同名称的同时指定外部名称（UIRectFramUsingBlendMode），更短的 RectFrameUsingBlendMode。

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>访问 C Dylib

如果你需要在 Xamarin iOS 应用程序中使用 C .dylib，则在调用该`DllImport`属性之前需要进行一些额外的设置。

例如，如果我们有`Animal.dylib` `Animal_Version`一个方法，我们将在应用程序中调用该方法，则需要在尝试使用库之前通知 Xamarin。

为此，请编辑`Main.CS`文件，使其类似于以下内容：

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

其中`/full/path/to/`是所使用的 .dylib 的完整路径。 在此代码准备就绪后，我们可以链接到`Animal_Version`方法，如下所示：

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>静态库

由于只能在 iOS 上使用静态库，因此没有要链接的外部共享库，因此 DllImport 特性中的 path 参数需要使用特殊名称`__Internal` （请注意名称开头的双下划线字符），而不是路径名称。

这会强制 DllImport 查找在当前程序中引用的方法的符号，而不是尝试从共享库中加载它。
