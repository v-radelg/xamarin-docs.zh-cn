---
title: 在 Xamarin.iOS 中实现 SiriKit
description: 本文档介绍在 xamarin ios 应用中实现 SiriKit 支持所需的步骤。 它讨论了 Intents 扩展和 Intents UI 扩展。
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: c8d5c1394bee2ca2419904b077522fac9d03d825
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669436"
---
# <a name="implementing-sirikit-in-xamarinios"></a>在 Xamarin.iOS 中实现 SiriKit

_本文介绍如何在 Xamarin.iOS 应用程序中实现 SiriKit 支持所需的步骤。_

新 ios 10、 通过 SiriKit，Xamarin.iOS 应用程序提供的 iOS 设备上使用 Siri 和 Maps 应用的用户可以访问的服务。 本文介绍如何通过添加所需的 Intents 扩展、 Intents UI 扩展和词汇 Xamarin.iOS 应用程序中实现 SiriKit 支持所需的步骤。

使用 Siri 适用于这一概念**域**，组知道相关的任务的操作。 该应用程序可以使用 Siri 与每个交互必须按如下所示划分为其已知的服务域之一：

- 音频或视频呼叫。
- 预订滑水感觉的时间。
- 管理锻炼。
- 消息传递。
- 正在搜索照片。
- 发送或接收付款。

当用户发出的 Siri 请求涉及应用扩展的服务之一时，SiriKit 发送该扩展**意向**对象，描述用户的请求和任何支持的数据。 然后，应用扩展生成的相应**响应**对象的给定**意向**，详细介绍如何扩展可以处理该请求。

本指南将介绍快速示例包括 SiriKit 支持现有应用程序。 对于此示例中，我们将使用假 MonkeyChat 应用：

[![](implementing-sirikit-images/monkeychat01.png "MonkeyChat 图标")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat 保持其自己书联系人的用户的好友名单，但每个屏幕名称 （如 Bobo 示例)，与相关联，并允许用户通过其屏幕名称发送到每个朋友的文本聊天。

## <a name="extending-the-app-with-sirikit"></a>扩展应用程序与 SiriKit

如中所示[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)指南中，扩展带有 SiriKit 的应用程序中包含有三个主要部分：

[![](implementing-sirikit-images/elements01.png "扩展应用程序与 SiriKit 关系图")](implementing-sirikit-images/elements01.png#lightbox)

这些问题包括：

1. **Intents 扩展**-验证用户响应，确认应用程序可以处理的请求和实际执行的任务以满足用户的请求。
2. **Intents UI 扩展** - *可选*、 提供响应 Siri 环境中的自定义用户界面并可以将应用 UI 和品牌到 Siri 来丰富用户体验。
3. **应用**-提供用户特定词汇，以便帮助 Siri 使用它的应用程序。 

将以下各节中详细介绍所有这些元素，并将其包含在应用中的步骤。


## <a name="preparing-the-app"></a>准备应用

SiriKit 构建扩展，但是，在将任何扩展添加到应用之前, 有开发人员需要执行的操作帮助 SiriKit 采用的一些事项。

### <a name="moving-common-shared-code"></a>移动公共共享的代码 

首先，开发人员可以将共享公共代码的一些之间移动应用和扩展到_共享项目_，_可移植类库 (Pcl)_ 或_本机库_。

扩展将需要能够执行所有的应用执行的操作。 中的示例 MonkeyChat 应用中，诸如查找联系人，添加新的联系人和条款将消息发送和检索消息历史记录。

通过将此公共代码移动到共享项目、 PCL 或本机库它轻松地维护该代码放在一个公用位置，并确保扩展与父应用程序提供统一的体验和功能的用户。

对于示例应用程序 MonkeyChat 数据模型和处理代码，例如网络和数据库访问将被移动到本机库。

请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 启动 Visual Studio for Mac 并打开 MonkeyChat 应用。
2. 右键单击解决方案名称在**Solution Pad** ，然后选择**添加** > **新建项目...**: 

    [![](implementing-sirikit-images/prep01.png "添加新项目")](implementing-sirikit-images/prep01.png#lightbox)
3. 选择**iOS** > **库** > **类库**然后单击**下一步**按钮： 

    [![](implementing-sirikit-images/prep02.png "选择类库")](implementing-sirikit-images/prep02.png#lightbox)
4. 输入`MonkeyChatCommon`有关**名称**然后单击**创建**按钮： 

    [![](implementing-sirikit-images/prep03.png "为名称输入 MonkeyChatCommon")](implementing-sirikit-images/prep03.png#lightbox)
5. 右键单击**引用**文件夹中的主应用程序**解决方案资源管理器**，然后选择**编辑引用...**.检查**MonkeyChatCommon**项目，然后单击**确定**按钮： 

    [![](implementing-sirikit-images/prep05.png "检查 MonkeyChatCommon 项目")](implementing-sirikit-images/prep05.png#lightbox)
6. 在中**解决方案资源管理器**，将主应用中的公共共享的代码拖动到本机库。
7. 对于 MonkeyChat，拖动**DataModels**并**处理器**主应用到本机库中的文件夹： 

    [![](implementing-sirikit-images/prep06.png "在解决方案资源管理器中的 DataModels 和处理器文件夹")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 启动 Visual Studio 并打开 MonkeyChat 应用。
2. 右键单击解决方案名称在**解决方案资源管理器**，然后选择**添加** > **新建项目...**.
3. 选择**可视化C#**   > **共享项目**然后单击**下一步**按钮： 

    [![](implementing-sirikit-images/prep02.w157-sml.png "选择类库")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. 输入`MonkeyChatCommon`有关**名称**然后单击**创建**按钮。
5. 右键单击**引用**文件夹中的主应用程序**解决方案资源管理器**，然后选择**编辑引用...**.检查**MonkeyChatCommon**项目，然后单击**确定**按钮： 

    [![](implementing-sirikit-images/prep05w.png "检查 MonkeyChatCommon 项目")](implementing-sirikit-images/prep05w.png#lightbox)
6. 在中**解决方案资源管理器**，将主应用中的公共共享的代码拖动到共享的项目。
7. 对于 MonkeyChat，拖动**DataModels**并**处理器**主应用到本机库中的文件夹。

-----

编辑的任何文件已移动到本机库，将命名空间以匹配的库。 例如，更改`MonkeyChat`到`MonkeyChatCommon`:

```csharp
using System;
namespace MonkeyChatCommon
{
    /// <summary>
    /// A message sent from one user to another within a conversation.
    /// </summary>
    public class MonkeyMessage
    {
        public MonkeyMessage ()
        {
        }
        ...
    }
}
```

接下来，请返回到主应用程序并添加`using`语句本机库的命名空间的任意位置应用程序使用已移动的类之一：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using MonkeyChatCommon;

namespace MonkeyChat
{
    public partial class MasterViewController : UITableViewController
    {
        public DetailViewController DetailViewController { get; set; }

        DataSource dataSource;
        ...
    }
}
```

### <a name="architecting-the-app-for-extensions"></a>扩展的构建应用程序

通常情况下，应用将注册多个意向和开发人员需要确保应用程序的体系结构的适当数量的意向扩展。

在其中应用需要多个目的的情况下，开发人员可以将所有其意向处理放入一个意向扩展或创建适用于每个意向单独的意向扩展的选项。

如果选择为每个意向创建单独的意向扩展时，开发人员可能最终会复制大量的样板代码中每个扩展，并创建大量的处理器和内存开销。

若要帮助的两个选项之间进行选择，请参阅是否任何意向自然同属于。 例如，进行音频和视频呼叫应用可能想要在单个的意向扩展中包含这两个这些意向，因为它们类似的任务的处理，并可以提供大多数代码重用。

对于任何目的或意图不适合到现有组的组，在应用程序的解决方案来包含它们创建一个新的意向扩展。


### <a name="setting-the-required-entitlements"></a>设置所需的权利

包括 SiriKit 集成任何 Xamarin.iOS 应用程序，必须具有正确的授权设置。 如果开发人员不会正确设置这些所需的权利，它们将不能安装或测试真正的 iOS 10 （或更高版本） 在硬件上，这也是由于 iOS 10 的要求应用程序模拟器不支持 SiriKit。

请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击`Entitlements.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 切换到**源**选项卡。
3. 添加`com.apple.developer.siri`**属性**，将**类型**到`Boolean`并**值**到`Yes`: 

    [![](implementing-sirikit-images/setup01.png "添加 com.apple.developer.siri 属性")](implementing-sirikit-images/setup01.png#lightbox)
4. 保存对文件所做的更改。
5. 双击**项目文件**中**解决方案资源管理器**打开进行编辑。
6. 选择**iOS 捆绑签名**并确保`Entitlements.plist`中选择文件**自定义权利**字段： 

    [![](implementing-sirikit-images/setup02.png "在自定义权利字段选择 Entitlements.plist 文件")](implementing-sirikit-images/setup02.png#lightbox)
7. 单击“确定”按钮保存更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击`Entitlements.plist`文件中**解决方案资源管理器**打开进行编辑。
3. 添加`com.apple.developer.siri`**属性**，将**类型**到`Boolean`并**值**到`Yes`: 

    [![](implementing-sirikit-images/setup01w.png "添加 com.apple.developer.siri 属性")](implementing-sirikit-images/setup01w.png#lightbox)
4. 保存对文件所做的更改。
5. 双击**项目文件**中**解决方案资源管理器**打开进行编辑。
6. 选择**iOS 捆绑签名**并确保`Entitlements.plist`中选择文件**自定义权利**字段。

-----

完成后，应用程序的`Entitlements.plist`文件应如下所示 （在编辑器中打开外部）：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.siri</key>
    <true/>
</dict>
</plist>
```

### <a name="correctly-provisioning-the-app"></a>正确预配应用程序

由于严格的安全，Apple 已周围将 SiriKit framework，任何 Xamarin.iOS 应用，用于实现 SiriKit_必须_具有正确的应用程序 ID 和权利 （请参阅上面部分），并且必须使用适当签名预配配置文件。

执行以下操作在 Mac 上：

1. 在 web 浏览器中，导航到[ https://developer.apple.com ](https://developer.apple.com)和登录到你的帐户。
2. 单击**证书**，**标识符**并**配置文件**。
3. 选择**预配配置文件**，然后选择**应用 Id**，然后单击**+** 按钮。
4. 输入**名称**新配置文件。
5. 输入**捆绑 ID**按照 Apple 的命名建议。
6. 向下滚动到**应用服务**部分中，选择**SiriKit**然后单击**继续**按钮： 

    [![](implementing-sirikit-images/setup03.png "选择 SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. 验证所有设置，然后**提交**应用 id。
8. 选择**预配配置文件** > **开发**，单击**+** 按钮，选择**Apple ID**，然后单击**继续**。
9. 单击选择**所有**，然后单击**继续**。
10. 单击**全**，然后单击**继续**。
11. 输入**配置文件名称**使用 Apple 的命名建议，然后单击**继续**。
12. 启动 Xcode。
13. 从 Xcode 菜单上选择**首选项...**
14. 选择**帐户**，然后单击**查看详细信息...** 按钮： 

    [![](implementing-sirikit-images/setup04.png "选择帐户")](implementing-sirikit-images/setup04.png#lightbox)
15. 单击**下载所有配置文件**中较低的左下角的按钮： 

    [![](implementing-sirikit-images/setup05.png "下载所有配置文件")](implementing-sirikit-images/setup05.png#lightbox)
16. 絋粄**预配配置文件**创建更高版本中安装了 Xcode。
17. 打开该项目添加到 SiriKit 支持在 Visual Studio for mac。
18. 双击`Info.plist`文件中**解决方案资源管理器**。
18. 絋粄**捆绑包标识符**匹配更高版本的 Apple 的开发人员门户中创建的一个： 

    [![](implementing-sirikit-images/setup06.png "捆绑包标识符")](implementing-sirikit-images/setup06.png#lightbox)
18. 在中**解决方案资源管理器**，选择**项目**。
19. 右键单击项目并选择**选项**。
21. 选择**iOS 捆绑签名**，选择**签名标识**并**预配配置文件**上面创建： 

    [![](implementing-sirikit-images/setup07.png "选择签名标识和预配配置文件")](implementing-sirikit-images/setup07.png#lightbox)
22. 单击“确定”按钮保存更改。

> [!IMPORTANT]
> 测试 SiriKit 仅适用于真实 iOS 10 个硬件设备而不是在 iOS 10 模拟器。 如果遇到问题安装 SiriKit 启用 Xamarin.iOS 应用程序的真实硬件上，确保所需的权利、 应用程序 ID、 签名标识符和预配配置文件具有已正确配置，在 Apple 的开发人员门户和 Visual Studio for mac。

### <a name="requesting-siri-authorization"></a>请求 Siri 授权

该应用将添加任何用户特定词汇或 Intents 扩展连接到使用 Siri 之前，必须从用户访问使用 Siri 请求授权。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

编辑应用程序的`Info.plist`文件中，切换到**源**查看和添加`NSSiriUsageDescription`键，其描述应用程序使用 Siri 和内容的方式的字符串值将发送的数据类型。 例如，MonkeyChat 应用可能会说"MonkeyChat 联系人将发送至 Siri":

[![](implementing-sirikit-images/request01.png "Info.plist 编辑器中 NSSiriUsageDescription")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

编辑应用程序的`Info.plist`文件，并添加`NSSiriUsageDescription`键，其描述应用程序使用 Siri 和内容的方式的字符串值将发送的数据类型。 例如，MonkeyChat 应用可能会说"MonkeyChat 联系人将发送至 Siri":

[![](implementing-sirikit-images/request01w.png "Info.plist 编辑器中 NSSiriUsageDescription")](implementing-sirikit-images/request01w.png#lightbox)

-----

调用`RequestSiriAuthorization`方法的`INPreferences`类应用程序首次启动时。 编辑`AppDelegate.cs`类，并使`FinishedLaunching`方法看起来像以下：


```csharp
using Intents;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{

    // Request access to Siri
    INPreferences.RequestSiriAuthorization ((INSiriAuthorizationStatus status) => {
        // Respond to returned status
        switch (status) {
        case INSiriAuthorizationStatus.Authorized:
            break;
        case INSiriAuthorizationStatus.Denied:
            break;
        case INSiriAuthorizationStatus.NotDetermined:
            break;
        case INSiriAuthorizationStatus.Restricted:
            break;
        }
    });


    return true;
}
```

第一次调用此方法时，警报则会显示提示用户允许应用程序访问使用 Siri。 开发人员添加到消息`NSSiriUsageDescription`更高版本将显示在此警报。 如果用户最初拒绝访问，他们可以使用**设置**应用授予应用的访问权限。

在任何时候，应用程序可以检查应用程序可以通过调用访问 Siri`SiriAuthorizationStatus`方法的`INPreferences`类。

### <a name="localization-and-siri"></a>本地化和 Siri

在 iOS 设备，用户就能够为不同的 Siri 然后系统默认选择语言。 应用程序时使用本地化的数据时，将需要使用`SiriLanguageCode`方法的`INPreferences`类，以获取从 Siri 语言代码。 例如：

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>添加用户特定词汇

用户特定词汇打算提供单词或短语是唯一的应用程序的单个用户。 作为一组有序术语中，在具有列表的开头的最重要条款的用户的最高有效使用优先级排序，将在运行时从主应用程序 （不应用扩展） 提供这些。

用户特定词汇必须属于以下类别之一：

- 联系人姓名 （即未受联系人 framework）。
- 照片标记。
- 照片唱片集名称。
- 健身名称。

若要将注册为自定义词汇的术语，在选择时仅选择条款可能被误解的某个不熟悉应用程序。 永远不会注册常用术语如"My 健身"或"我的唱片集"。 例如，MonkeyChat 应用程序将注册与用户的通讯簿中的每个联系人的昵称。

应用程序通过调用提供用户特定词汇`SetVocabularyStrings`方法`INVocabulary`类，并传入`NSOrderedSet`主应用中的集合。 应用应始终调用`RemoveAllVocabularyStrings`方法首先，需要删除任何现有的条款，然后再添加新的。 例如：

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using Foundation;
using Intents;

namespace MonkeyChatCommon
{
    public class MonkeyAddressBook : NSObject
    {
        #region Computed Properties
        public List<MonkeyContact> Contacts { get; set; } = new List<MonkeyContact> ();
        #endregion

        #region Constructors
        public MonkeyAddressBook ()
        {
        }
        #endregion

        #region Public Methods
        public NSOrderedSet<NSString> ContactNicknames ()
        {
            var nicknames = new NSMutableOrderedSet<NSString> ();

            // Sort contacts by the last time used
            var query = Contacts.OrderBy (contact => contact.LastCalledOn);

            // Assemble ordered list of nicknames by most used to least
            foreach (MonkeyContact contact in query) {
                nicknames.Add (new NSString (contact.ScreenName));
            }

            // Return names
            return new NSOrderedSet<NSString> (nicknames.AsSet ());
        }

        // This method MUST only be called on a background thread!
        public void UpdateUserSpecificVocabulary ()
        {
            // Clear any existing vocabulary
            INVocabulary.SharedVocabulary.RemoveAllVocabularyStrings ();

            // Register new vocabulary
            INVocabulary.SharedVocabulary.SetVocabularyStrings (ContactNicknames (), INVocabularyStringType.ContactName);
        }
        #endregion
    }
}
```

利用此代码，它可能会调用，如下所示：

```csharp
using System;
using System.Threading;
using UIKit;
using MonkeyChatCommon;
using Intents;

namespace MonkeyChat
{
    public partial class ViewController : UIViewController
    {
        #region AppDelegate Access
        public AppDelegate ThisApp {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do we have access to Siri?
            if (INPreferences.SiriAuthorizationStatus == INSiriAuthorizationStatus.Authorized) {
                // Yes, update Siri's vocabulary
                new Thread (() => {
                    Thread.CurrentThread.IsBackground = true;
                    ThisApp.AddressBook.UpdateUserSpecificVocabulary ();
                }).Start ();
            }
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

> [!IMPORTANT]
> Siri 为提示将自定义词汇，并将合并作为很多尽可能的术语。 但是，空间是有限使重要注册自定义词汇_仅_可能令人困惑，因此保持在最低限度的已注册的词的总数目的术语。

有关详细信息，请参阅我们[用户特定词汇参考](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[指定自定义词汇参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

### <a name="adding-app-specific-vocabulary"></a>添加特定于应用词汇

应用特定词汇定义的特定字词和短语将已知的所有应用程序的用户，例如车辆类型或健身名称。 由于这些是应用程序的一部分，它们在定义`AppIntentVocabulary.plist`文件作为主应用程序捆绑包的一部分。 此外，还应本地化这些单词和短语。

特定于应用汇术语必须属于以下类别之一：

- 写选项。
- 健身名称。

应用特定词汇文件包含两个根级别键：

- `ParameterVocabularies` **所需**-定义应用的自定义的条款和它们适用于意向参数。
- `IntentPhrases` **可选**-包含使用自定义的术语中定义的示例短语`ParameterVocabularies`。

在每个条目`ParameterVocabularies`必须指定一个 ID 字符串、 术语和术语适用于的目的。 此外，单个字词可能适用于多个目的。

有关可接受的值和所需的文件结构的完整列表，请参阅 Apple[应用词汇文件格式参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)。

若要添加`AppIntentVocabulary.plist`到应用程序项目文件中，执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 右键单击项目名称在**解决方案资源管理器**，然后选择**添加** > **新文件...**  >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "添加的属性列表")](implementing-sirikit-images/plist01.png#lightbox)
2. 双击`AppIntentVocabulary.plist`文件中**解决方案资源管理器**打开进行编辑。
3. 单击**+** 若要添加的键，设置**名称**到`ParameterVocabularies`并**类型**到`Array`:

    [![](implementing-sirikit-images/plist02.png "将名称设置为 ParameterVocabularies 和到数组类型")](implementing-sirikit-images/plist02.png#lightbox)
4. 展开`ParameterVocabularies`然后单击**+** 按钮，然后设置**类型**到`Dictionary`:

    [![](implementing-sirikit-images/plist03.png "将类型设置为字典")](implementing-sirikit-images/plist03.png#lightbox)
5. 单击**+** 若要添加新的密钥，请设置**名称**到`ParameterNames`并**类型**到`Array`:

    [![](implementing-sirikit-images/plist04.png "将名称设置为 ParameterNames 和到数组类型")](implementing-sirikit-images/plist04.png#lightbox)
6. 单击**+** 若要添加的新键**类型**的`String`和作为其中一个可用的参数名称的值。 例如， `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05.png#lightbox)
7. 添加`ParameterVocabulary`关键`ParameterVocabularies`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist06.png "将 ParameterVocabulary 密钥添加到具有数组类型的 ParameterVocabularies 键")](implementing-sirikit-images/plist06.png#lightbox)
8. 添加的新键**类型**的`Dictionary`:

    [![](implementing-sirikit-images/plist07.png "添加字典类型的新键")](implementing-sirikit-images/plist07.png#lightbox)
9. 添加`VocabularyItemIdentifier`键，其**类型**的`String`和指定的期限内的唯一 ID:

    [![](implementing-sirikit-images/plist08.png "添加具有字符串类型的 VocabularyItemIdentifier 键，并指定一个唯一的 ID")](implementing-sirikit-images/plist08.png#lightbox)
10. 添加`VocabularyItemSynonyms`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist09.png "添加具有数组类型的 VocabularyItemSynonyms 键")](implementing-sirikit-images/plist09.png#lightbox)
11. 添加的新键**类型**的`Dictionary`:

    [![](implementing-sirikit-images/plist10.png "添加字典类型的新键")](implementing-sirikit-images/plist10.png#lightbox)
12. 添加`VocabularyItemPhrase`键，其**类型**的`String`和术语定义应用程序：

    [![](implementing-sirikit-images/plist11.png "添加具有字符串类型和应用程序所定义的术语的 VocabularyItemPhrase 键")](implementing-sirikit-images/plist11.png#lightbox)
13. 添加`VocabularyItemPronunciation`键，其**类型**的`String`和术语的语音发音：

    [![](implementing-sirikit-images/plist12.png "添加具有字符串类型和术语的语音发音的 VocabularyItemPronunciation 键")](implementing-sirikit-images/plist12.png#lightbox)
14. 添加`VocabularyItemExamples`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist13.png "添加具有数组类型的 VocabularyItemExamples 键")](implementing-sirikit-images/plist13.png#lightbox)
15. 添加了一些`String`术语的示例使用具有键：

    [![](implementing-sirikit-images/plist14.png "添加使用的术语的示例使用的几个字符串键")](implementing-sirikit-images/plist14.png#lightbox)
16. 对应用程序需要定义任何其他自定义条款重复上述步骤。
17. 折叠`ParameterVocabularies`密钥。
18. 添加`IntentPhrases`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist15.png "添加具有数组类型的 IntentPhrases 键")](implementing-sirikit-images/plist15.png#lightbox)
19. 添加的新键**类型**的`Dictionary`:

    [![](implementing-sirikit-images/plist16.png "添加字典类型的新键")](implementing-sirikit-images/plist16.png#lightbox)
20. 添加`IntentName`键，其**类型**的`String`和意向。 例如：

    [![](implementing-sirikit-images/plist17.png "添加该示例使用字符串类型和意图的 IntentName 密钥")](implementing-sirikit-images/plist17.png#lightbox)
21. 添加`IntentExamples`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist18.png "添加具有数组类型的 IntentExamples 键")](implementing-sirikit-images/plist18.png#lightbox)
22. 添加了一些`String`术语的示例使用具有键：

    [![](implementing-sirikit-images/plist19.png "添加使用的术语的示例使用的几个字符串键")](implementing-sirikit-images/plist19.png#lightbox)
23. 对应用程序需要提供的示例用法任何意向重复上述步骤。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 右键单击项目名称在**解决方案资源管理器**，然后选择**添加 > 新建项...> Apple > 属性列表 > Info.plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "添加新 Info.plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. 双击`AppIntentVocabulary.plist`文件中**解决方案资源管理器**打开进行编辑。
3. 单击**+** 若要添加的键，设置**名称**到`ParameterVocabularies`并**类型**到`Array`:

    [![](implementing-sirikit-images/plist02w.png "将名称设置为 ParameterVocabularies 和到数组类型")](implementing-sirikit-images/plist02w.png#lightbox)
4. 展开`ParameterVocabularies`然后单击**+** 按钮，然后设置**类型**到`Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "将类型设置为字典")](implementing-sirikit-images/plist03w.png#lightbox)
5. 单击**+** 若要添加新的密钥，请设置**名称**到`ParameterNames`并**类型**到`Array`:

    [![](implementing-sirikit-images/plist04w.png "将名称设置为 ParameterNames 和到数组类型")](implementing-sirikit-images/plist04w.png#lightbox)
6. 单击**+** 若要添加的新键**类型**的`String`和作为其中一个可用的参数名称的值。 例如， `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05w.png#lightbox)
7. 添加`ParameterVocabulary`关键`ParameterVocabularies`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist06w.png "将 ParameterVocabulary 密钥添加到具有数组类型的 ParameterVocabularies 键")](implementing-sirikit-images/plist06w.png#lightbox)
8. 添加的新键**类型**的`Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "添加字典类型的新键")](implementing-sirikit-images/plist07w.png#lightbox)
9. 添加`VocabularyItemIdentifier`键，其**类型**的`String`和指定的期限内的唯一 ID:

    [![](implementing-sirikit-images/plist08w.png "添加具有字符串类型的 VocabularyItemIdentifier 键，并指定的期限内的唯一 ID")](implementing-sirikit-images/plist08w.png#lightbox)
10. 添加`VocabularyItemSynonyms`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist09w.png "添加具有数组类型的 VocabularyItemSynonyms 键")](implementing-sirikit-images/plist09w.png#lightbox)
11. 添加的新键**类型**的`Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "添加字典类型的新键")](implementing-sirikit-images/plist10w.png#lightbox)
12. 添加`VocabularyItemPhrase`键，其**类型**的`String`和术语定义应用程序：

    [![](implementing-sirikit-images/plist11w.png "添加具有字符串类型和应用程序所定义的术语的 VocabularyItemPhrase 键")](implementing-sirikit-images/plist11w.png#lightbox)
13. 添加`VocabularyItemPronunciation`键，其**类型**的`String`和术语的语音发音：

    [![](implementing-sirikit-images/plist12w.png "添加具有字符串类型和术语的语音发音的 VocabularyItemPronunciation 键")](implementing-sirikit-images/plist12w.png#lightbox)
14. 添加`VocabularyItemExamples`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist13w.png "添加具有数组类型的 VocabularyItemExamples 键")](implementing-sirikit-images/plist13w.png#lightbox)
15. 添加了一些`String`术语的示例使用具有键：

    [![](implementing-sirikit-images/plist14w.png "添加使用的术语的示例使用的几个字符串键")](implementing-sirikit-images/plist14w.png#lightbox)
16. 对应用程序需要定义任何其他自定义条款重复上述步骤。
17. 折叠`ParameterVocabularies`密钥。
18. 添加`IntentPhrases`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist15w.png "添加具有数组类型的 IntentPhrases 键")](implementing-sirikit-images/plist15w.png#lightbox)
19. 添加的新键**类型**的`Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "添加字典类型的新键")](implementing-sirikit-images/plist16w.png#lightbox)
20. 添加`IntentName`键，其**类型**的`String`和意向。 例如：

    [![](implementing-sirikit-images/plist17w.png "添加该示例使用字符串类型和意图的 IntentName 密钥")](implementing-sirikit-images/plist17w.png#lightbox)
21. 添加`IntentExamples`键，其**类型**的`Array`:

    [![](implementing-sirikit-images/plist18w.png "添加具有数组类型的 IntentExamples 键")](implementing-sirikit-images/plist18w.png#lightbox)
22. 添加了一些`String`术语的示例使用具有键：

    [![](implementing-sirikit-images/plist19w.png "添加使用的术语的示例使用的几个字符串键")](implementing-sirikit-images/plist19w.png#lightbox)
23. 对应用程序需要提供的示例用法任何意向重复上述步骤。

-----

> [!IMPORTANT]
> `AppIntentVocabulary.plist`将注册使用 Siri 上测试在开发和它的设备可能需要一些时间 Siri 以合并自定义词汇。 因此，测试人员将需要等待几分钟，然后再尝试在更新后测试应用程序特定词汇。

有关详细信息，请参阅我们[应用程序特定词汇参考](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[指定自定义词汇参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

## <a name="adding-an-intents-extension"></a>添加的 Intents 扩展

现在，已准备好应用程序，采用 SiriKit，开发人员将需要将一个 （或多个） Intents 扩展添加到解决方案后，可处理 Siri 集成所需的意图。

每个所需的 Intents 扩展，请执行以下操作：

- 添加到 Xamarin.iOS 应用程序解决方案的 Intents 扩展项目。
- 配置 Intents 扩展`Info.plist`文件。
- 修改的 Intents 扩展的主类。

有关详细信息，请参阅我们[Intents 扩展引用](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[创建的是 Intents 扩展引用](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1)。

### <a name="creating-the-extension"></a>创建扩展

若要添加到解决方案的 Intents 扩展，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 右键单击**解决方案名称**中**Solution Pad** ，然后选择**添加** > **添加新项目...**.
2. 从对话框中选择**iOS** > **扩展** > **意向扩展**然后单击**下一步**按钮： 

    [![](implementing-sirikit-images/intents05.png "选择意向扩展")](implementing-sirikit-images/intents05.png#lightbox)
3. 接下来输入**名称**意向扩展并单击**下一步**按钮： 

    [![](implementing-sirikit-images/intents06.png "输入意向扩展的名称")](implementing-sirikit-images/intents06.png#lightbox)
4. 最后，单击**创建**按钮将意向扩展添加到应用程序解决方案： 

    [![](implementing-sirikit-images/intents07.png "将意向扩展添加到应用程序解决方案")](implementing-sirikit-images/intents07.png#lightbox)
5. 在中**解决方案资源管理器**，右键单击**引用**新创建的意向扩展的文件夹。 检查常见的共享的代码库项目 （即上面创建的应用） 的名称，然后单击**确定**按钮： 

    [![](implementing-sirikit-images/intents08.png "选择公共共享的代码库项目的名称")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 右键单击**解决方案名称**中**解决方案资源管理器**，然后选择**添加** > **添加新项目...**.
2. 从对话框中选择**可视化C#> iOS 扩展 > 意向扩展**然后单击**下一步**按钮：

    [![](implementing-sirikit-images/intents05.w157-sml.png "选择意向扩展")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. 接下来输入**名称**意向扩展并单击**确定**按钮。
1. 在中**解决方案资源管理器**，右键单击**引用**新建的 Intents 扩展的文件夹，然后选择**添加 > 引用**。 检查常见的共享的代码库项目 （即上面创建的应用） 的名称，然后单击**确定**按钮：

    [![](implementing-sirikit-images/intents08w.png "选择公共共享的代码库项目的名称")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

重复这些步骤的意图扩展数 (基于[构建于扩展的应用](#Architecting-the-App-for-Extensions)上面部分)，该应用需要。

### <a name="configuring-the-infoplist"></a>配置 Info.plist

对于每个已添加到应用程序的解决方案的 Intents 扩展，必须在配置`Info.plist`文件以使用应用。

就像任何典型的应用程序扩展，应用将有现有的键`NSExtension`和`NSExtensionAttributes`。 Intents 扩展有必须配置的两个新属性：

[![](implementing-sirikit-images/intents01.png "必须配置两个新属性")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** -是必需的包含应用程序想要支持从意向扩展的意向类名称的数组。
- **IntentsRestrictedWhileLocked** -是应用程序以指定扩展的锁定屏幕行为的可选密钥。 它包含应用程序需要要求用户在登录后才能使用意向扩展中的意图类名称的数组。

若要配置意向扩展`Info.plist`文件中中, 双击该**解决方案资源管理器**打开进行编辑。 接下来，切换到**源**视图，然后展开`NSExtension`和`NSExtensionAttributes`在编辑器中的密钥：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "在编辑器中的 NSExtension 和 NSExtensionAttributes 键")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "在编辑器中的 NSExtension 和 NSExtensionAttributes 键")](implementing-sirikit-images/intents02w.png#lightbox)

-----

展开`IntentsSupported`密钥，然后添加此扩展将支持任何意向类的名称。 对于示例 MonkeyChat 应用，它支持`INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "INSendMessageIntent 密钥")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "INSendMessageIntent 密钥")](implementing-sirikit-images/intents09w.png#lightbox)

-----

如果应用程序可以选择要求用户登录到设备后，若要使用给定的意向，请展开`IntentRestrictedWhileLocked`密钥，然后添加具有受限访问权限的方式的类名称。 对于示例 MonkeyChat 应用，用户必须登录才能发送聊天消息，因此我们添加了`INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "添加了的 INSendMessageIntent 密钥")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "添加了的 INSendMessageIntent 密钥")](implementing-sirikit-images/intents10w.png#lightbox)

-----


有关可用意向域的完整列表，请参阅 Apple 的[意向域引用](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>配置的主类

接下来，开发人员将需要配置到 Siri 中充当意向扩展的主入口点的主类。 它必须是的子类`INExtension`这符合`IINIntentHandler`委托。 例如：

```csharp
using System;
using System.Collections.Generic;

using Foundation;
using Intents;

namespace MonkeyChatIntents
{
    [Register ("IntentHandler")]
    public class IntentHandler : INExtension, IINSendMessageIntentHandling, IINSearchForMessagesIntentHandling, IINSetMessageAttributeIntentHandling
    {
        #region Constructors
        protected IntentHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override NSObject GetHandler (INIntent intent)
        {
            // This is the default implementation.  If you want different objects to handle different intents,
            // you can override this and return the handler you want for that particular intent.

            return this;
        }
        #endregion
        ...
    }
}
```

应用上的意向扩展的主类，必须实现的一个孤立方法`GetHandler`方法。 此方法通过 SiriKit 传递意向，而该应用程序必须返回**意向处理程序**给定意向的类型匹配的。

由于示例 MonkeyChat 应用仅处理一种意图，它返回自身中`GetHandler`方法。 如果扩展插件处理多个目的，开发人员会为每个意向类型添加类，并返回此处基于实例`Intent`传递给该方法。

### <a name="handling-the-resolve-stage"></a>处理解决阶段

解决阶段其中意向扩展将阐明并验证参数传入 Siri 从，并且已通过用户的会话设置。

获取从 Siri 发送每个参数，对于没有`Resolve`方法。 应用程序需要实现此方法对于应用程序可能需要使用 Siri 的帮助以从用户获得正确答案的每个参数。

对于示例 MonkeyChat 应用程序，意图扩展将需要将消息发送到一个或多个收件人。 为每个收件人列表中，该扩展将需要进行联系搜索可以具有以下结果：

- 找到一个匹配的联系人。
- 位于两个或多个匹配的联系人。
- 不找到任何匹配的联系人。

此外，MonkeyChat 需要消息的正文的内容。 如果用户未提供此，使用 Siri 需要提示用户输入内容。

意向扩展需要适当地处理每种情况。


```csharp
[Export ("resolveRecipientsForSearchForMessages:withCompletion:")]
public void ResolveRecipients (INSendMessageIntent intent, Action<INPersonResolutionResult []> completion)
{
    var recipients = intent.Recipients;
    // If no recipients were provided we'll need to prompt for a value.
    if (recipients.Length == 0) {
        completion (new INPersonResolutionResult [] { (INPersonResolutionResult)INPersonResolutionResult.NeedsValue });
        return;
    }

    var resolutionResults = new List<INPersonResolutionResult> ();

    foreach (var recipient in recipients) {
        var matchingContacts = new INPerson [] { recipient }; // Implement your contact matching logic here to create an array of matching contacts
        if (matchingContacts.Length > 1) {
            // We need Siri's help to ask user to pick one from the matches.
            resolutionResults.Add (INPersonResolutionResult.GetDisambiguation (matchingContacts));
        } else if (matchingContacts.Length == 1) {
            // We have exactly one matching contact
            resolutionResults.Add (INPersonResolutionResult.GetSuccess (recipient));
        } else {
            // We have no contacts matching the description provided
            resolutionResults.Add ((INPersonResolutionResult)INPersonResolutionResult.Unsupported);
        }
    }

    completion (resolutionResults.ToArray ());
}

[Export ("resolveContentForSendMessage:withCompletion:")]
public void ResolveContent (INSendMessageIntent intent, Action<INStringResolutionResult> completion)
{
    var text = intent.Content;
    if (!string.IsNullOrEmpty (text))
        completion (INStringResolutionResult.GetSuccess (text));
    else
        completion ((INStringResolutionResult)INStringResolutionResult.NeedsValue);
}
```

有关详细信息，请参阅我们[解决阶段引用](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple [Resolving 和处理意向参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1)。

### <a name="handling-the-confirm-stage"></a>处理确认阶段

在确认阶段是意向扩展在其中检查以查看其具有的所有信息以满足用户的请求。 应用程序需要发送确认此过程将内容的所有支持的详细信息是有关发生 siri 因此它可以确认的用户，这是预期的操作。

```csharp
[Export ("confirmSendMessage:completion:")]
public void ConfirmSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Verify user is authenticated and the app is ready to send a message.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Ready, userActivity);
    completion (response);
}
```

有关详细信息，请参阅我们[确认阶段引用](~/ios/platform/sirikit/understanding-sirikit.md)。

### <a name="processing-the-intent"></a>处理意向

这是意向扩展实际执行任务以完成用户的请求并将结果传递回 Siri，因此可以通知用户的点。


```csharp
public void HandleSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Implement the application logic to send a message here.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, userActivity);
    completion (response);
}

public void HandleSearchForMessages (INSearchForMessagesIntent intent, Action<INSearchForMessagesIntentResponse> completion)
{
    // Implement the application logic to find a message that matches the information in the intent.
    ...

    var userActivity = new NSUserActivity (nameof (INSearchForMessagesIntent));
    var response = new INSearchForMessagesIntentResponse (INSearchForMessagesIntentResponseCode.Success, userActivity);

    // Initialize with found message's attributes
    var sender = new INPerson (new INPersonHandle ("sarah@example.com", INPersonHandleType.EmailAddress), null, "Sarah", null, null, null);
    var recipient = new INPerson (new INPersonHandle ("+1-415-555-5555", INPersonHandleType.PhoneNumber), null, "John", null, null, null);
    var message = new INMessage ("identifier", "I am so excited about SiriKit!", NSDate.Now, sender, new INPerson [] { recipient });
    response.Messages = new INMessage [] { message };
    completion (response);
}

public void HandleSetMessageAttribute (INSetMessageAttributeIntent intent, Action<INSetMessageAttributeIntentResponse> completion)
{
    // Implement the application logic to set the message attribute here.
    ...

    var userActivity = new NSUserActivity (nameof (INSetMessageAttributeIntent));
    var response = new INSetMessageAttributeIntentResponse (INSetMessageAttributeIntentResponseCode.Success, userActivity);
    completion (response);
}
```

有关详细信息，请参阅我们[处理阶段引用](~/ios/platform/sirikit/understanding-sirikit.md)。

## <a name="adding-an-intents-ui-extension"></a>添加的 Intents UI 扩展

可选的 Intents UI 扩展提供了机会，将应用程序的 UI 和品牌到 Siri 体验，并使用户感到连接到该应用程序。 借助此扩展的品牌以及 visual 和其他信息转换脚本，可以将应用程序。

[![](implementing-sirikit-images/intentsui01.png "Intents UI 扩展输出示例")](implementing-sirikit-images/intentsui01.png#lightbox)

就像 Intents 扩展，开发人员将 Intents UI 扩展为执行以下步骤：

- 添加到 Xamarin.iOS 应用程序解决方案的 Intents UI 扩展项目。
- 配置 Intents UI 扩展`Info.plist`文件。
- 修改的 Intents UI 扩展的主类。

有关详细信息，请参阅我们[Intents UI 扩展引用](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[提供的自定义接口引用](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1)。

### <a name="creating-the-extension"></a>创建扩展

若要添加到解决方案的 Intents UI 扩展，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 右键单击**解决方案名称**中**Solution Pad** ，然后选择**添加** > **添加新项目...**.
2. 从对话框中选择**iOS** > **扩展** > **意向 UI 扩展**然后单击**下一步**按钮： 

    [![](implementing-sirikit-images/intents11.png "选择意向 UI 扩展")](implementing-sirikit-images/intents11.png#lightbox)
3. 接下来输入**名称**意向扩展并单击**下一步**按钮： 

    [![](implementing-sirikit-images/intents12.png "输入意向扩展的名称")](implementing-sirikit-images/intents12.png#lightbox)
4. 最后，单击**创建**按钮将意向扩展添加到应用程序解决方案： 

    [![](implementing-sirikit-images/intents13.png "将意向扩展添加到应用程序解决方案")](implementing-sirikit-images/intents13.png#lightbox)
5. 在中**解决方案资源管理器**，右键单击**引用**新创建的意向扩展的文件夹。 检查常见的共享的代码库项目 （即上面创建的应用） 的名称，然后单击**确定**按钮： 

    [![](implementing-sirikit-images/intents14.png "选择公共共享的代码库项目的名称")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 右键单击**解决方案名称**中**解决方案资源管理器**，然后选择**添加** > **添加新项目...**
2. 从对话框中选择**iOS** > **扩展** > **意向 UI 扩展**然后单击**下一步**按钮。
3. 接下来输入**名称**意向扩展并单击**确定**按钮。
5. 在中**解决方案资源管理器**，右键单击**引用**新创建的意向扩展的文件夹。 检查常见的共享的代码库项目 （即上面创建的应用） 的名称，然后单击**确定**按钮。
    
-----

### <a name="configuring-the-infoplist"></a>配置 Info.plist

配置 Intents UI 扩展`Info.plist`文件以使用应用。

就像任何典型的应用程序扩展，应用将有现有的键`NSExtension`和`NSExtensionAttributes`。 Intents 扩展是一个新属性，必须配置：

[![](implementing-sirikit-images/intents03.png "必须配置一个新属性")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported**是必需的包含应用程序想要支持从意向扩展的意向类名称的数组。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要配置意向 UI 扩展`Info.plist`文件中中, 双击该**解决方案资源管理器**打开进行编辑。 接下来，切换到**源**视图，然后展开`NSExtension`和`NSExtensionAttributes`在编辑器中的密钥：

[![](implementing-sirikit-images/intents04.png "在编辑器中的 NSExtension 和 NSExtensionAttributes 键")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要配置意向 UI 扩展`Info.plist`文件中中, 双击该**解决方案资源管理器**打开进行编辑。 展开`NSExtension`和`NSExtensionAttributes`在编辑器中的密钥：

[![](implementing-sirikit-images/intents04w.png "在编辑器中参阅 NSExtension 和 NSExtensionAttributes 键")](implementing-sirikit-images/intents04w.png#lightbox)

-----

展开`IntentsSupported`密钥，然后添加此扩展将支持任何意向类的名称。 对于示例 MonkeyChat 应用，它支持`INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "INSendMessageIntent 密钥")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "INSendMessageIntent 密钥")](implementing-sirikit-images/intents15w.png#lightbox)

-----

有关可用意向域的完整列表，请参阅 Apple 的[意向域引用](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>配置的主类

配置到 Siri 中充当意向 UI 扩展插件的主入口点的主类。 它必须是的子类`UIViewController`这符合`IINUIHostedViewController`接口。 例如：

```csharp
using System;
using Foundation;
using CoreGraphics;
using Intents;
using IntentsUI;
using UIKit;

namespace MonkeyChatIntentsUI
{
    public partial class IntentViewController : UIViewController, IINUIHostedViewControlling
    {
        #region Constructors
        protected IntentViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }

        public override void DidReceiveMemoryWarning ()
        {
            // Releases the view if it doesn't have a superview.
            base.DidReceiveMemoryWarning ();

            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Public Methods
        [Export ("configureWithInteraction:context:completion:")]
        public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
        {
            // Do configuration here, including preparing views and calculating a desired size for presentation.

            if (completion != null)
                completion (DesiredSize ());
        }

        [Export ("desiredSize:")]
        public CGSize DesiredSize ()
        {
            return ExtensionContext.GetHostedViewMaximumAllowedSize ();
        }
        #endregion
    }
}
```

将通过使用 Siri`INInteraction`类实例与`Configure`方法的`UIViewController`内意向 UI 扩展实例。

`INInteraction`对象提供给扩展的信息的三个主要部分：

1. 正在处理的意向对象。
2. 中的意图响应对象`Confirm`和`Handle`意向扩展方法。
3. 定义应用程序和 Siri 之间的交互状态交互状态。

`UIViewController`实例是使用 Siri 与的交互的原则和从它继承，因此`UIViewController`，它有权访问的所有 UIKit 功能。

当调用 Siri`Configure`方法的`UIViewController`将指出视图控制器或者将托管在 Siri Snippit 或映射卡的视图上下文中传递。

使用 Siri 也将该应用需要应用程序已完成对其进行配置后将返回该视图所需的大小的完成处理程序中。

### <a name="design-the-ui-in-ios-designer"></a>IOS 设计器中的用户界面设计

Intents UI 扩展用户界面中 iOS 设计器布局。 双击该扩展`MainInterface.storyboard`文件中**解决方案资源管理器**打开进行编辑。 将在所有所需的 UI 元素，以构建用户界面并保存所做的更改。

> [!IMPORTANT]
> 虽然可以添加交互元素，例如`UIButtons`或`UITextFields`意向 UI 扩展到`UIViewController`、 这些严格禁止用作意向 UI 中非交互式的用户将不能与之交互。

### <a name="wire-up-the-user-interface"></a>布置的用户界面

Intents UI 扩展用户界面创建 iOS 设计器中，编辑`UIViewController`子类并重写`Configure`方法，如下所示：

```csharp
[Export ("configureWithInteraction:context:completion:")]
public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
{
    // Do configuration here, including preparing views and calculating a desired size for presentation.
    ...

    // Return desired size
    if (completion != null)
        completion (DesiredSize ());
}

[Export ("desiredSize:")]
public CGSize DesiredSize ()
{
    return ExtensionContext.GetHostedViewMaximumAllowedSize ();
}
```

### <a name="overriding-the-default-siri-ui"></a>重写默认 Siri UI

Intents UI 扩展将始终显示以及其他使用 Siri 内容，例如应用图标和名称在 UI 的顶部或，着眼于基于，可能会在底部显示 （如发送或取消） 按钮。

有少数情况下，应用可以替换 Siri 向用户显示默认情况下，例如消息传送的信息或其中应用程序可以使用一个针对应用程序替换默认的体验的映射。

如果需要重写默认 Siri UI 元素 Intents UI 扩展`UIViewController`子类需要实现`IINUIHostedViewSiriProviding`接口和参加专门显示特定界面元素。

将以下代码添加到`UIViewController`子类来告诉 Siri 意向 UI 扩展已显示的消息内容：

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>注意事项

Apple 建议开发人员考虑设计和实现意向 UI 扩展时，考虑以下注意事项：

- **是有意识的内存使用量**-因为意图 UI 扩展插件是临时的仅显示在短时间，系统会施加更严格内存约束不是与完整的应用一起使用。
- **请考虑最小值和最大的视图大小**-确保意图 UI 扩展看起来不错对每个 iOS 设备类型、 大小和方向。 此外，应用会发送回 Siri 所需的大小可能不能授予。
- **使用灵活的和自适应的布局模式**-同样，若要确保用户界面看起来很棒的每台设备上。

## <a name="summary"></a>总结

本文已涵盖 SiriKit，并显示如何将提供的 iOS 设备上使用 Siri 和 Maps 应用的用户可以访问的服务的 Xamarin.iOS 应用添加。




## <a name="related-links"></a>相关链接

- [ElizaChat 示例](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [意向框架引用](https://developer.apple.com/reference/intents)
- [Intents UI 框架引用](https://developer.apple.com/reference/intentsui)
- [意向域引用](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
