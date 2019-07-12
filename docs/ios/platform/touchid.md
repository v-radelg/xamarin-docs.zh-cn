---
title: 在 Xamarin.iOS touch ID
description: 本文档介绍如何在 Xamarin.iOS 应用程序中使用 Touch ID，Apple 的指纹生物识别身份验证技术。
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 25ace6d7febe495164378b3633f06371806e2f82
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832309"
---
# <a name="touch-id-in-xamarinios"></a>在 Xamarin.iOS touch ID

作为一种方式进行身份验证的用户-类似于密码的 iOS 7 中引入了触摸屏输入 ID。 但是，它被限制为解锁设备、 应用商店、 使用 iTunes 和 iCloud 密钥链仅进行身份验证。

现在有两种方法使用 Touch ID 作为使用本地身份验证 API 的 iOS 8 应用程序的身份验证机制。 目前不能使用本地身份验证进行远程身份验证。

若要完全了解 Touch ID 和其价值，我们应了解密钥链服务以及这些新的更改的用户的数据的含义。 Keychain Access 中也已后在 iOS 8 中通过新的访问控制列表 (Acl) 功能使用扩展。

## <a name="keychain--secure-enclave"></a>密钥链和安全 Enclave

密钥链是大型数据库提供安全存储密码、 密钥、 证书和说明的一个单独的 Apple id。 在 iOS 8 中的应用始终有权访问自己唯一的密钥链的项，并且不能访问其他应用程序的任何密钥链的项。 这不同于 OS X 的密钥链被解除锁定都使用一个密码，让任何密钥链服务感知应用程序使用密钥链。 在本文中我们将重点介绍 iOS 8 中的密钥链工作原理。

密钥链是一个专用的数据库，每个行被称为_密钥链项_。 每个项由密钥链属性描述和包含的加密值。 若要允许高效使用密钥链，它非常适合较小的项目，或_机密_。
密钥链中的每个项受用户密码和唯一设备机密。 密钥链项应受到保护，即使用户不使用其设备。 这实现在 iOS 中通过仅允许在设备处于解锁状态时可用的项-锁定设备时变得不可用。 它们还可以存储在加密的备份。 密钥链的主要功能之一是强制实施访问控制;应用程序有权访问其部分密钥链，并已将阻止所有其他应用程序。 下图显示了应用程序与密钥链交互的方式：

[![](touchid-images/image1.png "此图描述了应用程序与密钥链交互的方式")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>安全 Enclave

密钥链不能解密的密钥链项重试。而是在中完成*安全 Enclave*。 安全 enclave 是负责确定成功匹配的指纹对已注册的打印 Touch ID 传感器数据的 A7 芯片中协处理器。 它然后将解密密钥链项，并返回到密钥链的解密的密钥。

### <a name="working-with-keychain"></a>使用密钥链

第一次你的应用程序应查询到密钥链，以查看是否存在密码。 如果不存在，可能需要提示输入密码，因此用户不一直在要求。 如果需要更新密码，提示用户输入新密码并将更新后的值传递到密钥链。

> [!NOTE]
> 使用机密从密钥链中检索后，应从内存中清除所有引用的数据。 永远不会将其分配给全局变量。

## <a name="keychain-acl-and-touch-id"></a>密钥链 ACL 和触摸 ID

访问控制列表是介绍有关必须发生什么情况允许发生的特定操作的信息的 iOS 8 中新的密钥链项属性。 这可能是在显示警报对话框或请求密码的形式。 ACL 允许你设置的可访问性和密钥链项的身份验证。 下图显示了这一新属性如何使用密钥链项的其余部分将联系：

[![](touchid-images/image2.png "下图显示了这一新属性如何归结剩余密钥链项目")](touchid-images/image2.png#lightbox)

截至 iOS 8，现在还有新的用户状态策略`SecAccessControl`，这强制实施安全 enclave 上 iPhone 5 及更高版本。 我们可以看到在下表中，只是如何设备配置可能会影响策略评估：

|设备配置|策略评估|备份机制|
|--- |--- |--- |
|而无需密码的设备|无访问权限|无|
|使用密码的设备|需要密码|无|
|使用 Touch ID 的设备|首选 Touch ID|允许密码|

安全 Enclave 内的所有操作可以相互都信任。 这意味着我们可以使用 Touch ID 身份验证结果授权密钥链项解密。 安全 Enclave 还会保留失败的 Touch ID 匹配，在其中种情况下用户将需要恢复为使用密码的计数器。
IOS 8 中，名为的新框架_本地身份验证_，支持此过程中设备的身份验证。 下一节中，我们将探讨这。

## <a name="local-authentication"></a>本地身份验证

随着我们建立在上一部分中，应用程序可以使用本地身份验证对用户进行身份验证中与已在设备设置安全策略的遵从性。

目前，该 API 提供了只有两个功能：首先，它有助于通过使用新密钥链访问控制列表 (Acl) 的现有密钥链服务。 密钥链数据可以是与成功的身份验证的用户指纹解锁。

其次，LocalAuthentication 提供两种方法进行身份验证本地应用程序。 开发人员应使用`CanEvaluatePolicy`来确定设备是否处于可接受 Touch ID，然后`EvaluatePolicy`启动身份验证操作。

虽然这两个功能提供本地身份验证，它们不提供用于应用程序或用户对远程服务器进行身份验证的机制。
本地身份验证进行身份验证提供的新标准用户界面。 对于 Touch ID，这是具有两个按钮，如下图所示的警报视图。 一个按钮来取消，而是要使用的身份验证 – 密码的回退方法。 此外，还有必须设置的自定义消息。 它是用于向用户为何 Touch ID 身份验证时需要说明的好办法。

[![](touchid-images/image12.png "Touch ID 身份验证警报")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>使用密钥链服务

我们早一些在密钥链项是如何被解密，使用安全 enclave 验证我们的密码。 在 iOS 8，我们可以使用本地身份验证请求与提供的回退机制或密码的实现上的访问控制列表功能结合使用 Touch ID 验证。
若要使用我们应使用的 ACL`SecAccessControl`策略，然后选中设备使用的状态`SecAccessible.WhenPasscodeSetThisDeviceOnly`或`SecAccessible.WhenUnlocked`。

#### <a name="considerations-with-acl"></a>使用 ACL 的注意事项

有许多事情我们应使用密钥链的 ACL 时请记住，下面列出了其中一些：

- 只能使用前台应用程序 – 如果您调用任何密钥链操作在后台线程的调用将失败。
- 添加和更新密钥链项可能需要身份验证。
- 如果请求在 keychain 中返回多个匹配项，可能需要身份验证。
- ACL 受保护的项仅设备，并因此不同步或备份。

### <a name="using-local-authentication-without-keychain-services"></a>使用本地身份验证不使用密钥链服务

作为一种方法来收集凭据，如密码或 Touch ID，并使用安全 Enclave 完成对用户进行身份验证创建本地身份验证。 可以将其视为您的应用程序和可以永远不会直接相互通信安全 Enclave 之间的桥梁。 它还可用于你的应用程序的策略评估。

若要执行此操作应用程序调用本地身份验证，这将启动内部安全 Enclave 的操作内的策略评估。 您可以利用此身份验证提供给您的应用程序，而无需直接查询/访问安全 Enclave。

[![](touchid-images/image13a.png "使用本地身份验证不使用密钥链服务")](touchid-images/image13a.png#lightbox)

应用程序中使用本地身份验证提供简单的方法来实现用户验证，例如若要为各个解锁功能仅为设备所有者，例如银行应用程序，或助手家长控制的眼睛应用程序。 您还可以使用它作为一种方法来扩展已存在的身份验证 – 用户喜欢其信息安全，但它们还希望能够进行选择。

本地身份验证的安全性有何不同的密钥链。 例如，使用密钥链，信任关系时，操作系统和安全 Enclave 之间。 使用本地身份验证，它是应用程序和操作系统，这意味着只有权访问的安全 Enclave，不安全 Enclave 本身结果之间。

有关安全的主题，它还是非常重要，了解是否有**不能访问**到已注册的手指或指纹映像。 安全 Enclave 是此信息的所有者，因此没有其他系统组件有权访问它。

若要通过使用本地身份验证 API 没有密钥链的情况下使用 Touch ID，有几个函数，我们可以使用。 下面详细介绍这些：

*   `CanEvaluatePolicy` -这会直接检查设备能够接受 Touch id。
*   `EvaluatePolicy` – 这将启动身份验证操作和显示的 UI，并返回`true`或`false`答案。
*   `DeviceOwnerAuthenticationWithBiometrics` – 这是可用于显示 Touch ID 屏幕的策略。 值得注意是，没有密码的此处回退机制，而是应允许用户跳过 Touch ID 身份验证应用程序中实现以上后备机制。

有几条注意事项与使用本地身份验证，如下所示：

*   使用密钥链，它只能运行在前景中。 在后台线程上调用它将导致失败。
*   请记住，策略评估可能会失败。 密码按钮将需要作为回退实现。
*   必须提供`localizedReason`来解释为什么需要身份验证。 这有助于与用户建立信任。

接下来，在下面的部分中，我们将探讨如何实现 API 考虑这些注意事项。

## <a name="adding-touch-id-to-your-application"></a>将 Touch ID 添加到你的应用程序

在前面部分中，我们介绍了其理论依据访问和使用密钥链和本地身份验证的身份验证。 现在，我们将介绍如何将在 Touch ID 集成到你的应用程序。

### <a name="walkthrough"></a>演练

让我们看一下将某些 Touch ID 身份验证添加到我们的应用程序。 在本演练中我们将更新[情节提要表](https://developer.xamarin.com/samples/StoryboardTable/)示例中，添加本地身份验证，以便其工作原理类似[情节提要表 – 本地身份验证](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/)示例，只允许身份验证的用户将工作添加到列表。

1. 下载示例，然后在运行 Visual Studio for mac。
2. 双击`MainStoryboard.Storyboard`iOS 设计器中打开示例。 此示例中，我们想要将一个新屏幕添加到我们的应用程序，它将控制身份验证。 这会在当前之前`MasterViewController`。
3. 将一个新**视图控制器**从**工具箱**到**设计图面**。 设置为**根视图控制器**通过**按 Ctrl 并拖动**从**导航控制器**:

    [![](touchid-images/image4.png "将根视图控制器")](touchid-images/image4.png#lightbox)
4.  命名新视图控制器`AuthenticationViewController`。
5. 接下来，将一个按钮，并将其置于`AuthenticationViewController`。 我们称之为`AuthenticateButton`，并为其提供文本`Add a Chore`。
6. 在创建事件`AuthenticateButton`调用`AuthenticateMe`。
7. 创建手动从 segue`AuthenticationViewController`通过单击底部的黑色栏并**按 Ctrl 并拖动**从到栏`MasterViewController`，然后选择**推送**(或**显示**如果使用大小类）：

    [![](touchid-images/image5.png "将其从栏拖到 MasterViewController 和选择推送或显示")](touchid-images/image6.png#lightbox)
8. 单击新创建的 segue 并为其提供标识符`AuthenticationSegue`，如下所示：

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

这是您需要实现 Touch ID 身份验证使用本地身份验证的所有代码。 下图中突出显示的行显示了如何使用本地身份验证：

[![](touchid-images/image8.png "突出显示的行显示了如何使用本地身份验证")](touchid-images/image8.png#lightbox)

首先，我们需要建立设备是否处于可接受输入，通过使用 Touch ID`CanEvaluatePolicy`并在策略中传递`DeviceOwnerAuthenticationWithBiometrics`。 如果这为 true，则我们可以通过使用显示 Touch ID UI `EvaluatePolicy`。 有三个部分的信息，我们将传递到`EvaluatePolicy`– 策略本身、 解释了为何有必要，身份验证的字符串和回复处理程序。 回复处理程序告诉应用程序，它应在成功，或不成功，身份验证的情况下执行的操作。 让我们看更详细地介绍答复处理程序：

[![](touchid-images/image9.png "回复处理程序")](touchid-images/image9.png#lightbox)

类型的指定答复的处理程序`LAContextReplyHandler`，后者采用参数成功 –`bool`值，和一个`NSError`调用`error`。 如果成功，这是我们将实际执行位置无论是我们想要进行身份验证 – 在这种情况下显示屏幕可让我们将添加新的任务。 请记住本地身份验证的注意事项之一是，它必须是在前台运行，因此请务必使用`InvokeOnMainThread`:

[![](touchid-images/image10.png "InvokeOnMainThread 用于本地身份验证")](touchid-images/image10.png#lightbox)

最后，在身份验证成功后，我们想要转换到`MasterViewController`。 `PerformSegue`方法可用于执行此操作：

[![](touchid-images/image11.png "调用转换到 MasterViewController PerformSegue 方法")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>总结
在本指南还介绍了密钥链以及这在 iOS 中的工作方式。 我们还探讨了密钥链 ACL，并在 iOS 中的对此更改。 接下来，我们了解了一下本地身份验证框架，这是 iOS 8 中的新增功能，然后介绍了在我们的应用程序中实现 Touch ID 身份验证。

## <a name="related-links"></a>相关链接

- [情节提要表 – 本地身份验证](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) 
- [密钥链 WWDC 示例](https://developer.xamarin.com/samples/KeychainTouchID/)
- [密钥链 （示例）](https://developer.xamarin.com/samples/Keychain/)
