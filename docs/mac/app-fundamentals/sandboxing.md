---
title: 对 Xamarin 应用程序进行沙箱处理
description: 本文介绍如何对应用商店上的发布进行沙箱处理。 它涵盖了进入沙盒的所有元素, 例如容器目录、授权、用户确定的权限、特权分离和内核强制。
ms.prod: xamarin
ms.assetid: 06A2CA8D-1E46-410F-8C31-00EA36F0735D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 8379b9c9575c5a4f24f6c35c37cf8682e53b78ec
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121130"
---
# <a name="sandboxing-a-xamarinmac-app"></a>对 Xamarin 应用程序进行沙箱处理

_本文介绍如何对应用商店上的发布进行沙箱处理。它涵盖了进入沙盒的所有元素, 例如容器目录、授权、用户确定的权限、特权分离和内核强制。_

## <a name="overview"></a>概述

在 Xamarin Mac C#应用程序中使用和 .net 时, 与使用目标 C 或 Swift 时一样, 可以对应用程序进行沙箱处理。

[![正在运行的应用的示例](sandboxing-images/intro01.png "正在运行的应用的示例")](sandboxing-images/intro01-large.png#lightbox)

在本文中, 我们将介绍在 Xamarin 和 Mac 应用程序中使用沙盒的基本知识, 以及进入沙盒的所有元素: 容器目录、授权、用户确定的权限、特权分离和内核强制。 强烈建议您先完成[Hello, Mac](~/mac/get-started/hello-mac.md)一文, 特别是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)及[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分的简介, 因为它涵盖了我们将在本文。

你可能想要查看[Xamarin 内部](~/mac/internals/how-it-works.md)示例文档的 " `Register` [向目标公开C#类/方法](~/mac/internals/how-it-works.md)" 部分, 并说明用于将C#类连接到的和`Export`特性。目标-C 对象和 UI 元素。

## <a name="about-the-app-sandbox"></a>关于应用沙盒

应用沙盒提供强大的防御措施, 可以防止应用程序对系统资源的访问权限, 从而防止恶意应用程序在 Mac 上运行。

非沙箱应用程序具有运行应用程序的用户的完全权限, 并且可以访问或执行用户可以执行的任何操作。 如果应用包含安全漏洞 (或其使用的任何框架), 黑客可以利用这些漏洞, 并使用该应用控制运行它的 Mac。

通过在每个应用程序的基础上限制对资源的访问, 经过沙箱处理的应用程序可针对用户计算机上运行的应用程序的部分, 提供一系列防御能力、损坏或恶意意向。

应用沙盒是内置于 macOS 中的一种访问控制技术 (在内核级别强制执行), 提供了两个策略:

1. 应用程序沙箱使开发人员可以描述应用程序将_如何_与操作系统交互, 并以这种方式仅向其授予完成工作所需的访问权限, 而不是更多。
2. 应用沙箱允许用户通过 "打开" 和 "保存" 对话框、"拖放" 操作以及其他常见用户交互, 无缝授予对系统的进一步访问权限。

### <a name="preparing-to-implement-the-app-sandbox"></a>准备实现应用沙盒

本文详细讨论的应用沙箱的元素如下所示:

- 容器目录
- 下方
- 用户确定的权限
- 特权分离
- 内核强制

了解这些详细信息后, 便可以创建一个计划, 用于在 Xamarin 应用程序中采用应用沙盒。

首先, 您需要确定您的应用程序是否适用于沙箱处理 (大多数应用程序都是)。 接下来, 你将需要解决任何 API 不兼容问题, 并确定你将需要哪些应用沙盒元素。 最后, 了解如何使用特权分离来最大程度地提高应用程序的防御级别。

采用应用沙盒时, 应用程序使用的某些文件系统位置会有所不同。 特别是, 你的应用程序将有一个容器目录, 该目录将用于应用程序支持文件、数据库、缓存以及不是用户文档的任何其他文件。 MacOS 和 Xcode 都支持将这些类型的文件从其旧位置迁移到容器。

## <a name="sandboxing-quick-start"></a>沙盒快速入门

在本部分中, 我们将创建一个简单的 Xamarin 应用程序, 该应用程序使用 Web 视图 (需要网络连接, 除非特别请求, 除非特别要求) 作为应用沙盒入门的示例。

我们将验证应用程序是否已进行了沙盒处理并了解了如何排查并解决常见的应用沙箱错误。

### <a name="creating-the-xamarinmac-project"></a>创建 Xamarin. Mac 项目

让我们执行以下操作来创建示例项目:

1. 开始 Visual Studio for Mac 然后单击 "**新建解决方案"。** 列表中。
2. 在 "**新建项目**" 对话框中, 选择 " **Mac** > **app** > **Cocoa 应用**": 

    [![创建新的 Cocoa 应用](sandboxing-images/sample01.png "创建新的 Cocoa 应用")](sandboxing-images/sample01-large.png#lightbox)
3. 单击 "**下一步**" `MacSandbox`按钮, 输入作为项目名称, 然后单击 "**创建**" 按钮: 

    [![输入应用程序名称](sandboxing-images/sample02.png "输入应用程序名称")](sandboxing-images/sample02-large.png#lightbox)
4. 在**Solution Pad**中, 双击 Xcode 文件以将其打开, 以便在中进行编辑: 

    [![编辑主情节提要](sandboxing-images/sample03.png "编辑主情节提要")](sandboxing-images/sample03-large.png#lightbox)
5. 将**Web 视图**拖到窗口上, 调整其大小以填充内容区域, 并将其设置为随窗口一起放大和缩小: 

    [![添加 web 视图](sandboxing-images/sample04.png "添加 web 视图")](sandboxing-images/sample04-large.png#lightbox)
6. 创建名`webView`为的 web 视图的插座: 

    [![创建新插座](sandboxing-images/sample05.png "创建新插座")](sandboxing-images/sample05-large.png#lightbox)
7. 返回 Visual Studio for Mac, 然后双击**Solution Pad**中的**ViewController.cs**文件, 将其打开进行编辑。
8. 添加以下 using 语句:`using WebKit;`
9. `ViewDidLoad`使方法类似于以下内容: 

    ```csharp
    public override void AwakeFromNib ()
    {
        base.AwakeFromNib ();
        webView.MainFrame.LoadRequest(new NSUrlRequest(new NSUrl("http://www.apple.com")));
    }
    ```

10. 保存更改。

运行应用程序, 并确保在窗口中显示 Apple 网站, 如下所示:

[![显示应用运行示例](sandboxing-images/sample06.png "显示应用运行示例")](sandboxing-images/sample06-large.png#lightbox)

<a name="Signing_and_Provisioning_the_App" />

### <a name="signing-and-provisioning-the-app"></a>对应用进行签名和预配

我们首先需要对 Xamarin 应用程序进行预配并对其进行签名, 然后才能启用应用沙盒。

让我们执行以下操作:

1. 登录到 Apple 开发人员门户: 

    [![登录 Apple 开发人员门户](sandboxing-images/sign01.png "登录 Apple 开发人员门户")](sandboxing-images/sign01-large.png#lightbox)
2. 选择**证书, 标识符 & 配置文件**: 

    [![选择证书、标识符和配置文件](sandboxing-images/sign02.png "选择证书、标识符和配置文件")](sandboxing-images/sign02-large.png#lightbox)
3. 在 " **Mac 应用**" 下, 选择 "**标识符**": 

    [![选择标识符](sandboxing-images/sign03.png "选择标识符")](sandboxing-images/sign03-large.png#lightbox)
4. 为应用程序创建新 ID: 

    [![创建新的应用 ID](sandboxing-images/sign04.png "创建新的应用 ID")](sandboxing-images/sign04-large.png#lightbox)
5. 在 "**预配配置文件**" 下, 选择 "**开发**: 

    [![选择开发](sandboxing-images/sign05.png "选择开发")](sandboxing-images/sign05-large.png#lightbox)
6. 创建新的配置文件并选择 " **Mac 应用开发**": 

    [![创建新的配置文件](sandboxing-images/sign06.png "创建新的配置文件")](sandboxing-images/sign06-large.png#lightbox)
7. 选择前面创建的应用 ID: 

    [![选择应用 ID](sandboxing-images/sign07.png "选择应用 ID")](sandboxing-images/sign07-large.png#lightbox)
8. 选择此配置文件的开发人员: 

    [![添加开发人员](sandboxing-images/sign08.png "添加开发人员")](sandboxing-images/sign08-large.png#lightbox)
9. 选择此配置文件的计算机: 

    [![选择允许的计算机](sandboxing-images/sign09.png "选择允许的计算机")](sandboxing-images/sign09-large.png#lightbox)
10. 为配置文件命名: 

    [![为配置文件命名](sandboxing-images/sign10.png "为配置文件命名")](sandboxing-images/sign10-large.png#lightbox)
11. 单击 "**完成**" 按钮。

> [!IMPORTANT]
> 在某些情况中, 你可能需要直接从 Apple 开发人员门户下载新的预配配置文件, 然后双击该配置文件进行安装。 你可能还需要停止并重新启动 Visual Studio for Mac, 然后它才能访问新的配置文件。

接下来, 需要在开发计算机上加载新的应用 ID 和配置文件。 让我们执行以下操作:

1. 从 " **Xcode** " 菜单中, 启动 Xcode 并选择 "**首选项**": 

    ![编辑 Xcode 中的帐户](sandboxing-images/sign11.png "编辑 Xcode 中的帐户")
2. 单击 "**查看详细信息 ...** " 按钮: 

    ![单击 "查看详细信息" 按钮](sandboxing-images/sign12.png "单击 \"查看详细信息\" 按钮")
3. 单击 "**刷新**" 按钮 (位于左下角)。
4. 单击 "**完成**" 按钮。

接下来, 需要在 Xamarin Mac 项目中选择新的应用 ID 和预配配置文件。 让我们执行以下操作:

1. 在**Solution Pad**中, 双击**info.plist**文件以将其打开以进行编辑。
2. 确保**捆绑标识符**与我们之前创建的应用 ID 相匹配 (例如: `com.appracatappra.MacSandbox`): 

    [![编辑捆绑标识符](sandboxing-images/sign13.png "编辑捆绑标识符")](sandboxing-images/sign13-large.png#lightbox)
3. 接下来, 双击**info.plist**文件, 并确保**Icloud 密钥值存储**和**icloud 容器**均与我们之前创建的应用 ID 匹配 (例如: `com.appracatappra.MacSandbox`): 

    [![编辑 info.plist 文件](sandboxing-images/sign17.png "编辑 info.plist 文件")](sandboxing-images/sign17-large.png#lightbox)
4. 保存更改。
5. 在**Solution Pad**中, 双击项目文件以打开其编辑选项:  

    ![Editign 解决方案的选项](sandboxing-images/sign14.png "Editign 解决方案的选项")
6. 选择 " **Mac 签名**", 然后选中 **"对应用程序捆绑进行签名**并**对安装程序包签名"** 。 在 "**预配配置文件**" 下, 选择上面创建的配置文件: 

    ![设置预配配置文件](sandboxing-images/sign15.png "设置预配配置文件")
7. 单击 "**完成**" 按钮。

> [!IMPORTANT]
> 可能必须退出并重新启动 Visual Studio for Mac, 才能识别 Xcode 安装的新应用 ID 和预配配置文件。

#### <a name="troubleshooting-provisioning-issues"></a>预配问题疑难解答

此时, 你应尝试运行应用程序, 并确保已正确签名和预配所有内容。 如果应用仍像以前一样运行, 一切都很好。 发生故障时, 可能会看到如下所示的对话框:

[![示例设置问题对话框](sandboxing-images/sign16.png "示例设置问题对话框")](sandboxing-images/sign16-large.png#lightbox)

下面是设置和签名问题的最常见原因:

- 应用程序包 ID 与所选配置文件的应用 ID 不匹配。
- 开发人员 ID 与所选配置文件的开发人员 ID 不匹配。
- 正在进行测试的 Mac 的 UUID 未注册为所选配置文件的一部分。

如果出现问题, 请更正 Apple 开发人员门户中的问题, 刷新 Xcode 中的配置文件, 并 Visual Studio for Mac 中执行干净的生成。

### <a name="enable-the-app-sandbox"></a>启用应用沙盒

通过在项目选项中选择一个复选框来启用应用沙盒。 请执行以下操作：

1. 在**Solution Pad**中, 双击**info.plist**文件以将其打开以进行编辑。
2. 选中 "**启用权利**和**启用应用沙盒**": 

    [![编辑权利和启用沙盒](sandboxing-images/sign17.png "编辑权利和启用沙盒")](sandboxing-images/sign17-large.png#lightbox)
3. 保存更改。

此时, 你已启用应用沙盒, 但尚未提供 Web 视图所需的网络访问权限。 如果现在运行应用程序, 应会看到一个空白窗口:

[![显示被阻止的 web 访问](sandboxing-images/sample08.png "显示被阻止的 web 访问")](sandboxing-images/sample08-large.png#lightbox)

### <a name="verifying-that-the-app-is-sandboxed"></a>验证应用是否已进行沙盒处理

除了资源阻止行为以外, 有三种主要方法可用于判断是否已成功对 Xamarin 应用程序进行沙盒处理:

1. 在 "查找器" 中, 检查`~/Library/Containers/`文件夹的内容-如果应用已进行沙盒处理, 则会有一个名为的文件夹, 如应用的`com.appracatappra.MacSandbox`捆绑包标识符 (示例:): 

    [![打开应用程序的捆绑包](sandboxing-images/sample09.png "打开应用程序的捆绑包")](sandboxing-images/sample09-large.png#lightbox)
2. 系统在活动监视器中将应用视为沙盒:
    - 启动活动监视器 (在`/Applications/Utilities`下)。 
    - 选择 "**查看** > **列**", 并确保已选中 "**沙盒**" 菜单项。
    - 确保沙箱列读取`Yes`应用程序: 

    [![在活动监视器中检查应用](sandboxing-images/sample10.png "在活动监视器中检查应用")](sandboxing-images/sample10-large.png#lightbox)
3. 检查应用二进制文件是否已沙箱化:
    - 启动终端应用。
    - 导航到 "应用`bin`程序" 目录。
    - 发出此命令: `codesign -dvvv --entitlements :- executable_path` (其中`executable_path`是应用程序的路径): 

    [![在命令行上检查应用](sandboxing-images/sample11.png "在命令行上检查应用")](sandboxing-images/sample11-large.png#lightbox)

### <a name="debugging-a-sandboxed-app"></a>调试沙盒应用

调试器通过 TCP 连接到 Xamarin 应用程序, 这意味着在默认情况下启用沙盒时, 它无法连接到应用, 因此, 如果你尝试在未启用适当权限的情况下运行应用, 则会收到错误 *"无法连接到调试器"* . 

[![设置所需选项](sandboxing-images/debug01.png "设置所需选项")](sandboxing-images/debug01-large.png#lightbox)

"**允许传出网络连接 (客户端)** " 权限是调试程序所必需的, 启用此项将允许正常调试。 由于你无法在不进行调试的情况下进行`CompileEntitlements`调试, `msbuild`因此我们已将目标更新为, 以将该权限自动添加到仅针对调试版本进行沙盒处理的任何应用的权利。 发布版本应使用未修改的权利文件中指定的权利。

### <a name="resolving-an-app-sandbox-violation"></a>解决应用沙盒冲突

如果已沙盒的 Xamarin Mac 应用程序尝试访问尚未显式允许的资源, 则会发生应用沙盒冲突。 例如, Web 视图将不再能够显示 Apple 网站。

当 Visual Studio for Mac 中指定的权利设置与应用程序的要求不匹配时, 会发生应用沙盒冲突的最常见来源。 同样, 返回到我们的示例, 这是使 Web 视图不起作用的缺少的网络连接权利。

#### <a name="discovering-app-sandbox-violations"></a>发现应用沙盒冲突

如果你怀疑你的 Xamarin 应用程序中发生了应用沙盒冲突, 发现问题的最快方法是使用**控制台**应用。

请执行以下操作：

1. 编译有问题的应用程序并从 Visual Studio for Mac 运行该应用程序。
2. 打开**控制台**应用程序 (从`/Applications/Utilties/`)。
3. 选择侧栏中的**所有消息**, `sandbox`并在搜索中输入: 

    [![控制台中的沙盒处理问题的示例](sandboxing-images/resolve01.png "控制台中的沙盒处理问题的示例")](sandboxing-images/resolve01-large.png#lightbox)

对于上面的示例应用, 可以看到内核由于应用沙盒而阻止`network-outbound`流量, 因为我们尚未请求该权限。

#### <a name="fixing-app-sandbox-violations-with-entitlements"></a>通过权利解决应用沙盒冲突

现在, 我们已经了解了如何查找应用的沙箱冲突, 接下来让我们了解如何通过调整应用程序的权利来解决它们。

请执行以下操作：

1. 在**Solution Pad**中, 双击**info.plist**文件以将其打开以进行编辑。
2. 在 "**权利**" 部分下, 选中 "**允许传出网络连接 (客户端)** " 复选框: 

    [![编辑权利](sandboxing-images/sign17.png "编辑权利")](sandboxing-images/sign17-large.png#lightbox)
3. 保存对应用程序所做的更改。

如果我们对示例应用执行上述操作, 然后生成并运行它, web 内容现在将按预期方式显示。

## <a name="the-app-sandbox-in-depth"></a>应用程序沙箱深入了解

应用程序沙箱提供的访问控制机制很少, 易于理解。 但是, 每个应用程序采用的应用沙盒的使用方式是唯一的, 并根据应用的要求。

由于你尽力保护 Xamarin 应用程序免受恶意代码的攻击, 因此在应用程序 (或它所使用的一个库或框架) 中只需要一个漏洞才能控制应用程序与主板.

应用沙盒旨在通过允许你指定应用程序与系统的预期交互, 来防止接管 (或限制可能导致的损坏)。 系统仅授予对应用程序完成工作所需的资源的访问权限。

设计应用沙盒时, 您正在设计最糟糕的情况。 如果应用程序由于恶意代码而遭到破坏, 则限制为仅访问应用程序沙盒中的文件和资源。

### <a name="entitlements-and-system-resource-access"></a>权利和系统资源访问

如前文所述, 未进行沙盒处理的 Xamarin 应用程序被授予了运行该应用程序的用户的完全权限和访问权限。 如果恶意代码受到侵害, 则非保护的应用程序可以充当恶意行为的代理, 其范围可能会造成损害。

通过启用应用程序沙箱, 你可以删除除一组最少的特权之外的所有权限, 然后只需使用你的 Xamarin 应用的权利即可只需重新启用。 

您可以通过编辑应用程序的**info.plist**文件并检查或从编辑器下拉框中选择所需权限来修改应用程序的应用程序沙箱资源:

[![编辑权利](sandboxing-images/sign17.png "编辑权利")](sandboxing-images/sign17-large.png#lightbox)

### <a name="container-directories-and-file-system-access"></a>容器目录和文件系统访问

当你的 Xamarin 应用程序采用应用沙盒时, 它可以访问以下位置:

- **应用容器目录**-首次运行时, 操作系统会创建一个特殊的_容器目录_, 其中的所有资源都可以访问。 应用对此目录具有完全读/写访问权限。
- **应用组容器目录**-可以向应用授予对同一组中的应用共享的一个或多个_组容器_的访问权限。
- **用户指定的文件**-您的应用程序会自动获取对用户显式打开或拖放到应用程序中的文件的访问权限。
- **相关项**-对于相应的权利, 你的应用程序可以访问具有相同名称但扩展名不同的文件。 例如, 已另存为`.txt`文件和的`.pdf`文档。
- **临时目录、命令行工具目录和特定于全球的位置**-您的应用程序对系统指定的其他定义正确的位置中的文件具有不同的访问级别。

#### <a name="the-app-container-directory"></a>应用容器目录

Xamarin 应用程序的应用容器目录具有以下特征:

- 它位于用户的主目录 (通常`~Library/Containers`为) 中的隐藏位置, 并且可以通过应用程序内的`NSHomeDirectory`函数 (见下文) 访问。 因为它位于主目录中, 所以每个用户都将获得自己的应用容器。
- 应用对容器目录及其内的所有子目录和文件具有无限制读/写访问权限。
- 大多数 macOS 路径查找 Api 都是相对于应用程序的容器。 例如, 容器将有自己的**库**(通过`NSLibraryDirectory`访问)、**应用程序支持**和**首选项**子目录。
- macOS 通过代码签名建立和应用及其容器之间的连接。 即使另一个应用程序尝试使用其**捆绑标识符**来欺骗应用程序, 它也不能访问容器, 因为代码签名。
- 容器不用于用户生成的文件。 而是用于应用程序使用的文件, 如数据库、缓存或其他特定类型的数据。
- 对于_shoebox_类型的应用 (如 Apple 的照片应用), 用户的内容将进入容器。

> [!IMPORTANT]
> 遗憾的是, xamarin 不具有 100% API 覆盖面 (与 Xamarin 不同), 因此, 此`NSHomeDirectory` api 尚未在当前版本的 Xamarin 中进行映射。

作为临时解决方法, 可以使用以下代码:

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

从 Mac macOS 10.7.5 (及更高版本) 开始, 应用程序可以`com.apple.security.application-groups`使用权利访问组中的所有应用程序所共有的共享容器。 可以将此共享容器用于非用户的内容, 如数据库或其他类型的支持文件 (如缓存)。

组容器自动添加到每个应用的沙箱容器 (如果它们是组的一部分), 并存储在`~/Library/Group Containers/<application-group-id>`中。 组 ID_必须_以开发团队 id 和句点开头, 例如:

```plist
<team-id>.com.company.<group-name>
```

有关详细信息, 请参阅 Apple[将应用程序添加到](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)授权[密钥参考](https://developer.apple.com/library/prerelease/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/AboutEntitlements.html#//apple_ref/doc/uid/TP40011195)中的应用程序组。

#### <a name="powerbox-and-file-system-access-outside-of-the-app-container"></a>应用程序容器外的 Powerbox 和文件系统访问

沙盒中的 Xamarin 应用程序可以通过以下方式访问其容器外的文件系统位置:

- 按用户的特定方向 (通过 "打开" 和 "保存" 对话框或 "拖放" 等方法)。
- 使用特定文件系统位置 (如`/bin`或`/usr/lib`) 的权利。
- 当文件系统位置位于可读的特定目录中时 (例如, 共享)。

_Powerbox_是 macOS 的安全技术, 可与用户进行交互, 以扩展你的沙盒 Xamarin 应用程序的文件访问权限。 Powerbox 没有 API, 但会在应用调用`NSOpenPanel`或`NSSavePanel`时以透明方式激活。 可以通过为 Xamarin Mac 应用程序设置的权利启用 Powerbox 访问。

当沙盒应用显示 "打开" 或 "保存" 对话框时, 窗口由 Powerbox (而不是 AppKit) 提供, 因此可以访问用户有权访问的任何文件或目录。

当用户从 "打开" 或 "保存" 对话框中选择文件或目录 (或将其拖动到应用的图标上) 时, Powerbox 会将关联的路径添加到应用的沙箱中。

此外, 系统会自动将以下内容自动用于沙盒应用:

- 与系统输入法的连接。
- 从 "**服务**" 菜单调用用户选择的服务 (仅适用于服务提供商标记为 "安全" 的_沙箱应用_的服务)。
- 从 "**最近打开**" 菜单中打开用户选择的文件。
- 在其他应用程序之间使用 "复制 & 粘贴"。
- 从以下世界上可读的位置读取文件:
    - `/bin`
    - `/sbin`
    - `/usr/bin`
    - `/usr/lib`
    - `/usr/sbin`
    - `/usr/share`
    - `/System`
- 在创建`NSTemporaryDirectory`的目录中读取和写入文件。

默认情况下, 沙盒 Xamarin 应用程序打开或保存的文件将保持可访问状态, 直到应用程序终止 (除非该文件在应用程序退出时仍处于打开状态)。 在下次启动应用程序时, 将通过 macOS Resume 功能自动将打开的文件还原到应用程序的沙盒。

若要为位于 Xamarin 应用容器外的文件提供持久性, 请使用安全作用域的书签 (见下文)。

#### <a name="related-items"></a>相关项

应用沙箱允许应用访问具有相同文件名但扩展名不同的相关项目。 此功能包含两部分: a) 应用程序`Info.plst`文件中的相关扩展的列表, b) 代码, 告诉沙箱应用程序将对这些文件执行哪些操作。

这种情况有意义:

1. 应用程序需要能够保存不同版本的文件 (具有新扩展名)。 例如, `.txt` `.pdf`将文件导出到文件。 若要处理这种情况, 必须使用`NSFileCoordinator`来访问该文件。 首先调用`WillMove(fromURL, toURL)`方法, 将文件移到新的扩展, 然后调用`ItemMoved(fromURL, toURL)`。
2. 应用需要打开一个主文件, 其中包含一个扩展名和多个具有不同扩展名的支持文件。 例如电影和字幕文件。 `NSFilePresenter`使用获取辅助文件的访问权限。 向属性提供主文件`PrimaryPresentedItemURL` , 将辅助文件`PresentedItemURL`提供给属性。 打开主文件后, 调用`AddFilePresenter` `NSFileCoordinator`类的方法来注册辅助文件。

在这两种情况下, 应用的**info.plist**文件必须声明应用可打开的文档类型。 对于任何文件类型, 请将`NSIsRelatedItemType`的 ( `YES`值为) 添加`CFBundleDocumentTypes`到数组中的项。

#### <a name="open-and-save-dialog-behavior-with-sandboxed-apps"></a>打开并保存带有沙盒应用的对话框行为

以下限制位于上`NSOpenPanel` , 并`NSSavePanel`在从沙盒 Xamarin 应用程序中调用它们时使用:

- 不能以编程方式调用 **"确定"** 按钮。
- 不能以编程方式更改用户在中`NSOpenSavePanelDelegate`所做的选择。

此外, 还就地进行了以下继承修改:

- **非沙盒** -  应用`NSOpenPanel``NSSavePanel``NSPanel``NSWindow``NSResponder``NSObject``NSOpenPanel``NSSavePanel``NSObject``NSOpenPanel``NSSavePanel`

### <a name="security-scoped-bookmarks-and-persistent-resource-access"></a>安全范围的书签和持久资源访问

如上所述, 沙盒 Xamarin 应用程序可以通过直接用户交互 (由 PowerBox 提供) 访问其容器外部的文件或资源。 但是, 对这些资源的访问不会在应用启动或系统重启期间自动保留。

通过使用_安全作用域的书签_, 沙盒 Xamarin 应用程序可以在应用重新启动后保留用户意向并维护对给定资源的访问权限。

#### <a name="security-scoped-bookmark-types"></a>安全作用域的书签类型

使用安全作用域的书签和持久资源访问时, 有两个 sistine 用例:

- **应用范围书签提供对用户指定的文件或文件夹的持久访问。** 

    例如, 如果沙盒 Xamarin Mac 应用程序允许使用打开外部文档进行编辑 (使用`NSOpenPanel`), 则应用程序可以创建应用范围的书签, 以便以后可以再次访问同一文件。
- **文档范围书签提供特定文档对子文件的永久访问权限。** 

例如, 一个视频编辑应用程序, 该应用程序创建一个项目文件, 该项目文件可以访问单独的图像、视频剪辑和以后将合并为单个影片的声音文件。

当用户将资源文件导入项目 (通过`NSOpenPanel`) 时, 应用程序将为存储在项目中的项创建文档范围的书签, 以便应用程序始终可以访问该文件。

可以打开书签数据和文档本身的任何应用程序都可以解析文档范围的书签。 这支持可移植性, 允许用户将项目文件发送给另一个用户, 并使所有书签都适用于这些用户。

> [!IMPORTANT]
> 文档范围的书签_只能_指向单个文件而不是文件夹, 并且该文件不能位于系统所使用的位置 (如`/private`或`/Library`)。

#### <a name="using-security-scoped-bookmarks"></a>使用安全作用域的书签

使用任何一种类型的安全范围书签, 都需要执行以下步骤:

1. **在需要使用安全范围内书签的 Xamarin 应用程序中设置合适的权利**-对于应用范围的书签, 请将`com.apple.security.files.bookmarks.app-scope`权利密钥设置为。 `true` 对于文档范围的书签, 请将`com.apple.security.files.bookmarks.document-scope`权利密钥设置`true`为。
2. **创建安全作用域的书签**-对于用户已提供访问权限的任何文件或文件夹 (例如, 通过`NSOpenPanel` ) 执行此操作, 应用将需要永久访问。 使用类`NSUrl`的`public virtual NSData CreateBookmarkData (NSUrlBookmarkCreationOptions options, string[] resourceValues, NSUrl relativeUrl, out NSError error)`方法创建书签。
3. **解析安全范围的书签**-当应用需要再次访问资源时 (例如, 重新启动后), 需要将书签解析为安全范围的 URL。 使用类`NSUrl`的`public static NSUrl FromBookmarkData (NSData data, NSUrlBookmarkResolutionOptions options, NSUrl relativeToUrl, out bool isStale, out NSError error)`方法解析书签。
4. **显式通知系统你要从安全作用域的 url 访问文件**-此步骤需要在获取上面的安全范围 url 之后立即执行, 或者在你稍后需要重新获取对资源的访问权限释放你对它的访问权限。 调用类`NSUrl`的方法,开始访问安全`StartAccessingSecurityScopedResource ()`范围的 URL。
5. **显式通知系统你已完成从安全范围 URL 访问文件的操作**-尽快, 你应在应用程序不再需要访问该文件 (例如, 如果用户将其关闭) 时通知系统。 调用类`NSUrl`的方法,停止访问安全`StopAccessingSecurityScopedResource ()`范围的 URL。

放弃对资源的访问权限后, 需要再次返回到步骤 4, 以便重新建立访问权限。 如果重新启动 Xamarin 应用, 则必须返回到步骤 3, 并重新解析书签。

> [!IMPORTANT]
> 如果无法释放对安全范围的 URL 资源的访问权限, 则会导致 Xamarin 应用程序泄漏内核资源。 因此, 在重新启动应用程序之前, 应用程序将无法再将文件系统位置添加到其容器。

### <a name="the-app-sandbox-and-code-signing"></a>应用沙盒和代码签名

启用应用沙盒并为 Xamarin 应用启用特定要求后 (通过权利), 必须对项目进行代码签名, 才能使沙盒中的沙箱生效。 必须执行代码签名, 因为应用沙盒处理所需的权利已链接到应用的签名。 

macOS 强制实施应用容器和其代码签名之间的链接, 在这种情况下, 其他任何应用程序都不能访问该容器, 即使它是哄骗应用捆绑 ID。 此机制的工作方式如下:

1. 当系统创建应用的容器时, 它将在该容器上设置_访问控制列表_(ACL)。 列表中的初始访问控制项包含应用的_指定需求_(DR), 其中描述了应用的未来版本的识别方式 (升级后)。
2. 每次启动具有相同捆绑 ID 的应用时, 系统都会检查应用的代码签名是否与容器 ACL 中某个条目中指定的指定要求相匹配。 如果系统找不到匹配项, 则会阻止应用程序启动。

代码签名的工作方式如下:

1. 创建 Xamarin Mac 项目之前, 请从 Apple 开发人员门户获取开发证书、分发证书和开发人员 ID 证书。
2. 当 Mac 应用商店分发 Xamarin 应用程序时, 它将使用 Apple 代码签名进行签名。

测试和调试时, 将使用已签名的 Xamarin 应用程序版本 (将用于创建应用容器)。 稍后, 如果你想要从 Apple 应用商店测试或安装版本, 它将使用 Apple 签名进行签名, 并将无法启动 (因为它与原始应用容器的代码签名不相同)。 在这种情况下, 你将收到类似于以下内容的故障报告:

```csharp
Exception Type:  EXC_BAD_INSTRUCTION (SIGILL)
```

若要解决此问题, 你需要调整 ACL 项以指向应用的 Apple 签名版本。

有关创建和下载沙盒所需的预配配置文件的详细信息, 请参阅上面的[签名和预配应用程序](#Signing_and_Provisioning_the_App)部分。

#### <a name="adjusting-the-acl-entry"></a>调整 ACL 条目

若要允许运行 Xamarin 应用的 Apple 签名版本, 请执行以下操作:

1. 打开终端应用 (在中`/Applications/Utilities`为)。
2. 向 Xamarin 应用程序的 Apple 签名版本打开查找器窗口。
3. 在`asctl container acl add -file`终端窗口中键入。
4. 将 Xamarin 应用程序的图标从 Finder 窗口拖放到终端窗口中。
5. 文件的完整路径将添加到终端中的命令。
6. 按**enter**执行该命令。

容器的 ACL 现在包含对这两个版本的 Xamarin 应用程序指定的代码要求, macOS 现在将允许运行任一版本。

#### <a name="display-a-list-of-acl-code-requirements"></a>显示 ACL 代码要求的列表

可以通过执行以下操作, 查看容器的 ACL 中的代码要求列表:

1. 打开终端应用 (在中`/Applications/Utilities`为)。
2. 键入 `asctl container acl list -bundle <container-name>`。
3. 按**enter**执行该命令。

通常`<container-name>`为 Xamarin 应用程序的捆绑标识符。

## <a name="designing-a-xamarinmac-app-for-the-app-sandbox"></a>设计应用程序沙箱的 Xamarin 应用程序

在设计应用程序沙箱的 Xamarin 应用程序时, 应该遵循常见的工作流。 这就是, 在应用程序中实现沙盒的具体信息在给定应用程序的功能中是唯一的。

### <a name="six-steps-for-adopting-the-app-sandbox"></a>采用应用沙盒的六个步骤

设计应用程序沙箱的 Xamarin 应用程序通常包括以下步骤:

1. 确定应用是否适用于沙盒。
2. 设计开发和分发策略。
3. 解决任何 API 不兼容问题。
4. 向 Xamarin 项目应用所需的应用沙盒。
5. 使用 XPC 添加特权分离。
6. 实现迁移策略。

> [!IMPORTANT]
> 你不能仅对应用捆绑包中的主要可执行文件进行沙盒处理, 还必须包含该捆绑包中的每个包含的帮助应用或工具。 这对于从 Mac 应用商店分发的任何应用都是必需的, 并且在可能的情况下, 应为任何其他形式的应用分发执行此操作。

若要获取 Xamarin 应用捆绑包中所有可执行二进制文件的列表, 请在终端中键入以下命令:

```bash
find -H [Your-App-Bundle].app -print0 | xargs -0 file | grep "Mach-O .*executable"
```

其中`[Your-App-Bundle]`是应用程序捆绑包的名称和路径。

### <a name="determine-whether-a-xamarinmac-app-is-suitable-for-sandboxing"></a>确定 Xamarin 应用是否适用于沙箱处理

大多数 Xamarin 应用程序都与应用程序沙箱完全兼容, 因此适用于沙箱处理。 如果应用程序需要应用沙盒不允许的行为, 则应考虑使用替代方法。

如果你的应用程序需要以下行为之一, 则它与应用沙盒不兼容:

- **授权服务**-使用应用沙盒时, 不能使用 "[授权服务 C 参考](https://developer.apple.com/library/prerelease/mac/documentation/Security/Reference/authorization_ref/index.html#//apple_ref/doc/uid/TP30000826)" 中所述的函数。
- **辅助功能 api** -无法对辅助应用 (如屏幕阅读器或控制其他应用程序的应用) 进行沙箱处理。
- **将 Apple 事件发送到任意应用**-如果应用需要向未知、任意应用发送 apple 事件, 则无法对其进行沙盒处理。 对于被调用应用程序的已知列表, 应用仍可进行沙盒处理, 且权利将需要包含被调用应用列表。
- **将用户信息字典发送到其他任务**-通过应用沙盒, 在发布到`userInfo` `NSDistributedNotificationCenter`对象以便发送消息其他任务时, 不能包括字典。
- **加载内核扩展**-应用沙盒禁止加载内核扩展。
- **在 "打开" 和 "保存" 对话框中模拟用户输入**-以编程方式操作 "打开" 或 "保存" 对话框, 以模拟或更改用户输入。
- **在其他应用上访问或设置首选项**-应用沙盒禁止操作其他应用的设置。
- **配置网络设置**-应用沙盒禁止操作网络设置。
- **终止其他应用**-应用沙盒禁止使用`NSRunningApplication`终止其他应用。

### <a name="resolving-api-incompatibilities"></a>解决 API 不兼容性

设计适用于应用沙盒的 Xamarin 应用程序时, 可能会遇到一些 macOS Api 使用不兼容的情况。

下面是一些常见问题和解决方法, 你可以解决这些问题:

- **打开、保存和跟踪文档**-如果你使用以外的任何技术`NSDocument`来管理文档, 则应切换到它, 因为内置了对应用沙盒的支持。 `NSDocument`自动使用 PowerBox, 如果用户在查找器中移动文档, 则支持在其中保留文档。
- **保留对文件系统资源的访问权限**-如果 Xamarin 应用程序依赖于对其容器之外的资源的持久访问, 请使用安全作用域的书签来维护访问权限。
- **为应用创建登录项**-使用应用沙盒时, 不能使用`LSSharedFileList`创建登录项, 也不能使用`LSRegisterURL`操作启动服务的状态。 使用通过`SMLoginItemSetEnabled`服务管理框架文档中的 "[添加登录项](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-SW1)" 一文中所述的函数。
- **访问用户数据**-如果使用 POSIX 函数 ( `getpwuid`如) 从目录服务获取用户的主目录, 请考虑使用 Cocoa 或 Core `NSHomeDirectory`Foundation 符号 (如)。
- **访问其他应用的首选项**-由于应用沙箱将路径查找 api 定向到应用的容器, 因此修改首选项会在该容器内发生, 并在不允许访问其他应用首选项。 
- **使用 Web 视图中的 HTML5 嵌入视频**-如果 Xamarin 应用使用 WebKit 播放嵌入式 HTML5 视频, 还必须将应用链接到 AV 基础框架。 否则, 应用沙盒会阻止 CoreMedia 播放这些视频。

### <a name="applying-required-app-sandbox-entitlements"></a>应用所需的应用沙盒权利

需要为想要在应用沙盒中运行的任何 Xamarin 应用程序编辑权利, 并选中 "**启用应用沙盒**" 复选框。

根据应用程序的功能, 你可能需要启用其他权利来访问操作系统功能或资源。 如果最大限度地减少了运行应用程序所需的最小权利, 最大限度地减少了你请求的权利, 只需随机启用权利即可。

若要确定 Xamarin 应用程序所需的权利, 请执行以下操作:

1. 启用应用沙盒并运行 Xamarin。
2. 运行应用程序的功能。
3. 打开控制台应用 (可在中`/Applications/Utilities`找到), 并`sandboxd`在 "**所有消息**" 日志中查找冲突。
4. 对于每`sandboxd`个冲突, 请使用应用容器而不是其他文件系统位置来解决此问题, 或应用应用沙盒权限以启用对受限制的操作系统功能的访问。
5. 再次重新运行并测试所有 Xamarin 应用程序功能。
6. 重复此步骤`sandboxd` , 直到所有冲突均已解决。

### <a name="add-privilege-separation-using-xpc"></a>使用 XPC 添加特权分离

开发适用于应用程序沙箱的 Xamarin 应用程序时, 查看应用程序在特权和访问方面的行为, 然后考虑将高风险操作划分为他们自己的 XPC 服务。

有关详细信息, 请参阅 Apple[创建 XPC 服务](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html#//apple_ref/doc/uid/10000172i-SW6)和[守护程序和服务编程指南](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i)。

### <a name="implement-a-migration-strategy"></a>实现迁移策略

如果要发布的是以前未进行沙盒处理的 Xamarin Mac 应用程序的新的沙盒版本, 需要确保当前用户具有平滑的升级路径。 

 有关如何实现容器迁移清单的详细信息, 请阅读 Apple 将[应用迁移到沙盒](https://developer.apple.com/library/prerelease/mac/documentation/Security/Conceptual/AppSandboxDesignGuide/MigratingALegacyApp/MigratingAnAppToASandbox.html#//apple_ref/doc/uid/TP40011183-CH6-SW1)文档。

## <a name="summary"></a>总结

本文详细介绍了如何对 Xamarin 应用程序进行沙箱处理。 首先, 我们创建了一个简单的 Xamarin 应用程序, 用于显示应用沙盒的基本知识。 接下来, 我们演示了如何解决沙盒冲突。 接下来, 我们深入探讨了应用程序沙箱, 最后介绍了如何设计应用程序沙箱的 Xamarin 应用程序。



## <a name="related-links"></a>相关链接

- [发布到 App Store](~/mac/deploy-test/publishing-to-the-app-store/index.md)
- [关于应用沙盒](https://developer.apple.com/library/content/documentation/Security/Conceptual/AppSandboxDesignGuide/AboutAppSandbox/AboutAppSandbox.html)
- [常见的应用沙盒处理问题](https://developer.apple.com/library/content/qa/qa1773/_index.html)
