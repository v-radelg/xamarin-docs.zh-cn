---
title: 安装和在 Xamarin 中使用 watchOS
description: 本文档介绍如何安装和使用 Xamarin 使用 watchOS。 此外，介绍了安装，watchOS 项目结构，如何使用 iOS 设计器、 Xcode 集成，并提供了故障排除提示。
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 12/05/2017
ms.openlocfilehash: 1bc7ba07676c66c411357b5ad043258fea611d71
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293126"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>安装和在 Xamarin 中使用 watchOS

watchOS 4 需要 macOS Sierra (10.12) 使用 Xcode 9。

在 watchOS 1 中最初所需使用 Xcode 7 的 OS X Yosemite (10.10)。

> [!WARNING]
> [在 watchOS 1 中更新将不会接受 2018 年 4 月 1 日之后](https://developer.apple.com/news/?id=11162017a)。 将来的更新必须使用 watchOS 2 SDK 或更高版本;在 watchos 生成建议 4 SDK。

## <a name="project-structure"></a>项目结构

Watch 应用包含三个项目：

- **Xamarin.iOS iPhone 应用程序项目**-这是正常的 iPhone 项目，它可以是任何 Xamarin.iOS 模板。 监视应用和其扩展将被捆绑在该主项目内。

- **监视扩展项目**-此项包含 Watch 应用程序的代码（如控制器类）。

- **监视应用项目**-此项包含用户界面情节提要文件，其中包含用于监视应用的所有 UI 资源。

[监视工具包目录示例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)解决方案在 Xamarin.Studio 中如下所示：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](installation-images/catalog-solution.png "Visual Studio 中的解决方案")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "Visual Studio 中的解决方案")

-----

下载并运行[WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)示例开始。
从示例的屏幕，可[控件](~/ios/watchos/user-interface/index.md)页。


## <a name="creating-a-new-project"></a>创建新项目

您不能创建一个新"监视解决方案"...而是可以将监视应用程序添加到现有的 iOS 应用程序。 请按照以下步骤创建的监视应用操作：

1. 如果没有现有的项目，首先选择**文件 > 新的解决方案**并创建 iOS 应用程序 (例如，**单一视图应用**):

    [![](installation-images/cycle8-2-sml.png "选择文件 > 新的解决方案，并创建 iOS 应用程序")](installation-images/cycle8-2.png#lightbox)

2. 后创建的 iOS 应用 （或你打算使用现有的 iOS 应用程序），右键单击解决方案并选择**添加 > 添加新项目...** .在中**新的项目**窗口中选择**watchOS > 应用程序 > WatchKit 应用**:

    [![](installation-images/cycle8-6-sml.png "选择 watchOS > 应用程序 > WatchKit 应用")](installation-images/cycle8-6.png#lightbox)

3. 在下一屏幕中，您选择的 iOS 应用程序项目应包括 watch 应用：

    [![](installation-images/cycle8-7-sml.png "选择的 iOS 应用程序项目应包括 watch 应用")](installation-images/cycle8-7.png#lightbox)

4. 最后，选择要保存项目的位置 （和根据需要启用源代码管理）：

    [![](installation-images/cycle8-8-sml.png "选择要保存项目的位置")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio for Mac 会自动配置[项目引用和**Info.plist**设置](~/ios/watchos/get-started/project-references.md)为您。

## <a name="creating-the-watch-user-interface"></a>创建监视用户界面

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>使用 Xamarin iOS 设计器

Watch 应用上双击**Interface.storyboard**编辑使用 iOS 设计器。 您可以将界面控制器和 UI 控件拖到从情节提要**工具箱**并将其配置使用**属性**板：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "情节提要设计器中")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "情节提要设计器中")](installation-images/iosdesigner-vs.png#lightbox)

-----

应为每个新界面控制器**类**选择它，然后输入中的名称**属性**板 (这将创建所需C#代码隐藏文件自动):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "为每个新界面控制器提供一个类")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "为每个新界面控制器提供一个类")

-----

创建的 segue **Ctrl + 拖动**从到另一个界面控制器上的按钮、 表或界面控制器。


### <a name="using-xcode-on-the-mac"></a>在 Mac 上使用 Xcode

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

你可以继续使用 Xcode 生成的 Interface.storyboard 文件上右键单击并选择您的用户界面**打开方式 > Xcode Interface Builder**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 用户还可以使用 Xcode 切换要直接使用 Mac 生成主机生成用户界面。
在 Visual Studio for Mac 中打开你的解决方案然后右键单击 Interface.storyboard 文件并选择**打开方式 > Xcode Interface Builder**:

-----

![](installation-images/openwith-xcode.png "在 Xcode Interface Builder 中打开 Interface.storyboard")

如果使用 Xcode，然后应遵循与正常的监视应用的相同步骤[iOS 应用情节提要](~/ios/user-interface/storyboards/index.md)(如创建输出口和操作由**Ctrl + 拖动**到 **.h**标头文件)。

在您保存情节提要中将自动添加的 Xcode Interface Builder 的输出口和操作创建到C# **。 designer.cs**监视扩展项目中的文件。


### <a name="adding-additional-screens-in-xcode"></a>在 Xcode 中添加其他屏幕

当将其他屏幕 （超出什么模板中包括默认情况下） 添加到使用 Xcode Interface Builder 情节提要**必须手动添加C#代码文件**针对每个新界面控制器。

请参阅[如何将新界面控制器添加到情节提要上的高级说明](~/ios/watchos/troubleshooting.md#add)。

*Xamarin iOS 设计器自动执行此操作，任何手动步骤是必需的。*


## <a name="building"></a>生成

包含的监视应用的项目，生成与其他 iOS 项目一样。 生成过程会导致包含监视扩展 (.appex)，它包含无代码的监视应用程序 (.app) 的 iPhone 应用程序 (.app)。


## <a name="launching"></a>启动

您可以启动监视应用程序在模拟器中使用 Visual Studio for Mac 或 Visual Studio （Mac 生成主机上启动）。

有两种模式启动的 WatchKit 应用：

- 正常的应用程序模式 （默认值），并
- [通知](~/ios/watchos/platform/notifications.md)（这需要采用 JSON 格式的测试通知有效负载）。

### <a name="xcode-8-support"></a>Xcode 8 支持

安装 Xcode 8 （或更高版本） 后，Apple Watch 模拟器是独立于 iOS 模拟器 (与不同[Xcode 6](#xcode6)，其中它们显示为*外部显示器*)。
当您选择监视应用程序项目，并使其成为启动项目时，该模拟器列表将显示*iOS 模拟器*可供选择 （如下所示）。

[![](installation-images/xs-xcode8-watchos3-sml.png "选择模拟器类型")](installation-images/xs-xcode8-watchos3.png#lightbox)

在开始调试时,*两个*模拟器应启动的 iOS 模拟器*和*Apple Watch 模拟器。 使用**命令 + Shift + H**以导航到菜单和时钟表盘; 并使用**硬件**菜单中设置**Force Touch 压力**。 滚动鼠标的触控板上将模拟使用数字 Crown。

#### <a name="troubleshooting"></a>疑难解答

以下的错误将出现在**应用程序输出**如果尝试启动到不具有配对的观看的模拟器：

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

请参阅[Apple 的论坛](https://forums.developer.apple.com/thread/7783)有关配置模拟器，如果默认值不起作用的说明。


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 和 watchOS 1

必须进行 *监视扩展项目* **启动项目** 之前运行或调试应用程序。 无法"启动"watch 应用本身，并且如果您选择的 iOS 应用，它将启动 iOS 模拟器中作为普通。

默认情况下的监视应用开始在普通**应用程序**从 Visual Studio for Mac 的模式 （不快速或通知模式）**运行**或**调试**命令。

使用 Xcode 6 时 iPhone 5，iPhone 5 秒，iPhone 6 和 iPhone 6 Plus 可以激活任意一个外部显示器**Apple Watch-38mm**或**Apple Watch-42 mm**将监视应用程序的位置显示。

> [!NOTE]
> 请记住，使用 Xcode 6 时，"监视" 屏幕不会自动出现在 iOS 模拟器中。
> 使用**硬件 > 外部显示**菜单以显示监视屏幕。

<a name="custommodes" />

## <a name="launching-notification-mode"></a>启动通知模式

请参阅[通知页](~/ios/watchos/platform/notifications.md)信息如何处理代码中的通知。


Visual Studio for Mac 可以使用通知启动监视应用_启动模式_通知：



右键单击监视应用程序项目，然后选择**运行与 > 自定义配置...** :


[![](installation-images/runwith-customparams-sml.png "运行自定义配置")](installation-images/runwith-customparams.png#lightbox)


这将打开**自定义参数**可以在其中选择的窗口**通知**（并提供 JSON 有效负载），然后按**运行**若要在模拟器中启动监视应用程序：


[![](installation-images/runwith-execargs-sml.png "设置通知和有效负载")](installation-images/runwith-execargs.png#lightbox)



## <a name="debugging"></a>调试

在 Visual Studio for Mac 和 Visual Studio 支持调试。
请记住在通知模式下进行调试时提供的通知的 JSON 文件。 此屏幕截图显示调试断点命中在 watch 应用中：

![](installation-images/debug-sml.png "此屏幕截图显示调试断点被命中的监视应用")

启动说明后您将得到 watch 应用上运行**iOS 模拟器 （监视）** 。
对于通知模式，可以选择**调试 > 打开的系统日志**(**CMD + /** )，并使用`Console.WriteLine`在代码中。

### <a name="debugging-lifecycle-event-handlers"></a>调试生命周期事件处理程序

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

WatchOS 模板文件 (如`InterfaceController`， `ExtensionDelegate`， `NotificationController`，和`ComplicationController`) 附带已实现其所需的生命周期方法。 添加`Console.WriteLine`调用和读取**应用程序输出**以更好地了解事件生命周期。



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [第一个 Watch 应用视频](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple WatchKit 提示](https://developer.apple.com/watchkit/tips/)
