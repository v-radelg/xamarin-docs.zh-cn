---
title: 演练：： 绑定 iOS OBJECTIVE-C 库
description: 本文提供了实际演练如何创建现有 OBJECTIVE-C 的库，InfColorPicker 的 Xamarin.iOS 绑定。 它介绍了编译静态 Objective C 库、 绑定，以及在 Xamarin.iOS 应用程序中使用绑定等主题。
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: fcf4e6d9b281eaac4be888c499e537f7397528a0
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669266"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>演练：： 绑定 iOS OBJECTIVE-C 库

_本文提供了实际演练如何创建现有 OBJECTIVE-C 的库，InfColorPicker 的 Xamarin.iOS 绑定。它介绍了编译静态 Objective C 库、 绑定，以及在 Xamarin.iOS 应用程序中使用绑定等主题。_

IOS 上工作时，可能会遇到情况下你想要使用第三方 OBJECTIVE-C 的库。 在这些情况下，可以使用 Xamarin.iOS_绑定项目_来创建[C#绑定](~/cross-platform/macios/binding/overview.md)它将允许您使用 Xamarin.iOS 应用程序中的库。

通常 iOS 生态系统中可以找到库 3 两种版本：

* 为使用预编译静态库文件`.a`以及其标头 （.h 文件） 的扩展。 例如， [Google 的分析库](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* 作为预编译的框架。 这是仅包含静态库、 标头和有时其他相关资源的文件夹`.framework`扩展。 例如， [Google 的 AdMob 库](https://developers.google.com/admob/ios/download)。
* 正如刚才源代码文件。 例如，一个库，其中仅包含`.m`和`.h`Objective C 文件。

在第一个和第二个方案将已有预编译的 CocoaTouch 静态库以便在本文中我们将重点放在第三个方案上。 请记住，然后再开始创建一个绑定，请始终检查与库以确保你可以随意将其绑定提供了许可证。

本文提供了创建使用开放源代码的绑定项目的分步演练[InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective C 项目作为示例，但本指南中的所有信息都可以与任何调整，以使用第三方 OBJECTIVE-C 的库。 InfColorPicker 库提供了允许用户选择一种颜色根据其 HSB 表示形式，使用户更加友好的颜色选择的可重用视图控制器。

[![](walkthrough-images/run01.png "在 iOS 上运行的 InfColorPicker 库的示例")](walkthrough-images/run01.png#lightbox)

我们将介绍所有必要的步骤才能使用此特定的 Objective C API，在 Xamarin.iOS 中：

- 首先，我们将创建使用 Xcode 的 Objective C 静态库。
- 然后，我们会将绑定与 Xamarin.iOS 此静态库。
- 接下来，显示目标 Sharpie 如何通过自动生成部分 （而不是全部） 来减少工作负荷的 Xamarin.iOS 绑定所需的必要 API 定义。
- 最后，我们将创建使用该绑定的 Xamarin.iOS 应用程序。

示例应用程序将演示如何使用强委托 InfColorPicker API 之间的通信和我们C#代码。 我们已了解如何使用强委托后，我们将介绍如何使用弱委托来执行相同的任务。

## <a name="requirements"></a>要求

本文假定你已具备一定的 Xcode 和 OBJECTIVE-C 的语言，并且您已经阅读了我们[绑定 OBJECTIVE-C](~/cross-platform/macios/binding/index.md)文档。 此外，下面是需要填写显示的步骤：

-  **Xcode 和 iOS SDK** -Apple 的 Xcode 和最新的 iOS API 需要安装并配置开发人员的计算机上。
-  **[Xcode 命令行工具](#Installing_the_Xcode_Command_Line_Tools)** -Xcode 命令行工具必须安装 Xcode （请参阅下面有关安装的详细信息） 的当前安装的版本。
-  **Visual Studio for Mac 或 Visual Studio** -最新版本的 Visual Studio for Mac 或 Visual Studio 应安装并配置开发计算机上。 Apple Mac 开发所需的 Xamarin.iOS 应用程序，并使用 Visual Studio 时您必须连接到[Xamarin.iOS 生成主机](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **最新版本的目标 Sharpie** -从下载目标 Sharpie 工具的当前副本[此处](~/cross-platform/macios/binding/objective-sharpie/get-started.md)。 如果已安装的目标 Sharpie，则可以更新它的最新版本通过使用 `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>安装 Xcode 命令行工具

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


如上面所述，我们将使用 Xcode 命令行工具 (具体而言`make`和`lipo`) 在本演练中。 `make`命令是一个非常常见的 Unix 实用工具，将通过使用自动化的可执行程序和库编译_生成文件_，它指定应如何构建程序。 `lipo`命令是 OS X 命令行实用程序，用于创建多体系结构的文件; 它将合并多个`.a`到一个文件，它可以使用的所有硬件体系结构的文件。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


如上面所述，我们将使用 Xcode 命令行工具上**Mac 生成主机**(具体而言`make`和`lipo`) 在本演练中。 `make`命令是一个非常常见的 Unix 实用工具，将通过使用自动化的可执行程序和库编译_生成文件_来指定如何生成程序。 `lipo`命令是 OS X 命令行实用程序，用于创建多体系结构的文件; 它将合并多个`.a`到一个文件，它可以使用的所有硬件体系结构的文件。


-----

根据 Apple[从命令行的 Xcode 常见问题解答生成](https://developer.apple.com/library/ios/technotes/tn2339/_index.html)文档，在 OS X 10.9 及更高版本，则**下载**窗格中的 Xcode**首选项**对话框不不再支持支持下载命令行工具。

你将需要使用以下方法之一来安装工具：

- **安装 Xcode** -时安装 Xcode，它附带的所有命令行工具。 在 OS X 10.9 填充码 (安装在`/usr/bin`)，可以将映射中包含的任何工具`/usr/bin`到在 Xcode 内相应的工具。 例如，`xcrun`命令，可用于查找或从命令行运行在 Xcode 内的任何工具。
- **终端应用程序**-从终端应用程序，可以通过运行安装命令行工具`xcode-select --install`命令：
    - 启动终端应用程序。
    - 类型`xcode-select --install`并按**Enter**，例如：

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - 你将需要安装的命令行工具，请单击**安装**按钮： [![](walkthrough-images/xcode01.png "安装命令行工具")](walkthrough-images/xcode01.png#lightbox)

    - 将下载并安装从 Apple 的服务器工具： [![](walkthrough-images/xcode02.png "下载工具")](walkthrough-images/xcode02.png#lightbox)

- **适用于 Apple 开发人员的下载**-命令行工具包形式提供[适用于 Apple 开发人员的下载](https://developer.apple.com/downloads/index.action)网页。 登录你的 Apple ID，然后搜索并下载命令行工具：[![](walkthrough-images/xcode03.png "查找的命令行工具")](walkthrough-images/xcode03.png#lightbox)

安装的命令行工具，我们就准备好继续演练。

## <a name="walkthrough"></a>演练

在本演练中，我们将介绍以下步骤：

- **[创建静态库](#Creating_A_Static_Library)** -此步骤涉及到创建的静态库**InfColorPicker** Objective C 代码。 将具有静态库`.a`文件扩展名，并且将嵌入到类库项目的.NET 程序集。
- **[创建 Xamarin.iOS 绑定项目](#Create_a_Xamarin.iOS_Binding_Project)** -静态库后，我们将使用它来创建 Xamarin.iOS 绑定项目。 绑定项目包含我们刚刚创建的静态库和元数据的窗体中的C#介绍了如何使用 Objective C API 的代码。 此元数据通常称为 API 定义。 我们将使用 **[目标 Sharpie](#Using_Objective_Sharpie)** 来帮助我们与创建 API 定义。
- **[规范化的 API 定义](#Normalize_the_API_Definitions)** -目标 Sharpie 能够很好地帮助我们，但它不能执行的所有内容。 我们将讨论一些更改，我们需要创建到 API 定义，然后可以使用它们。
- **[使用绑定库](#Using_the_Binding)** -最后，我们将创建一个 Xamarin.iOS 应用程序来演示如何使用我们新创建的绑定的项目。

现在，我们已了解涉及哪些步骤，让我们继续本演练的其余部分。

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>创建静态库

如果我们在 Github 中 InfColorPicker 的检查的代码：

[![](walkthrough-images/image02.png "检查为 InfColorPicker 在 Github 中的代码")](walkthrough-images/image02.png#lightbox)

我们可以看到项目中的以下三个目录：

- **InfColorPicker** -此目录包含项目的 Objective C 代码。
- **PickerSamplePad** -此目录包含一个示例 iPad 项目。
- **PickerSamplePhone** -此目录包含一个示例 iPhone 项目。

让我们来下载 InfColorPicker 项目从[GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip)并将其解压缩我们选择的目录中。 打开 Xcode 目标`PickerSamplePhone`项目中，我们看到 Xcode 导航器中的以下项目结构：

[![](walkthrough-images/image03.png "Xcode 导航器中的项目结构")](walkthrough-images/image03.png#lightbox)

此项目通过直接将 InfColorPicker 源代码 （在红框中） 添加到每个示例项目来实现代码重用。 有关示例项目代码是在蓝色的框中。 由于此特定项目不提供我们的静态库，因此有必要为我们创建 Xcode 项目来编译此静态库。

第一步是我们要添加到静态库 InfoColorPicker 源代码。 若要实现此目的让我们执行以下操作：

1. 启动 Xcode。
2. 从**文件**菜单中选择**新建** > **项目...**:

    [![](walkthrough-images/image04.png "开始一个新项目")](walkthrough-images/image04.png#lightbox)
3. 选择**框架和库**，则**Cocoa Touch 静态库**模板，然后单击**下一步**按钮：

    [![](walkthrough-images/image05.png "选择 Cocoa Touch 静态库模板")](walkthrough-images/image05.png#lightbox)

4. 输入`InfColorPicker`有关**项目名称**然后单击**下一步**按钮：

    [![](walkthrough-images/image06.png "输入项目名称 InfColorPicker")](walkthrough-images/image06.png#lightbox)
5. 选择一个位置来保存项目，然后单击**确定**按钮。
6. 现在，我们需要将源从 InfColorPicker 项目添加到我们的静态库项目。 因为**InfColorPicker.h**文件已存在静态库中默认情况下，Xcode 将不允许我们将其覆盖。 从**Finder**、 导航到在原始项目中，我们从 GitHub 解压缩 InfColorPicker 源代码、 将所有 InfColorPicker 文件复制和粘贴到我们新的静态库项目：

    [![](walkthrough-images/image12.png "将所有 InfColorPicker 文件复制")](walkthrough-images/image12.png#lightbox)

7. 返回到 Xcode 中，右键单击**InfColorPicker**文件夹，然后选择**将文件添加到"InfColorPicker..."**:

    [![](walkthrough-images/image08.png "添加文件")](walkthrough-images/image08.png#lightbox)

8. 在添加文件对话框中，导航到我们刚刚复制的 InfColorPicker 源代码文件，它们全部选中，单击**添加**按钮：

    [![](walkthrough-images/image09.png "选择所有，然后单击添加按钮")](walkthrough-images/image09.png#lightbox)

9. 源代码将复制到我们的项目：

    [![](walkthrough-images/image10.png "源代码将复制到项目")](walkthrough-images/image10.png#lightbox)

10. 从 Xcode 项目导航器中，选择**InfColorPicker.m**文件并注释掉的最后两行 （由于写入此库，不使用此文件的方式）：

    [![](walkthrough-images/image14.png "编辑 InfColorPicker.m 文件")](walkthrough-images/image14.png#lightbox)

11. 我们现在需要检查是否有任何框架所需的库。 在自述文件，或通过打开提供的示例项目之一，可以找到此信息。 此示例使用`Foundation.framework`， `UIKit.framework`，和`CoreGraphics.framework`让我们来添加它们。

12. 选择**InfColorPicker 目标 > 生成阶段**展开**二进制与库链接**部分：

    [![](walkthrough-images/image16b.png "展开二进制与库链接部分")](walkthrough-images/image16b.png#lightbox)

13. 使用 **+** 按钮以打开对话框让你可以添加上面列出的所需的帧框架：

    [![](walkthrough-images/image16c.png "添加上面列出的所需的帧框架")](walkthrough-images/image16c.png#lightbox)

14. **二进制与库链接**部分现在应类似于下图所示：

    [![](walkthrough-images/image16d.png "二进制与库链接部分")](walkthrough-images/image16d.png#lightbox)

此时，我们已关闭，但我们不完全正确完成。 创建静态库，但我们需要生成它以创建 Fat 二进制，包含所有所需的体系结构适用于 iOS 设备和 iOS 模拟器。

### <a name="creating-a-fat-binary"></a>创建 Fat 二进制文件

所有 iOS 设备都已由 ARM 体系结构提供支持都了随着时间的推移而开发的处理器。 每个新的体系结构仍保持向后兼容性的同时添加了新指令和其他改进。 在 iOS 设备上，我们 armv6、 armv7、 armv7s、 arm64 指令集 – 尽管[我们不能再使用 armv6](~/ios/deploy-test/compiling-for-different-devices.md)。 IOS 模拟器不支持通过 ARM 并且 istead x86 和提供支持的 x86_64 模拟器。 我们对我们的意义是，我们必须在每个指令上提供库设置。

Fat 库是`.a`文件包含所有支持的体系结构。

创建 fat 二进制是一个三步骤过程：

- 编译静态库的 ARM 7 和 ARM64 版本。
- 编译静态库的 x86 和 x84_64 版本。
- 使用`lipo`命令行工具，可将两个静态库合并为一个。

这三个步骤是很简单，且可能有必要在将来重复这些 OBJECTIVE-C 库接收更新时，或者如果我们需要使用 bug 修补程序。 如果您决定自动执行这些步骤，它将简化未来的维护和支持的 iOS 绑定项目。

有许多工具可用于自动执行此类任务的 shell 脚本[rake](http://rake.rubyforge.org/)， [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)，并[使](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html)。 我们安装 Xcode 命令行工具，我们还安装了进行，因此，它是将用于本演练的生成系统。 下面是**生成文件**可用于创建将适用于 iOS 设备和模拟器的任何库的多体系结构共享的库：

```bash
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```

输入**生成文件**您选择的纯文本编辑器中的命令和更新各部分之间用**YOUR 项目名称**与项目的名称。 也很重要，确保我们粘贴中说明的选项卡都已被保留，上面的说明。

使用名称保存文件**生成文件**到与我们在上面创建 InfColorPicker Xcode 静态库相同的位置：

[![](walkthrough-images/lib00.png "生成文件的名称保存该文件")](walkthrough-images/lib00.png#lightbox)

打开你的 Mac 上的终端应用程序并导航到你的生成文件的位置。 类型`make`在终端中，按**Enter**并且**生成文件**将执行：

[![](walkthrough-images/lib01.png "生成文件的示例输出")](walkthrough-images/lib01.png#lightbox)

运行时进行，您将看到大量滚动的文本。 如果一切运行正常，则会看到单词**生成成功**并`libInfColorPicker-armv7.a`，`libInfColorPicker-i386.a`并`libInfColorPickerSDK.a`文件将复制到与相同的位置**生成文件**:

[![](walkthrough-images/lib02.png "生成生成文件的 libInfColorPicker armv7.a、 libInfColorPicker i386.a 和 libInfColorPickerSDK.a 文件")](walkthrough-images/lib02.png#lightbox)

可以通过使用以下命令来确认 Fat 二进制文件中的体系结构：

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

这应显示以下信息：

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

此时，我们已通过创建静态库使用 Xcode 和 Xcode 命令行工具完成我们的 iOS 绑定的第一步`make`和`lipo`。 让我们移动到下一步，并使用**目标 Sharpie**自动为我们的 API 绑定创建。

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>创建 Xamarin.iOS 绑定项目

我们可以使用之前**目标 Sharpie**若要自动执行绑定过程，我们需要创建 Xamarin.iOS 绑定项目，以容纳 API 定义 (，我们将使用**目标 Sharpie**来帮助我们生成），并创建C#我们的绑定。

让我们执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 启动 Visual Studio for mac。
1. 从**文件**菜单中，选择**新建** > **解决方案...**:

    ![](walkthrough-images/bind01.png "启动新的解决方案")

1. 从新解决方案对话框中，选择**库** > **iOS 绑定项目**:

    ![](walkthrough-images/bind02.png "选择 iOS 绑定项目")

1. 单击**下一步**按钮。

1. 输入"InfColorPickerBinding"作为**项目名称**然后单击**创建**按钮以创建解决方案：

    ![](walkthrough-images/bind02a.png "作为项目名称输入 InfColorPickerBinding")

将创建解决方案和两个默认文件将包含：

![](walkthrough-images/bind03.png "在解决方案资源管理器中的解决方案结构")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. 启动 Visual Studio。

1. 从**文件**菜单中，选择**新建** > **项目...**:

    ![开始一个新项目](walkthrough-images/bind01vs.png "开始一个新项目")

1. 从新建项目对话框中，选择**可视化C#> iPhone 和 iPad > iOS 绑定库 (Xamarin)**:

    [![选择 iOS 绑定库](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. 输入"InfColorPickerBinding"作为**名称**然后单击**确定**按钮以创建解决方案。

将创建解决方案和两个默认文件将包含：

![](walkthrough-images/bind03vs.png "在解决方案资源管理器中的解决方案结构")

-----

- **ApiDefinition.cs** -此文件将包含协定来定义如何 Objective C API 将包装在C#。
- **Structs.cs** -此文件将保存的任何结构或枚举值所需的接口和委托。

我们将使用在本演练后面这两个文件。 首先，我们需要将 InfColorPicker 库添加到绑定项目。

### <a name="including-the-static-library-in-the-binding-project"></a>绑定项目中包括静态库

现在，我们使用基本绑定项目准备就绪，我们需要添加我们上面创建的 Fat 二进制库**InfColorPicker**库。

请执行以下步骤添加到库：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 右键单击**本机引用**在 Solution Pad 中，选择文件夹**添加本机引用**:

    ![](walkthrough-images/bind04a.png "添加本机引用")

1. 导航到 Fat 二进制我们前面所做 (`libInfColorPickerSDK.a`) 按**打开**按钮：

    ![](walkthrough-images/bind05.png "选择 libInfColorPickerSDK.a 文件")
1. 该文件将包括在项目中：

    ![](walkthrough-images/bind04.png "包括的文件")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 复制`libInfColorPickerSDK.a`从你**Mac 生成主机**并将其粘贴到你绑定的项目。

1. 右键单击项目并选择**添加 > 现有项...**:

    ![](walkthrough-images/bind04vs.png "添加现有文件")

1. 导航到`libInfColorPickerSDK.a`并按**添加**按钮：

    ![](walkthrough-images/bind05vs.png "添加 libInfColorPickerSDK.a")

1. 将项目中包含该文件。

-----

当 **.a**文件添加到项目中，将自动设置 Xamarin.iOS**生成操作**的文件**ObjcBindingNativeLibrary**，并创建一个特殊的文件名为`libInfColorPickerSDK.linkwith.cs`。


此文件包含`LinkWith`告知 Xamarin.iOS 如何句柄的静态库刚刚添加的属性。 此文件的内容如下面的代码段所示：

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith`特性标识为项目和一些重要链接器标志的静态库。


我们需要做的下一件事是创建 InfColorPicker 项目的 API 定义。 出于本演练的目的，我们将使用目标 Sharpie 生成文件**ApiDefinition.cs**。

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>使用目标 Sharpie

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


目标 Sharpie 是一个命令行工具 （由 Xamarin 提供），它可以帮助您创建绑定到第三方 Objective C 库所需的定义C#。 在本部分中，我们将使用目标 Sharpie，创建初始**ApiDefinition.cs** InfColorPicker 项目。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


目标 Sharpie 是一个命令行工具 （由 Xamarin 提供），它可以帮助您创建绑定到第三方 Objective C 库所需的定义C#。 在本部分中，我们将使用目标 Sharpie 上我们**Mac 生成主机**，创建初始**ApiDefinition.cs** InfColorPicker 项目。


-----

若要开始，让我们来下载目标 Sharpie 安装程序文件，作为在此详细[指南](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing)。 运行安装程序并按照屏幕提示的所有从安装向导，我们开发计算机上安装目标 Sharpie。

我们已成功地将目标 Sharpie 之后安装，让我们启动终端应用并输入以下命令以获取有关所有工具，它提供了可帮助在绑定中的帮助：

```bash
sharpie -help
```

如果我们执行上面的命令，将生成以下输出：

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

为本演练中，我们将使用以下目标 Sharpie 工具：

- **xcode** -此工具为我们提供了有关我们当前的 Xcode 安装以及 iOS 和 Mac Api，我们已安装了版本的信息。 我们将使用此信息更高版本生成我们的绑定时。
- **将绑定**-我们将使用此工具来分析 **.h**到初始 InfColorPicker 项目中的文件**ApiDefinition.cs**并**StructsAndEnums.cs**文件。

若要获取特定的目标 Sharpie 工具的帮助，请输入工具的名称和`-help`选项。 例如，`sharpie xcode -help`将返回以下输出：

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

我们可以在绑定过程之前，我们需要通过在终端中输入以下命令获取有关当前已安装 Sdk 的信息`sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

综上所述，我们可以看到，我们有`iphoneos9.3`在我们的机器上安装 SDK。 使用此信息后，我们已准备好分析 InfColorPicker 项目`.h`文件到初始**ApiDefinition.cs**和`StructsAndEnums.cs`InfColorPicker 项目。

在终端应用中输入以下命令：

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

其中`[full-path-to-project]`是到目录的完整路径位置**InfColorPicker** Xcode 项目文件位于我们的计算机，和 [iphone 操作系统] 是 iOS SDK，我们已安装了，如标记所示`sharpie xcode -sdks`命令。 请注意，在此示例中我们已传递 **\*.h**作为参数，其中包括*所有*此目录-中的标头文件通常您应不这样做，而改为仔细阅读标头文件，以查找顶层 **.h**引用的所有其他相关文件，并只需将其传递给目标 Sharpie 文件。

以下[输出](walkthrough-images/os05.png)将生成在终端中：

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

并**InfColorPicker.enums.cs**并**InfColorPicker.cs**将我们目录中创建文件：

[![](walkthrough-images/os06.png "InfColorPicker.enums.cs 和 InfColorPicker.cs 文件")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


我们在上面创建的绑定项目中打开这两个文件。 将复制的内容**InfColorPicker.cs**文件，并将其粘贴到**ApiDefinition.cs**文件中，替换现有`namespace ...`代码块的内容与**InfColorPicker.cs**文件 (离开`using`语句不变):

![](walkthrough-images/os07.png "InfColorPickerControllerDelegate 文件")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


我们在上面创建的绑定项目中打开这两个文件。 将复制的内容**InfColorPicker.cs**文件 (从**Mac 生成主机**) 并将其粘贴到**ApiDefinition.cs**文件中，替换现有`namespace ...`代码块的内容**InfColorPicker.cs**文件 (离开`using`语句不变)。


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>规范化的 API 定义

目标 Sharpie 有时会有问题翻译`Delegates`，因此我们将需要修改的定义`InfColorPickerControllerDelegate`接口，并替换`[Protocol, Model]`用以下行：

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
因此，定义如下所示：

[![](walkthrough-images/os11.png "定义")](walkthrough-images/os11.png#lightbox)

接下来，我们执行的内容相同的操作`InfColorPicker.enums.cs`文件，复制和粘贴它们`StructsAndEnums.cs`文件，保留`using`保持不变的语句：

[![](walkthrough-images/os09.png "内容 StructsAndEnums.cs 文件 ")](walkthrough-images/os09.png#lightbox)

您还可能会发现目标 Sharpie 已批注的绑定`[Verify]`属性。 这些属性指示您应该验证目标 Sharpie 通过比较与原始 C/Objective C 声明 （它将在绑定声明上方的注释中提供） 的绑定未正确的做法。 确认绑定后，应删除验证属性。 有关详细信息，请参阅[验证](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)指南。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


此时，我们绑定的项目应完成并可以生成。 让我们来生成我们绑定的项目，并确保我们最终会有任何错误：

[生成绑定项目，并确保没有任何错误](walkthrough-images/os12.png)


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


此时，我们绑定的项目应完成并可以生成。 让我们来生成我们绑定的项目，并确保我们最终会有任何错误。


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>使用绑定

请按照以下步骤创建示例 iPhone 应用程序，以使用的 iOS 绑定库上面创建操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. **创建 Xamarin.iOS 项目**-添加一个名为的新 Xamarin.iOS 项目**InfColorPickerSample**到解决方案，如以下屏幕截图中所示：

    ![](walkthrough-images/use01.png "添加单一视图应用")

    ![](walkthrough-images/use01a.png "设置标识符")

1. **将引用添加到绑定项目**-更新**InfColorPickerSample**项目，以便它具有对引用**InfColorPickerBinding**项目：

    ![](walkthrough-images/use02.png "添加对绑定项目引用")

1. **创建用户界面 iPhone** -双击**mainstoryboard.storyboard**中的文件**InfColorPickerSample**项目以在 iOS 设计器中编辑它。 添加**按钮**到视图，并调用它`ChangeColorButton`，在下面的示例所示：

    ![](walkthrough-images/use03.png "向视图添加一个按钮")

1. **添加 InfColorPickerView.xib** -InfColorPicker OBJECTIVE-C 库包括 **.xib**文件。 Xamarin.iOS 不包括此 **.xib**在绑定项目中，这将导致在我们的示例应用程序的运行时错误。 对此解决方法是将添加 **.xib**对 Xamarin.iOS 项目文件。 选择在 Xamarin.iOS 项目，右键单击并选择**添加 > 添加文件**，并添加 **.xib**文件中，如以下屏幕截图所示：

    ![](walkthrough-images/use04.png "添加 InfColorPickerView.xib")

1. 当要求时，将复制 **.xib**文件到项目中的。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **创建 Xamarin.iOS 项目**-添加一个名为的新 Xamarin.iOS 项目**InfColorPickerSample**使用**单一视图应用**模板：

    [![iOS 应用 (Xamarin) 项目](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![选择模板](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **将引用添加到绑定项目**-更新**InfColorPickerSample**项目，以便它具有对引用**InfColorPickerBinding**项目：

    ![](walkthrough-images/use02vs.png "将引用添加到绑定项目")

1. **创建用户界面 iPhone** -双击**mainstoryboard.storyboard**中的文件**InfColorPickerSample**项目以在 iOS 设计器中编辑它。 添加**按钮**到视图，并调用它`ChangeColorButton`，在下面的示例所示：

    ![](walkthrough-images/use03vs.png "创建 iPhone 用户界面")

1. **添加 InfColorPickerView.xib** -InfColorPicker OBJECTIVE-C 库包括 **.xib**文件。 Xamarin.iOS 不包括此 **.xib**在绑定项目中，这将导致在我们的示例应用程序的运行时错误。 对此解决方法是将添加 **.xib**对 Xamarin.iOS 项目从文件我们**Mac 生成主机**。 选择在 Xamarin.iOS 项目，右键单击并选择**外** > **现有项...**，并添加 **.xib**文件。

-----

接下来，让我们快速看一下在 OBJECTIVE-C 和我们处理这些绑定中的协议和C#代码。

### <a name="protocols-and-xamarinios"></a>协议和 Xamarin.iOS

Objective C 中一种协议定义方法 （或消息），可以在某些情况下使用。 从概念上讲，它们是非常类似于中的接口C#。 OBJECTIVE-C 协议之间的主要区别和一个C#接口是协议可以有可选的方法的类没有实现的方法。 Objective C 使用@optional关键字用于指示哪些方法是可选的。 有关协议的详细信息请参阅[事件、 协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)。

**InfColorPickerController**具有一个此类协议，以下代码段中所示：

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

通过使用此协议**InfColorPickerController**以通知用户上选择了一种新颜色和的客户端**InfColorPickerController**已完成。 目标 Sharpie 映射此协议，如下面的代码段中所示：

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

Xamarin.iOS 绑定库编译时，将创建名为的抽象基类`InfColorPickerControllerDelegate`，它使用虚拟方法实现此接口。

有两种方法，我们可以在 Xamarin.iOS 应用程序中实现此接口：

- **强委托**-使用强委托时，需要创建C#类的子类`InfColorPickerControllerDelegate`并重写相应的方法。 **InfColorPickerController**将使用此类的实例与其客户端进行通信。
- **弱委派**-弱委派是一种略有不同的技术，包括一些类上创建一个公共方法 (如`InfColorPickerSampleViewController`)，然后公开该方法`InfColorPickerDelegate`协议通过`Export`属性。

强委托提供了 Intellisense、 类型安全和更好的封装。 出于这些原因，应使用强委托您可以在其中，而不是弱委派。

在本演练中，我们将讨论这两种技术： 首次实现强委托，然后介绍了如何实现弱委派。

### <a name="implementing-a-strong-delegate"></a>实现强委托

最后，Xamarin.iOS 应用程序使用强委托响应`colorPickerControllerDidFinish:`消息：

**子类 InfColorPickerControllerDelegate** -将新类添加到名为的项目`ColorSelectedDelegate`。 编辑类，使它具有以下代码：

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
    public class ColorSelectedDelegate:InfColorPickerControllerDelegate
    {
        readonly UIViewController parent;

        public ColorSelectedDelegate (UIViewController parent)
        {
            this.parent = parent;
        }

        public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
        {
            parent.View.BackgroundColor = controller.ResultColor;
            parent.DismissViewController (false, null);
        }
    }
}
```

Xamarin.iOS 将绑定 OBJECTIVE-C 的委托，通过创建名为的抽象基类`InfColorPickerControllerDelegate`。 子类此类型和重写`ColorPickerControllerDidFinish`方法访问的值`ResultColor`属性的`InfColorPickerController`。

**创建一个实例 ColorSelectedDelegate** -我们的事件处理程序将需要的实例`ColorSelectedDelegate`我们在上一步中创建的类型。 编辑类`InfColorPickerSampleViewController`并将以下实例变量添加到类：

```csharp
ColorSelectedDelegate selector;
```

**初始化 ColorSelectedDelegate 变量**-若要确保`selector`是一个有效实例，更新方法`ViewDidLoad`中`ViewController`以匹配以下代码片段：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**实现方法 HandleTouchUpInsideWithStrongDelegate** -下一步实现的事件处理程序时在用户触摸**ColorChangeButton**。 编辑`ViewController`，并添加以下方法：

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

我们首先获取的实例`InfColorPickerController`通过静态方法，并使该实例通过属性我们强委托注意`InfColorPickerController.Delegate`。 此属性是自动生成为我们的目标 Sharpie。 最后我们调用`PresentModallyOverViewController`以显示的视图`InfColorPickerSampleViewController.xib`，以便用户可以选择一种颜色。

**运行应用程序**-此时我们已经完成了所有我们的代码。 如果您运行该应用程序，您应能更改的背景色`InfColorColorPickerSampleView`如以下屏幕截图中所示：

[![](walkthrough-images/run01.png "运行应用程序")](walkthrough-images/run01.png#lightbox)

祝贺你！ 此时您已成功创建并绑定 OBJECTIVE-C 库在 Xamarin.iOS 应用程序中使用。 接下来，让我们了解如何使用弱委托。

### <a name="implementing-a-weak-delegate"></a>实现弱委派

而不是设置为特定的委托绑定到 OBJECTIVE-C 的协议的类的子类，Xamarin.iOS 还可以从派生的任何类中实现此协议方法`NSObject`，修饰方法使用与`ExportAttribute`，，然后提供相应的选择器。 为您的类的一个实例时采用此方法时，分配`WeakDelegate`属性，而不是个`Delegate`属性。 弱委派提供了灵活地采用委托类按不同的继承层次结构。 让我们了解如何实现和使用弱委派在 Xamarin.iOS 应用程序中。

**创建事件处理程序为 TouchUpInside** -让我们来创建新的事件处理程序`TouchUpInside`更改背景色按钮的事件。 此处理程序将填充与相同的角色`HandleTouchUpInsideWithStrongDelegate`我们在上一节中创建，但将使用弱委派而不是强委托处理程序。 编辑类`ViewController`，并添加以下方法：

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**更新 ViewDidLoad** -我们必须更改`ViewDidLoad`，使其使用我们刚刚创建的事件处理程序。 编辑`ViewController`并更改`ViewDidLoad`为类似于下面的代码段：


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**句柄 colorPickerControllerDidFinish:消息**-当`ViewController`是完成后，iOS 将消息发送`colorPickerControllerDidFinish:`到`WeakDelegate`。 我们需要创建C#可以处理此消息的方法。 若要执行此操作，我们创建C#方法，然后将其与修饰`ExportAttribute`。 编辑`ViewController`，并将以下方法添加到类：

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

运行该应用程序。 现在应进行的操作完全按照它以前的但它使用弱委派而不强的委托。 此时您已成功完成此演练。 现在应已了解如何创建和使用 Xamarin.iOS 绑定项目。

## <a name="summary"></a>总结

本文介绍创建和使用 Xamarin.iOS 绑定项目的过程。 首先，我们讨论了如何将现有的 OBJECTIVE-C 库编译为静态库。 然后，我们介绍如何创建 Xamarin.iOS 绑定项目，以及如何使用目标 Sharpie 生成 Objective C 库的 API 定义。 我们讨论了如何更新和调整生成的 API 定义，以使其适合公共使用。 Xamarin.iOS 绑定项目完成后，我们将使用该绑定中的 Xamarin.iOS 应用程序，重点是使用委托强和弱委托。

## <a name="related-links"></a>相关链接

- [绑定示例 （示例）](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [绑定 Objective-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)
- [绑定详细信息](~/cross-platform/macios/binding/overview.md)
- [绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [面向 Objective-C 开发人员的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
- [框架设计指南](https://msdn.microsoft.com/library/ms229042.aspx)
- [Xamarin 大学课程：生成一个 Objective C 绑定库](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin 大学课程：生成与目标 Sharpie Objective C 绑定库](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
