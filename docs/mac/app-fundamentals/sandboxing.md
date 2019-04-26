---
title: 沙盒处理 Xamarin.Mac 应用
description: 本文介绍如何在 App Store 上发布的 Xamarin.Mac 应用程序沙盒处理。 它介绍了所有进入如容器目录、 授权、 确定用户的权限、 权限分离和内核强制进行沙箱处理的元素。
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6bf2f63e944e178d80f76fe363ef24410ff052ce
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61236712"
---
# <a name="sandboxing-a-xamarinmac-app"></a>沙盒处理 Xamarin.Mac 应用

_本文介绍如何在 App Store 上发布的 Xamarin.Mac 应用程序沙盒处理。它介绍了所有进入如容器目录、 授权、 确定用户的权限、 权限分离和内核强制进行沙箱处理的元素。_

## <a name="overview"></a>概述

在处理时使用 C# 和.NET Xamarin.Mac 应用程序中，可以对沙盒应用程序的相同能力像使用 OBJECTIVE-C 或 Swift 时。

[![正在运行的应用程序示例](sandboxing-images/intro01.png "的正在运行的应用示例")](sandboxing-images/intro01-large.png#lightbox)

在本文中，我们将介绍使用沙盒处理 Xamarin.Mac 应用程序及其所有进入沙箱处理的元素中的基础知识： 容器目录、 授权、 确定用户的权限、 权限分离和内核强制。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性使用绑定于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

## <a name="about-the-app-sandbox"></a>有关应用沙盒

应用沙盒提供强防御恶意应用程序在 Mac 上运行的限制的访问权限的应用程序对系统资源可能会导致的损害。

非沙盒应用程序拥有的用户运行该应用程序的完全控制权限和可以访问或执行任何用户可以操作。 如果应用包含安全漏洞 （或正在使用任何框架），黑客可以可能利用这些弱点，并使用该应用程序控制 Mac 上运行。

通过限制对基于每个应用程序的资源的访问，沙盒应用程序提供了一系列针对被盗、 损坏或部分用户的计算机上运行的应用程序的恶意企图的防御措施。

应用沙盒是内置于提供两方面的策略的 macOS （在内核级别强制执行） 的访问控制技术：

1. 应用沙盒，开发人员来描述_如何_与操作系统以及在这种方式，应用程序将进行交互，它授予所需完成工作并没有更多的访问权限。
2. 应用沙盒允许用户无缝地授予进一步对通过打开系统和保存对话框中，拖放操作和其他常见的用户交互。

### <a name="preparing-to-implement-the-app-sandbox"></a>准备实现应用沙盒

将在本文中详细讨论应用沙盒的元素如下所示：

- 容器目录
- 权利
- 确定用户的权限
- 特权隔离
- 内核强制

了解这些详细信息后，你将能够为采用应用沙盒 Xamarin.Mac 应用程序中创建计划。

首先，需要确定你的应用程序是否适合进行沙盒处理 （大多数应用程序是）。 接下来，需要解决任何 API 不兼容问题和确定你将需要应用沙盒的哪些元素。 最后，看看使用特权种分离使您最大化应用程序的防御级别。

当采用应用沙盒，某些应用程序使用的文件系统位置会有所不同。 特别是，应用程序将具有一个将用于应用程序支持文件、 数据库、 缓存和不是用户文档的任何其他文件的容器目录。 MacOS 和 Xcode 提供支持，以将这些类型的文件从其旧位置迁移到容器。

## <a name="sandboxing-quick-start"></a>沙盒处理快速入门

在本部分中，我们将举例说明如何开始使用应用沙盒创建简单的 Xamarin.Mac 应用来使用 Web 视图 （它需要受到限制，在沙盒处理除非明确请求的网络连接）。

我们将验证应用程序进行实际沙箱处理，并了解如何排查和解决常见应用沙盒错误。

### <a name="creating-the-xamarinmac-project"></a>创建 Xamarin.Mac 项目

让我们执行以下操作来创建我们的示例项目：

1. 启动 Visual Studio for Mac，然后单击**新的解决方案...** 列表中。
2. 从**新的项目**对话框中，选择**Mac** > **应用** > **Cocoa 应用**: 

    [![创建新的 Cocoa 应用](sandboxing-images/sample01.png "创建新的 Cocoa 应用")](sandboxing-images/sample01-large.png#lightbox)
3. 单击**下一步**按钮，输入`MacSandbox`的项目名称，然后单击**创建**按钮： 

    [![输入应用名称](sandboxing-images/sample02.png "输入应用名称")](sandboxing-images/sample02-large.png#lightbox)
4. 在中**Solution Pad**，双击**Main.storyboard**文件以进行编辑在 Xcode 中打开： 

    [![编辑主要情节提要](sandboxing-images/sample03.png "编辑主要情节提要")](sandboxing-images/sample03-large.png#lightbox)
5. 拖动**Web 视图**到窗口中，将其大小以填充内容区域并将其增加和减少与窗口设置： 

    [![添加 web 视图](sandboxing-images/sample04.png "添加 web 视图")](sandboxing-images/sample04-large.png#lightbox)
6. 创建输出口调用的 web 视图`webView`: 

    [![创建新的电源插座](sandboxing-images/sample05.png "创建新的电源插座")](sandboxing-images/sample05-large.png#lightbox)
7. 返回到 Visual Studio for Mac 和双击**ViewController.cs**中的文件**Solution Pad**打开进行编辑。
8. 添加以下 using 语句： `using WebKit;`
9. 使`ViewDidLoad`方法看起来像以下： 

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();
    webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
}
```

10. 保存更改。

运行应用程序，并确保在 Apple 网站中显示了窗口，如下所示：

[![显示运行示例应用](sandboxing-images/sample06.png "显示示例应用程序运行")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>签名和预配应用程序

我们可以让应用沙盒之前，我们首先需要预配和我们 Xamarin.Mac 应用程序进行签名。

允许以下操作：

1. 登录到 Apple 开发人员门户： 

    [![登录到 Apple 开发人员门户](sandboxing-images/sign01.png "登录到 Apple 开发人员门户")](sandboxing-images/sign01-large.png#lightbox)
2. 选择**证书、 标识符和配置文件**: 

    [![选择证书、标识符和配置文件](sandboxing-images/sign02.png "选择证书、标识符和配置文件")](sandboxing-images/sign02-large.png#lightbox)
3. 下**Mac 应用**，选择**标识符**: 

    [![选择标识符](sandboxing-images/sign03.png "选择标识符")](sandboxing-images/sign03-large.png#lightbox)
4. 创建新的应用程序 ID: 

    [![创建新的应用 ID](sandboxing-images/sign04.png "创建新的应用 ID")](sandboxing-images/sign04-large.png#lightbox)
5. 下**预配配置文件**，选择**开发**: 

    [![选择开发](sandboxing-images/sign05.png "选择开发")](sandboxing-images/sign05-large.png#lightbox)
6. 创建新的配置文件，然后选择**Mac 应用程序开发**: 

    [![创建新的配置文件](sandboxing-images/sign06.png "创建新的配置文件")](sandboxing-images/sign06-large.png#lightbox)
7. 选择我们在上面创建的应用 ID: 

    [![选择应用 ID](sandboxing-images/sign07.png "选择应用 ID")](sandboxing-images/sign07-large.png#lightbox)
8. 选择此配置文件的开发人员： 

    [![添加开发人员](sandboxing-images/sign08.png "添加开发人员")](sandboxing-images/sign08-large.png#lightbox)
9. 选择此配置文件的计算机： 

    [![选择允许的计算机](sandboxing-images/sign09.png "选择允许的计算机")](sandboxing-images/sign09-large.png#lightbox)
10. 为配置文件提供一个名称： 

    [![为指定名称的配置文件](sandboxing-images/sign10.png "为配置文件提供一个名称")](sandboxing-images/sign10-large.png#lightbox)
11. 单击**完成**按钮。

> [!IMPORTANT]
> 在某些情况下可能需要直接从 Apple 开发人员门户下载新的预配配置文件，然后双击它以安装。 可能还需要停止并重启 Visual Studio for Mac 之前它将能够访问新的配置文件。

接下来，我们需要在开发计算机上加载新的应用程序 ID 和配置文件。 让我们执行以下操作：

1. 启动 Xcode，然后选择**首选项**从**Xcode**菜单： 

    ![编辑在 Xcode 中的帐户](sandboxing-images/sign11.png "编辑在 Xcode 中的帐户")
2. 单击**查看详细信息...** 按钮： 

    ![单击查看详细信息按钮](sandboxing-images/sign12.png "单击查看详细信息按钮")
3. 单击**刷新**按钮 （以较低的左下角）。
4. 单击**完成**按钮。

接下来，我们需要在我们的 Xamarin.Mac 项目中选择新的应用程序 ID 和预配配置文件。 让我们执行以下操作：

1. 在中**Solution Pad**，双击**Info.plist**文件将其打开进行编辑。
2. 絋粄**捆绑包标识符**与我们上面创建的应用 ID 匹配 (示例： `com.appracatappra.MacSandbox`): 

    [![编辑捆绑包标识符](sandboxing-images/sign13.png "编辑捆绑包标识符")](sandboxing-images/sign13-large.png#lightbox)
3. 接下来，双击**Entitlements.plist**文件，并确保我们**iCloud 键值存储**并**iCloud 容器**所有与我们上面创建的应用 ID 匹配 (示例：`com.appracatappra.MacSandbox`): 

    [![编辑 Entitlements.plist 文件](sandboxing-images/sign17.png "编辑 Entitlements.plist 文件")](sandboxing-images/sign17-large.png#lightbox)
3. 保存更改。
4. 在中**Solution Pad**，双击要打开其选项来编辑的项目文件：  

    ![Editign 解决方案的选项](sandboxing-images/sign14.png "Editign 解决方案的选项")
5. 选择**Mac 签名**，然后检查**登录应用程序捆绑包**并**安装程序包签名**。 下**预配配置文件**，选择我们在上面创建的一个： 

    ![设置预配配置文件](sandboxing-images/sign15.png "设置预配配置文件")
6. 单击**完成**按钮。

> [!IMPORTANT]
> 您可能需要退出并重新启动 Visual Studio for Mac，即可使用它，以识别新的应用程序 ID 和预配配置文件安装的 Xcode。

#### <a name="troubleshooting-provisioning-issues"></a>预配问题疑难解答

此时应尝试运行该应用程序，并确保所有内容已经过签名和正确地预配。 如果仍像以前一样运行的应用，所有内容是很好。 出现故障时，可能会收到类似于以下对话框：

[![预配问题对话框示例](sandboxing-images/sign16.png "预配问题对话框示例")](sandboxing-images/sign16-large.png#lightbox)

下面是预配和签名问题的最常见原因：

- 应用捆绑包 ID 与所选配置文件的应用 ID 不匹配。
- 开发人员 ID 与所选配置文件的开发人员 ID 不匹配。
- Mac 上进行测试的 UUID 未注册为所选配置文件的一部分。

如果出现问题，更正此问题在 Apple 开发人员门户、 刷新在 Xcode 中的配置文件和执行干净的生成，在 Visual Studio for mac。

### <a name="enable-the-app-sandbox"></a>启用应用沙盒

通过在项目选项中选择一个复选框启用应用沙盒。 请执行以下操作：

1. 在中**Solution Pad**，双击**Entitlements.plist**文件将其打开进行编辑。
2. 两项全选**启用的权利**并**启用应用程序沙盒**: 

    [![编辑权利和启用沙盒](sandboxing-images/sign17.png "编辑权利和启用沙盒处理")](sandboxing-images/sign17-large.png#lightbox)
3. 保存更改。

此时，已启用应用沙盒但尚未在 Web 视图提供所需的网络访问。 如果您运行应用程序现在，应会看到一个空白的窗口：

[![被阻止的 web 访问](sandboxing-images/sample08.png "被阻止的 web 访问")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>验证应用程序进行沙箱处理

除了阻塞行为的资源，有三个主要的方法可用于指示是否已成功沙盒 Xamarin.Mac 应用程序：

1. 在 Finder 中，检查的内容`~/Library/Containers/`文件夹-如果应用程序进行沙箱处理，将有类似应用的捆绑标识符名为的文件夹 (示例： `com.appracatappra.MacSandbox`): 

    [![打开应用的捆绑包](sandboxing-images/sample09.png "打开应用的捆绑包")](sandboxing-images/sample09-large.png#lightbox)
2. 系统将其视为活动监视器中沙盒应用程序：
    - 启动活动监视器 (下`/Applications/Utilities`)。 
    - 选择**视图** > **列**，并确保**沙盒**选中菜单项。
    - 确保沙盒列读取`Yes`为应用程序： 

    [![检查活动监视器中的应用程序](sandboxing-images/sample10.png "检查活动监视器中的应用程序")](sandboxing-images/sample10-large.png#lightbox)
3. 检查应用程序二进制进行沙箱处理：
    - 启动终端应用程序。
    - 导航到应用程序`bin`目录。
    - 发出以下命令： `codesign -dvvv --entitlements :- executable_path` (其中`executable_path`是你的应用程序的路径): 

    [![检查命令行上的应用](sandboxing-images/sample11.png "检查命令行上的应用程序")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>调试沙盒应用程序

调试器连接到 Xamarin.Mac 应用的整个 TCP，这意味着，默认情况下启用沙盒机制，时，无法连接到该应用程序，因此如果尝试运行该应用程序而无需启用适当的权限，你将收到一个错误 *"无法连接到调试器"*。 

[![设置所需的选项](sandboxing-images/debug01.png "设置所需的选项")](sandboxing-images/debug01-large.png#lightbox)

**允许传出网络连接 （客户端）** 权限是所需的调试器，使此将允许调试正常。 由于没有它进行调试时不能我们已更新`CompileEntitlements`目标进行`msbuild`为进行调试的沙箱处理的任何应用程序仅生成自动将该权限添加到权利。 发布版本应使用未修改的形式在权利文件中指定的权利。

### <a name="resolving-an-app-sandbox-violation"></a>解决了应用沙盒冲突

如果不显式允许应用程序尝试访问某个资源具有沙盒 Xamarin.Mac，会发生了应用沙盒冲突。 例如，Web 视图不再能够显示 Apple 网站。

为 Mac 在 Visual Studio 中指定的授权设置与应用程序要求不匹配时发生应用沙盒冲突的最常见的原因。 同样，回到本示例中，缺少网络连接工作保持 Web 视图的权利。

#### <a name="discovering-app-sandbox-violations"></a>发现应用沙盒冲突

如果您怀疑应用沙盒冲突发生在 Xamarin.Mac 应用程序中，发现问题的最快方法是使用**控制台**应用。

请执行以下操作：

1. 编译应用程序有问题，并从 Visual Studio 运行 for mac。
2. 打开**控制台**应用程序 (从`/Applications/Utilties/`)。
3. 选择**的所有消息**侧栏中，输入`sandbox`中搜索： 

    [![在控制台中的沙盒处理问题的示例](sandboxing-images/resolve01.png "举例说明在控制台中的沙盒处理问题")](sandboxing-images/resolve01-large.png#lightbox)

对于我们上面的示例应用，您可以看到正在阻止 Kernal`network-outbound`由于应用沙盒，因为我们不已请求该权限的流量。

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>修复冲突与权利相关的应用沙盒

现在，我们已了解如何查找应用沙盒处理冲突，让我们看如何可以通过调整我们的应用程序的权利解决它们。

请执行以下操作：

1. 在中**Solution Pad**，双击**Entitlements.plist**文件将其打开进行编辑。
2. 下**权利**部分中，选择**允许传出网络连接 （客户端）** 复选框： 

    [![编辑权利](sandboxing-images/sign17.png "编辑权利")](sandboxing-images/sign17-large.png#lightbox)
3. 将所做的更改保存到应用程序。

如果我们执行上述操作的示例应用，然后生成并运行它，web 内容将立即显示按预期方式。

## <a name="the-app-sandbox-in-depth"></a>深入了解应用沙盒

应用沙盒提供的访问控制机制是几个并易于理解。 但是，每个应用程序将采用应用沙盒的方法是唯一，并且基于应用的要求。

提供您最大程度地保护免受恶意代码被利用的 Xamarin.Mac 应用程序，需要一个漏洞中有任一应用程序 （或其使用的一个库或框架） 能够与应用程序的交互控制系统。

应用沙盒旨在防止接管 （或限制损害程度可能会导致） 通过允许您指定与系统的应用程序的预期的交互。 系统将仅授予应用程序完成其工作所需的资源而不安装其他访问权限。

应用沙盒的设计时，您正在设计的最糟糕的情形。 如果应用程序 does 被恶意代码受到安全威胁，它被限制为访问文件和应用程序的沙盒中的资源。

### <a name="entitlements-and-system-resource-access"></a>权利和系统资源访问权限

如上面看到的尚未沙盒的 Xamarin.Mac 应用程序授予完全控制权限和运行该应用程序的用户的访问。 如果恶意代码被攻破，未受保护的应用可以充当使用范围可能会带来危害范围恶意行为适用于的代理。

通过启用应用沙盒，除最少的一组特权，在删除然后重新启用按需限使用 Xamarin.Mac 应用的权利。 

通过编辑来修改应用程序的应用程序沙盒资源及其**Entitlements.plist**文件和检查，或选择编辑器下拉框从所需的权限：

[![编辑权利](sandboxing-images/sign17.png "编辑权利")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>容器目录和文件系统访问权限

当在 Xamarin.Mac 应用程序采用应用沙盒时，它有权访问以下位置：

- **应用容器目录**-首次运行时，操作系统将创建一个特殊_容器目录_其中所有资源转，只有它可以访问。 应用程序将具有对此目录的完全读/写访问权限。
- **应用组容器目录**-你的应用可以被授予访问权限的一个或多_的组容器_同一个组在应用之间共享的。
- **用户指定的文件**-应用程序会自动获得对文件的显式打开或拖放到该应用程序上由用户访问。
- **相关项**-与相应的授权，你的应用程序可以访问具有相同名称但不同的扩展名的文件。 例如，文档已保存为这两者`.txt`文件和一个`.pdf`。
- **临时目录、 命令行工具目录和世界上可读的特定位置**-您的应用程序中其他明确定义的位置由系统指定的文件具有不同程度的访问权限。

#### <a name="the-app-container-directory"></a>应用容器目录

Xamarin.Mac 应用程序的应用程序容器目录具有以下特征：

- 它是在用户的主目录中隐藏的位置 (通常`~Library/Containers`)，即可访问`NSHomeDirectory`应用程序中的函数 （见下文）。 因为它是在主目录中，每个用户将获得自己容器适用的应用。
- 应用程序对容器目录和所有子目录和文件都具有无限制读/写访问权限。
- MacOS 的路径查找 Api 的大部分都是相对于应用的容器。 例如，容器将具有其自己**库**(通过访问`NSLibraryDirectory`)，**应用程序支持**并**首选项**子目录。
- macOS 建立并强制执行之间的连接和应用和代码签名通过其容器。 甚至是另一个应用尝试欺骗应用程序使用其**捆绑包标识符**，它将不能访问容器，因为代码签名。
- 为用户生成的文件不是容器。 它是应用程序使用如数据库、 缓存或其他特定类型的数据的文件。
- 有关_shoebox_类型的应用 （如 Apple 的照片应用程序），用户的内容将转到容器。

> [!IMPORTANT]
> 遗憾的是，Xamarin.Mac 不具有 100 %api 覆盖范围尚未 （不同于 Xamarin.iOS)，因此`NSHomeDirectory`Xamarin.Mac 的当前版本中尚未映射 API。

作为临时的解决方法，可以使用以下代码：

```csharp
[System.Runtime.InteropServices.DllImport("/System/Library/Frameworks/Foundation.framework/Foundation")]
public static extern IntPtr NSHomeDirectory();

public static string ContainerDirectory {
    get {
        return ((NSString)ObjCRuntime.Runtime.GetNSObject(NSHomeDirectory())).ToString ();
        }
}
```

#### <a name="the-app-group-container-directory"></a>应用组容器目录

从 Mac macOS 10.7.5 （和更高版本），应用程序可以使用`com.apple.security.application-groups`授权访问普遍适用于组中的所有应用程序共享的容器。 对于非用户面向公众的内容，例如数据库或其他类型的支持文件 （例如缓存），可以使用此共享的容器。

组容器会自动添加到每个应用的沙盒容器 （如果它们是组的一部分） 并存储在`~/Library/Group Containers/<application-group-id>`。 组 ID_必须_开始使用你的开发团队 ID 和一个句点，例如：

```plist
<team-id>.com.company.<group-name>
```

有关详细信息，请参阅 Apple[添加到应用程序组的应用程序](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)中[权利键引用](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195)。

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>在应用容器外 Powerbox 和文件系统访问

沙盒的 Xamarin.Mac 应用程序可以按以下方式访问该容器以外的文件系统位置：

- 在用户 （通过打开和保存对话框或其他方法，例如拖放功能） 的特定方向。
- 通过为特定的文件系统位置使用权利 (如`/bin`或`/usr/lib`)。
* 在某些而言是完全可读 （例如共享） 的目录中时的文件系统位置。

_Powerbox_是 macOS 安全技术，与要展开沙盒的 Xamarin.Mac 应用的文件访问权限的用户进行交互。 Powerbox 不有任何 API，但该应用程序调用时，将以透明方式激活`NSOpenPanel`或`NSSavePanel`。 通过 Xamarin.Mac 应用程序设置的权利启用 Powerbox 访问。

在沙盒应用程序显示一个打开或保存对话框，窗口主讲 Powerbox （和不 AppKit），因此有权访问任何文件或目录的用户具有访问权限。

当用户选择文件或目录从打开或保存对话框 （或拖动到应用的图标上的任何一个） 时，Powerbox 的关联的路径添加到应用的沙盒中。

此外，系统会自动允许沙盒应用程序到以下：

- 连接到系统的输入方法。
- 调用服务从用户所选**Services**菜单 (仅为服务标记为_安全沙盒应用程序的_由服务提供商)。
- 通过从用户选择打开的文件**最近打开**菜单。
- 使用其他应用程序之间的复制和粘贴。
- 从世界上可读的以下位置读取文件：
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- 读取和写入文件中创建的目录`NSTemporaryDirectory`。

为默认值，文件打开或保存通过沙盒的 Xamarin.Mac 应用程序之前仍然能够访问该应用程序终止 （除非应用程序退出时仍处于打开该文件）。 打开的文件将自动还原到通过 macOS 恢复功能的应用程序的沙盒下次启动应用程序。

若要提供对位于外部 Xamarin.Mac 应用的容器的文件的持久性，使用安全作用域内的书签 （见下文）。

#### <a name="related-items"></a>相关的项

应用沙盒允许应用访问具有相同的文件名，但不同的扩展插件的相关的项。 该功能由两部分组成： a） 在应用的相关扩展的列表`Info.plst`文件，b） 代码告诉沙盒什么应用程序将执行与这些文件。

有两种方案，这样具有一定意义：

1. 应用程序需要能够保存该文件 （具有一个新的扩展） 的不同版本。 例如，导出`.txt`文件为`.pdf`文件。 若要处理这种情况下，必须使用`NSFileCoordinator`访问该文件。 你将调用`WillMove(fromURL, toURL)`方法首先，将文件移动到新的扩展插件，然后调用`ItemMoved(fromURL, toURL)`。
2. 应用程序需要使用不同的扩展插件中打开一个扩展名的主要文件和几个支持文件。 例如，电影和字幕文件。 使用`NSFilePresenter`才能访问辅助文件。 提供的主文件`PrimaryPresentedItemURL`属性和辅助文件到`PresentedItemURL`属性。 当打开主文件时，调用`AddFilePresenter`方法的`NSFileCoordinator`类，以注册辅助文件。

在这两种情况下，应用程序的**Info.plist**文件必须声明应用程序可以打开的文档类型。 对于任何文件类型，添加`NSIsRelatedItemType`(值为`YES`) 中的项到`CFBundleDocumentTypes`数组。

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>打开和保存对话框行为与沙盒应用程序

以下限制将置于`NSOpenPanel`和`NSSavePanel`沙盒的 Xamarin.Mac 应用中调用它们时：

- 不能以编程方式调用**确定**按钮。
- 您不能以编程方式更改中的用户的选择`NSOpenSavePanelDelegate`。

此外，以下继承修改将准备就绪：

- **非沙盒应用程序** - `NSOpenPanel` `NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>安全作用域内的书签和持久资源访问

如上面所述，沙盒 Xamarin.Mac 应用程序可以访问文件或通过直接用户交互 （如通过 PowerBox） 该容器以外的资源。 但是，这些资源的访问权限不会自动保留在应用启动或系统重新启动。

通过使用_Security-Scoped 书签_，沙盒 Xamarin.Mac 应用程序可以保留用户的意图和维护访问给定的资源应用程序后重新启动。

#### <a name="security-scoped-bookmark-types"></a>安全作用域内的书签类型

当使用 Security-Scoped 书签和持久资源访问权限，有两个 sistine 用例：

- **App-Scoped 书签提供永久访问权限的用户指定的文件或文件夹。** 

    例如，如果沙盒 Xamarin.Mac 应用程序允许使用打开进行编辑的外部文档 (使用`NSOpenPanel`)，该应用程序可以创建 App-Scoped 书签，以便它可以在以后再次访问同一文件。
- **Document-Scoped 书签提供子文件对一个特定文档永久访问权限。** 

例如，创建项目文件的视频编辑应用有权访问的各个图像、 视频剪辑和更高版本将合并到单个电影的声音文件。

当用户将资源文件导入到项目 (通过`NSOpenPanel`)，应用创建存储在项目中，项 Document-Scoped 书签，以便始终向应用程序可访问该文件。

可通过书签数据和文档本身可以打开任何应用程序解决 Document-Scoped 书签。 此项支持可移植性，允许用户将项目文件发送到另一个用户并使所有书签以及为其工作。

> [!IMPORTANT]
> Document-Scoped 书签可以_仅_指向单个文件而不是文件夹，该文件不能为在系统使用的位置 (如`/private`或`/Library`)。

#### <a name="using-security-scoped-bookmarks"></a>使用安全作用域内的书签

使用任一类型的 Security-Scoped 书签，要求您执行以下步骤：

1. **需要使用 Security-Scoped 书签的 Xamarin.Mac 应用中设置的相应权利**-设置 For App-Scoped 书签`com.apple.security.files.bookmarks.app-scope`权利的关键`true`。 对于 Document-Scoped 书签设置`com.apple.security.files.bookmarks.document-scope`权利的关键`true`。
2. **创建 Security-Scoped 书签**-将执行此操作的任何文件或文件夹的用户提供访问 (通过`NSOpenPanel`例如)，该应用将需要永久访问权限。 使用`public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)`方法的`NSUrl`类，以创建书签。
3. **解决 Security-Scoped 书签**-当应用程序需要 （例如，重启后） 访问资源再次时它将需要解析为一个安全作用域的 URL 的书签。 使用`public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)`方法的`NSUrl`类来解析书签。
4. **显式通知你想要从 Security-Scoped URL 访问到文件系统**-需要获取上述 Security-Scoped URL 之后立即执行此步骤或当您以后想要之后重新获取对资源的访问放弃您对其访问权限。 调用`StartAccessingSecurityScopedResource ()`方法的`NSUrl`类，开始访问 Security-Scoped URL。
5. **显式通知完成系统从 Security-Scoped URL 访问该文件**-越早越好，应通知系统，当应用程序不再需要文件访问权 （例如，如果用户将其关闭） 时。 调用`StopAccessingSecurityScopedResource ()`方法的`NSUrl`类，用以停止访问 Security-Scoped URL。

放弃对资源的访问后，你将需要返回到步骤 4，再次以重新建立访问权限。 如果重新启动 Xamarin.Mac 应用时，必须返回到步骤 3，并将书签重新解析。

> [!IMPORTANT]
> 发布 Security-Scoped URL 资源的访问权限失败将导致泄露的内核资源的 Xamarin.Mac 应用。 因此，应用将不再能够将文件系统位置添加到其容器，直到它重新启动。

### <a name="the-app-sandbox-and-code-signing"></a>应用沙盒和代码签名

启用应用沙盒并为 （通过权利） 的 Xamarin.Mac 应用中启用的特定要求后，您必须代码签名项目进行沙盒处理才会生效。 您必须执行代码签名，因为所需的应用程序沙盒的权利已链接到应用程序的签名。 

macOS 强制实施应用程序的容器和其代码签名，没有任何其他应用程序可以访问该容器，即使它欺骗应用捆绑包 id。 这种方式之间的链接 此机制的工作方式如下：

1. 当系统会创建应用程序的容器时，它会设置_访问控制列表_(ACL) 对该容器。 初始访问控制项列表中的包含的应用_指定要求_（灾难恢复），它描述了如何未来版本的应用程序可以 （当升级） 识别。
2. 每次具有相同的捆绑 ID 的应用启动时，系统会检查应用的代码签名与匹配指定一个容器的 ACL 中的条目中的指定要求。 如果系统没有找到匹配项，它会阻止应用启动。

代码签名的工作原理如下：

1. 创建 Xamarin.Mac 项目之前, 从 Apple 开发人员门户获取开发证书、 创建分发证书和开发人员 ID 证书。
2. Mac App Store 分发 Xamarin.Mac 应用，它使用 Apple 的代码签名处进行签名。

测试和调试时，您将使用已签名 （这将用于创建应用程序容器） 的 Xamarin.Mac 应用程序的版本。 更高版本，如果你想要测试或从 Apple 应用商店安装的版本，它将使用 Apple 签名进行签名，并将无法启动 （因为它不一定与原始应用程序容器相同的代码签名）。 在这种情况下，您将收到类似于以下的故障报告：

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

若要解决此问题，您将需要调整 ACL 条目以指向 Apple 签名版本的应用。

有关创建和下载所需的沙箱处理的预配配置文件的详细信息，请参阅[签名和预配应用](#Signing_and_Provisioning_the_App)上面一节。

#### <a name="adjusting-the-acl-entry"></a>调整 ACL 条目

若要允许 Apple 签名的版本的 Xamarin.Mac 应用运行，请执行以下操作：

1. 打开终端应用 (在`/Applications/Utilities`)。
2. 打开 Finder 窗口到 Xamarin.Mac 应用的 Apple 签名版本。
3. 类型`asctl container acl add -file `在终端窗口中。
4. 从 Finder 窗口 Xamarin.Mac 应用的图标拖放在终端窗口。
5. 该文件的完整路径将添加到终端中的命令。
6. 按**Enter**以执行该命令。

容器的 ACL 现在包含两个版本的 Xamarin.Mac 应用程序的指定的代码要求和 macOS 现在允许版本以运行。

#### <a name="display-a-list-of-acl-code-requirements"></a>显示 ACL 代码要求的列表

可以通过执行以下容器的 ACL 中查看一系列代码要求：

1. 打开终端应用 (在`/Applications/Utilities`)。
2. 键入 `asctl container acl list -bundle <container-name>`。
3. 按**Enter**以执行该命令。

`<container-name>`通常是 Xamarin.Mac 应用程序的捆绑包标识符。

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>应用沙盒设计 Xamarin.Mac 应用

没有为应用程序沙盒设计 Xamarin.Mac 应用时应遵循的常见工作流。 也就是说，在应用中实现沙箱处理的具体情况要对给定的应用的功能唯一的。

### <a name="six-steps-for-adopting-the-app-sandbox"></a>采用应用沙盒的六个步骤

通常为应用程序沙盒设计 Xamarin.Mac 应用包括以下步骤：

1. 确定应用程序是否适用于沙盒处理。
2. 设计开发和分发策略。
3. 解决任何 API 不兼容问题。
4. 适用于 Xamarin.Mac 项目所需的应用程序沙盒权利。
5. 添加使用 XPC 特权隔离。
6. 实现迁移策略。

> [!IMPORTANT]
> 必须不仅沙盒应用程序捆绑包，但也是每个包含的帮助器中的主可执行文件应用程序或该捆绑中的工具。 这需要从 Mac App Store 分发的任何应用程序并且，如果可能，应出于任何其他形式的应用分发。

有关 Xamarin.Mac 应用的捆绑包中的所有可执行二进制文件的列表，在终端中键入以下命令：

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

其中`[Your-App-Bundle]`是名称和应用程序的捆绑包的路径。

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>确定是否适用于沙盒处理 Xamarin.Mac 应用

大多数 Xamarin.Mac 应用程序是与应用沙盒完全兼容，因此，适用于沙盒处理。 如果应用需要应用沙盒不允许的行为，则应考虑一种替代方法。

如果您的应用程序需要以下行为之一，它与不兼容应用沙盒：

- **授权服务**-使用应用沙盒中，您不能处理这两个函数中所述[授权服务 C 参考](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826)。
- **辅助功能 Api** -无法沙盒辅助应用程序，例如屏幕阅读器或控制其他应用程序的应用。
- **将 Apple 事件发送到任意应用**-如果应用需要将 Apple 事件发送到未知的任意应用程序，它不能进行沙盒处理。 有关已知的被调用的应用列表，该应用程序仍可以进行沙盒处理和权利将必须包括被调用的应用列表。
- **将用户信息词典中分布式通知发送到其他任务**-不能包含与应用沙盒`userInfo`字典在发布到`NSDistributedNotificationCenter`消息传送的其他任务的对象。
- **加载内核扩展**的情况下应用沙盒禁止加载内核扩展。
- **模拟用户输入中打开和保存对话框**-以编程方式操作打开或保存对话框来模拟或更改用户输入禁止的应用沙盒。
- **访问或其他应用上设置首选项**-处理的其他应用设置禁止的应用沙盒。
- **配置网络设置**的情况下应用沙盒禁止操作网络设置。
- **终止其他应用**-应用沙盒禁止使用`NSRunningApplication`终止其他应用。

### <a name="resolving-api-incompatibilities"></a>解决 API 不兼容性

在设计 Xamarin.Mac 应用的应用沙盒时，可能会遇到与某些 macOS Api 的用法不兼容。

下面是一些常见的问题和可以如何解决这些问题的操作：

- **打开、 保存和跟踪文档**-如果您正在管理而不使用任何技术文档`NSDocument`，由于应用沙盒的内置支持应切换到它。 `NSDocument` 自动处理 PowerBox 并且使您的沙盒中的文档，如果用户将其移动在查找器中提供支持。
- **保留对文件系统资源的访问权限**-如果应用依赖于对该容器以外的资源的持续访问 Xamarin.Mac 使用安全作用域内的书签可继续访问。
- **为应用创建登录名项**-使用应用沙盒中，不能创建项使用的登录名`LSSharedFileList`也可以操作使用的启动服务的状态不`LSRegisterURL`。 使用`SMLoginItemSetEnabled`函数苹果中所述[添加使用服务管理框架的登录名项](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1)文档。
- **访问用户数据**-如果使用 POSIX 函数如`getpwuid`若要从目录服务中获取用户的主目录，请考虑使用 Cocoa 或核心基础符号如`NSHomeDirectory`。
- **访问其他应用首选项的**-由于应用沙盒将定向到应用的容器路径查找 Api，修改该容器中的首选项所需位置和访问中的另一个应用程序首选项不允许。 
- **在 Web 视图中使用 HTML5 嵌入视频**-如果 Xamarin.Mac 应用使用 WebKit 播放 HTML5 视频嵌入，则必须链接程序针对 AV Foundation 框架。 应用沙盒将阻止 CoreMedia play 否则为这些视频。

### <a name="applying-required-app-sandbox-entitlements"></a>将应用所需的应用沙盒权利

将需要编辑为你想要在应用程序沙盒中运行，并检查任何 Xamarin.Mac 应用程序的权利**启用应用程序沙盒**复选框。

基于应用的功能，您可能需要启用其他访问操作系统功能或资源的授权。 应用沙盒处理效果就越好最大程度减少请求运行您的应用程序所需的最低限度的权利因此只是随机启用的权利。

若要确定 Xamarin.Mac 应用所需的权利，请执行以下操作：

1. 启用应用沙盒并运行 Xamarin.Mac 应用。
2. 运行应用的功能。
3. 打开控制台应用 (在可用`/Applications/Utilities`) 并查找`sandboxd`中的冲突**的所有消息**日志。
4. 每个`sandboxd`冲突，解决此问题是通过在应用程序容器而不是其他文件系统位置或应用应用沙盒权利，若要允许访问受限制的操作系统功能。
5. 重新运行并再次测试 Xamarin.Mac 应用的所有功能。
6. 直到所有重复`sandboxd`冲突已解决。

### <a name="add-privilege-separation-using-xpc"></a>添加使用 XPC 特权隔离

在开发时 Xamarin.Mac 应用的应用沙盒中的术语特权和访问权限，看看应用的行为，然后请考虑将高风险操作划分其自己的 XPC 服务。

有关详细信息，请参阅 Apple[创建 XPC 服务](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6)并[守护程序和服务编程指南](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)。

### <a name="implement-a-migration-strategy"></a>实现迁移策略

如果发布新的沙盒版本不是以前沙盒的 Xamarin.Mac 应用程序，你将需要确保当前用户拥有一个平滑的升级路径。 

 有关如何实现容器迁移清单的详细信息，请阅读 Apple[将应用迁移到沙盒](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1)文档。

## <a name="summary"></a>总结

本文已详细的介绍了沙盒处理 Xamarin.Mac 应用程序。 首先，我们创建一个只需 Xamarin.Mac 应用以显示应用沙盒的基础知识。 接下来，我们介绍了如何解决沙盒冲突。 然后，我们采取了深入查看应用沙盒，并最后，我们了解了有关应用沙盒设计 Xamarin.Mac 应用。



## <a name="related-links"></a>相关链接

- [发布到 App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [有关应用沙盒](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [应用程序沙盒常见问题](https://developer.apple.com/library/content/qa/qa1773/_index.html)
