---
title: Xamarin 中的 Touch ID
description: 本文档介绍如何在 Xamarin iOS 应用程序中使用 Touch ID、Apple 的生物指纹身份验证技术。
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: cb71fc75b01621ed381f42b41df68fc80309f22c
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121303"
---
# <a name="touch-id-in-xamarinios"></a>Xamarin 中的 Touch ID

在 iOS 7 中引入了 Touch ID 作为对用户进行身份验证的方法-类似于密码。 但是, 仅限使用应用商店解锁设备, 使用 iTunes 并仅对 iCloud 密钥链进行身份验证。

现在有两种方法可以使用触控 ID 作为 iOS 8 应用程序中使用本地身份验证 API 的身份验证机制。 目前不能使用本地身份验证来进行远程身份验证。

为了充分了解 Touch ID 及其价值, 我们应该探索密钥链服务以及这些新更改对用户数据的意义。 通过使用新的访问控制列表 (Acl) 功能, 还在 iOS 8 中扩展了密钥链访问。

## <a name="keychain--secure-enclave"></a>密钥链 & Secure Enclave

密钥链是一个大型数据库, 为单个 Apple ID 的密码、密钥、证书和说明提供安全存储。 在 iOS 8 中, 应用程序始终可以访问自己的唯一密钥链项, 并且不能访问其他应用程序的任何密钥链项。 这不同于 OS X, 其中使用单个密码解锁密钥链, 使任何密钥链服务感知应用程序都可使用密钥链。 在本文中, 我们将重点介绍密钥链在 iOS 8 中的工作方式。

密钥链是专用数据库, 其中每行称为_密钥链项_。 每一项由密钥链属性描述, 并由加密值组成。 为了有效地使用密钥链, 它针对小项目或_机密_进行了优化。
每个密钥链项目都受用户密码和唯一设备机密的保护。 即使用户不使用其设备, 也应保护密钥链项。 这是通过以下方式实现的: 仅允许在设备解锁时提供的项可用-设备锁定后, 它们将不可用。 它们还可以存储在加密的备份中。 密钥链的主要功能之一是强制实施访问控制;应用程序有权访问它的密钥链部分, 将阻止所有其他应用程序。 下图说明了应用程序如何与密钥链交互:

[![](touchid-images/image1.png "此图说明了应用程序如何与密钥链交互")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>保护 Enclave

密钥链无法自行解密密钥链项;而是在*Secure Enclave*中完成。 安全 enclave 是 A7 芯片中的一个共同处理器, 负责从 Touch ID 传感器确定指纹数据是否与已注册的打印匹配成功。 然后, 它将对密钥链项进行解密, 并将解密后的机密返回给密钥链。

### <a name="working-with-keychain"></a>使用密钥链

首先, 应用程序应查询到密钥链以查看是否存在密码。 如果它不存在, 你可能需要提示输入密码, 以使用户不会不断被询问。 如果需要更新密码, 则提示用户输入新密码, 并将更新后的值传递到密钥链。

> [!NOTE]
> 使用从密钥链检索到的机密后, 应从内存中清除对数据的所有引用。 不要将其分配给全局变量。

## <a name="keychain-acl-and-touch-id"></a>密钥链 ACL 和 Touch ID

"访问控制列表" 是 iOS 8 中的新密钥链项属性, 描述了允许特定操作发生的情况的相关信息。 这可能是显示警报对话框或请求密码的形式。 ACL 允许你为密钥链项设置辅助功能和身份验证。 下图显示了此新属性如何与密钥链项的其余部分结合在一起:

[![](touchid-images/image2.png "此图显示了此新属性如何与密钥链项的其余部分结合在一起")](touchid-images/image2.png#lightbox)

从 iOS 8 开始, 现在有一个新的用户状态策略`SecAccessControl`, 它由 iPhone 5s 及更高版本上的 secure enclave 强制执行。 在下表中, 可以看到设备配置如何影响策略评估:

|设备配置|策略评估|备份机制|
|--- |--- |--- |
|不带密码的设备|无访问权限|无|
|带密码的设备|需要密码|无|
|具有 Touch ID 的设备|首选 Touch ID|允许密码|

安全 Enclave 内的所有操作都可以相互信任。 这意味着我们可以使用 Touch ID 身份验证结果来授权密钥链项解密。 Secure Enclave 还会保留失败的触摸 ID 匹配的计数器, 在这种情况下, 用户必须使用密码还原。
IOS 8 中称为_本地身份验证_的新框架在设备中支持此身份验证过程。 下一节将对此进行探讨。

## <a name="local-authentication"></a>本地身份验证

如前一部分中所述, 应用程序可以使用本地身份验证对用户进行身份验证, 使其遵守已在设备上设置的安全策略。

目前, API 仅提供两个功能:首先, 它通过使用新的密钥链访问控制列表 (Acl) 来帮助现有的密钥链服务。 用户指纹的身份验证成功后, 可以使用密钥链数据解除锁定。

其次, Localauthentication.framework 提供了两种方法来对应用程序进行本地身份验证。 开发人员应`CanEvaluatePolicy`使用来确定设备是否能够接受 Touch ID, 然后再`EvaluatePolicy`开始身份验证操作。

尽管这两种功能都提供了本地身份验证, 但它们并不提供应用程序或用户对远程服务器进行身份验证的机制。
本地身份验证提供了一个用于身份验证的新的标准用户界面。 对于 Touch ID, 这是一个包含两个按钮的警报视图, 如下所示。 一个按钮用于取消, 另一个用于使用身份验证的回退方式–密码。 还必须设置自定义消息。 最好使用此方法向用户说明需要使用触控 ID 身份验证的原因。

[![](touchid-images/image12.png "Touch ID 身份验证警报")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>With 密钥链 Services

我们前面已经探讨了如何使用安全 enclave 验证密码来解密密钥链项目。 在 iOS 8 中, 可以使用本地身份验证, 通过访问控制列表功能 (提供回退机制的实现或密码) 来请求触控 ID 验证。
若要使用 ACL, 应使用`SecAccessControl`策略, 然后使用`SecAccessible.WhenPasscodeSetThisDeviceOnly`或`SecAccessible.WhenUnlocked`检查设备的状态。

#### <a name="considerations-with-acl"></a>ACL 注意事项

将 ACL 与密钥链结合使用时, 我们应注意以下事项:

- 仅适用于前台应用程序–如果在后台线程上调用任何密钥链操作, 则调用将失败。
- 添加和更新密钥链项可能需要进行身份验证。
- 如果请求在密钥链中返回多个匹配项, 则可能需要进行身份验证。
- 受 ACL 保护的项仅限设备, 因此不会同步或备份。

### <a name="using-local-authentication-without-keychain-services"></a>在没有密钥链服务的情况下使用本地身份验证

本地身份验证是作为一种收集凭据 (例如密码或触控 ID) 的方法创建的, 并使用安全 Enclave 来完成对用户的身份验证。 将其视为应用程序和安全 Enclave 之间的桥梁, 这永远不会直接相互通信。 它还可用于对应用程序进行策略评估。

若要执行此操作, 应用程序需要在本地身份验证中调用策略评估, 这会在 Secure Enclave 中启动操作。 你可以利用它向应用程序提供身份验证, 而无需直接查询/访问安全 Enclave。

[![](touchid-images/image13a.png "在没有密钥链服务的情况下使用本地身份验证")](touchid-images/image13a.png#lightbox)

在应用程序中使用本地身份验证提供了一种简单的方法来实现用户验证, 例如, 仅为设备所有者的眼睛 (如银行应用程序) 解除锁定功能, 或对个人的助手家长控制程序. 你还可以将其用作一种扩展已存在的身份验证的方式–用户喜欢其信息是安全的, 但他们也喜欢具有选项。

本地身份验证的安全性不同于密钥链的安全性。 例如, 使用密钥链时, 信任关系在操作系统和安全 Enclave 之间。 使用本地身份验证时, 它在应用程序和操作系统之间, 这意味着您只能访问安全 Enclave 的结果, 而不是安全 Enclave 本身。

在安全性方面, 知道**无权访问**已注册的手指或指纹映像也是非常重要的。 Secure Enclave 是此信息的所有者, 因此没有其他系统组件可以访问它。

若要通过利用本地身份验证 API 来使用 Touch ID 而不密钥链, 可以使用一些函数。 下面详细介绍了这些内容:

- `CanEvaluatePolicy`–只需检查设备是否能够接受 Touch ID。
- `EvaluatePolicy`–这将启动身份验证操作并显示 UI, 并返回`true`或`false`回答。
- `DeviceOwnerAuthenticationWithBiometrics`–这是可用于显示 "Touch ID" 屏幕的策略。 值得注意的是, 此处没有密码回退机制, 你应在应用程序中实现此回退, 以允许用户跳过 Touch ID 身份验证。

使用本地身份验证时, 请注意以下几点:

- 与密钥链一样, 它只能在前台运行。 在后台线程上调用该方法将导致其失败。
- 请记住, 策略评估可能会失败。 需要将密码按钮作为回退来实现。
- 您必须提供`localizedReason`以说明为何需要身份验证。 这有助于建立与用户的信任关系。

接下来, 在下面的部分中, 我们将介绍如何实现 API 来考虑这些注意事项。

## <a name="adding-touch-id-to-your-application"></a>向应用程序添加 Touch ID

在前面的部分中, 我们介绍了使用密钥链和本地身份验证进行访问和身份验证后的理论。 现在我们来看一下如何将 Touch ID 集成到你的应用程序中。

### <a name="walkthrough"></a>演练

我们来看看如何向应用程序添加一些 Touch ID 身份验证。 在本演练中, 我们将更新[情节提要表](https://docs.microsoft.com/samples/xamarin/ios-samples/data/storyboardtable/)示例, 添加本地身份验证, 以便其工作方式类似于[情节提要表–本地身份验证](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication)示例, 仅允许经过身份验证的用户向列表中添加工作。

1. 下载并在 Visual Studio for Mac 中运行该示例。
2. `MainStoryboard.Storyboard`双击以在 iOS 设计器中打开该示例。 在此示例中, 我们想要在应用程序中添加一个新屏幕来控制身份验证。 这将在当前`MasterViewController`之前。
3. 将新**视图控制器**从**工具箱**拖动到**Design Surface**。 **按住 Ctrl 并**从**导航控制器**拖动, 将此设置为**根视图控制器**:

    [![](touchid-images/image4.png "设置根视图控制器")](touchid-images/image4.png#lightbox)
4. 命名新的视图控制器`AuthenticationViewController`。
5. 接下来, 拖动一个按钮并将其放置`AuthenticationViewController`在上。 调用此`AuthenticateButton`方法, 并为其指定`Add a Chore`文本。
6. 在调用`AuthenticateButton` `AuthenticateMe`的上创建一个事件。
7. 单击底部的黑色条`AuthenticationViewController` , 并选择 "**推送**" (或 "使用大小类时`MasterViewController` **显示**") 从栏中创建手动 segue:

    [![](touchid-images/image5.png "从栏拖到 MasterViewController, 然后选择 \"推送\" 或 \"显示\"")](touchid-images/image6.png#lightbox)
8. 单击新创建的 segue 并为其指定标识符`AuthenticationSegue`, 如下所示:

    [![](touchid-images/image7.png "将 segue 标识符设置为 AuthenticationSegue")](touchid-images/image7.png#lightbox)
9. 将下列代码添加到 `AuthenticationViewController`：

    ```csharp
    partial void AuthenticateMe (UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var myReason = new NSString("To add a new chore");

        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
            var replyHandler = new LAContextReplyHandler((success, error) => {
                this.InvokeOnMainThread(()=> {
                    if(success)
                    {
                        Console.WriteLine("You logged in!");
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        // Show fallback mechanism here
                    }
                });
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
        };
    }
    ```

这是使用本地身份验证实现 Touch ID 身份验证所需的全部代码。 下图中突出显示的行显示了本地身份验证的使用:

[![](touchid-images/image8.png "突出显示的行显示了如何使用本地身份验证")](touchid-images/image8.png#lightbox)

首先, 我们需要确定设备是否能够接受 Touch ID 输入, 方法是使用`CanEvaluatePolicy`并传入策略。 `DeviceOwnerAuthenticationWithBiometrics` 如果为 true, 则可以使用`EvaluatePolicy`显示 Touch ID UI。 有三条信息需要传入`EvaluatePolicy` –策略本身, 一个字符串说明了需要身份验证的原因, 以及一个回复处理程序。 如果身份验证成功或失败, 则回复处理程序会告诉应用程序应执行的操作。 让我们更深入地了解回复处理程序:

[![](touchid-images/image9.png "回复处理程序")](touchid-images/image9.png#lightbox)

回复处理程序是指定类型的`LAContextReplyHandler`, 该类型采用参数 success `bool` –值和`NSError`调用`error`。 如果成功, 则我们会在其中实际执行要进行身份验证的任何内容-在这种情况下, 会显示屏幕, 让我们添加新的工作。 请记住, 本地身份验证的注意事项之一是必须在前台运行, 因此请务必使用`InvokeOnMainThread`:

[![](touchid-images/image10.png "使用 InvokeOnMainThread 进行本地身份验证")](touchid-images/image10.png#lightbox)

最后, 当身份验证成功时, 我们想要过渡到`MasterViewController`。 `PerformSegue`方法可用于执行此操作:

[![](touchid-images/image11.png "调用 PerformSegue 方法以过渡到 MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>总结

在本指南中, 我们介绍了密钥链以及如何在 iOS 中工作。 我们还探讨了密钥链 ACL, 并在 iOS 中对此进行了更改。 接下来, 我们看看本地身份验证框架, 这是 iOS 8 中的新框架, 并在应用程序中实现 Touch ID 身份验证。

## <a name="related-links"></a>相关链接

- [情节提要表–本地身份验证](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication)
- [密钥链 WWDC 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-keychaintouchid/)
- [密钥链 (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/keychain/)
