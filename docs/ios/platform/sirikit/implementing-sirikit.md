---
title: 在 Xamarin 中实现 SiriKit
description: 本文档介绍在 Xamarin iOS 应用程序中实现 SiriKit 支持所需的步骤。 它讨论了方法扩展和意向 UI 扩展。
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: a80caca0b8c8c48a468b20f63467357300bd6de1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031635"
---
# <a name="implementing-sirikit-in-xamarinios"></a>在 Xamarin 中实现 SiriKit

_本文介绍了在 Xamarin iOS 应用程序中实现 SiriKit 支持所需的步骤。_

SiriKit 的新功能允许 Xamarin iOS 应用提供用户可使用 Siri 和 iOS 设备上的地图应用程序访问的服务。 本文介绍通过添加所需的意图扩展、意向 UI 扩展和词汇，在 Xamarin iOS 应用程序中实现 SiriKit 支持所需的步骤。

Siri 适用于**域**的概念、相关任务的一组已知操作。 应用程序与 Siri 之间的每个交互都必须属于其已知的一个服务域，如下所示：

- 音频或视频呼叫。
- 预订一本。
- 管理 workouts。
- 彩信.
- 搜索照片。
- 发送或接收付款。

当用户发出涉及某个应用扩展服务的 Siri 请求时，SiriKit 将向该扩展发送**意向**对象，该对象描述用户的请求以及所有支持数据。 然后，应用扩展会针对给定**意向**生成相应的**响应**对象，并详细说明扩展如何处理请求。

本指南将演示如何在现有应用中包括 SiriKit 支持。 出于本示例的目的，我们将使用虚设的 MonkeyChat 应用：

[![](implementing-sirikit-images/monkeychat01.png "The MonkeyChat icon")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat 保留自己的用户朋友的联系人手册，每个与屏幕名称关联（例如 Bobo），并允许用户通过屏幕名称向每个朋友发送文本聊天。

## <a name="extending-the-app-with-sirikit"></a>用 SiriKit 扩展应用

如[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)指南中所示，使用 SiriKit 扩展应用程序涉及三个主要部分：

[![](implementing-sirikit-images/elements01.png "Extending the App with SiriKit diagram")](implementing-sirikit-images/elements01.png#lightbox)

这些方法包括：

1. **意向扩展**-验证用户的响应，确认应用程序可以处理请求，并实际执行任务以满足用户的请求。
2. **意向 UI 扩展** - *可选*，为 Siri 环境中的响应提供自定义 ui，并可将应用 Ui 和品牌加入 Siri，以丰富用户的体验。
3. **应用**-为应用提供特定于用户的词汇，以帮助 Siri 使用它。 

以下各部分详细介绍了所有这些元素以及将它们包含在应用中的步骤。

## <a name="preparing-the-app"></a>准备应用

SiriKit 是基于扩展构建的，但是，在将任何扩展添加到应用之前，开发人员需要执行几项操作来帮助采用 SiriKit。

### <a name="moving-common-shared-code"></a>移动公共共享代码 

首先，开发人员可以将应用和扩展之间共享的一些常见代码移到_共享项目_、_可移植类库（Pcl）_ 或_本机库_中。

扩展需要能够完成应用程序所执行的所有操作。 在示例 MonkeyChat 应用程序中，查找联系人、添加新联系人、发送消息和检索消息历史记录等。

通过将此通用代码移到共享项目、PCL 或本机库中，可以轻松地在一个常见位置维护代码，并确保扩展和父应用程序为用户提供统一的体验和功能。

在示例应用 MonkeyChat 的情况下，数据模型和处理代码（如网络和数据库访问）将移到本机库。

请执行以下操作：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 启动 Visual Studio for Mac 并打开 MonkeyChat 应用。
2. 在**Solution Pad**中右键单击解决方案名称，然后选择 "**添加** > "**新建项目 ...** "： 

    [![](implementing-sirikit-images/prep01.png "Add a new project")](implementing-sirikit-images/prep01.png#lightbox)
3. 选择 " **iOS** > 库 **" > 类库**，然后单击 "**下一步**" 按钮： 

    [![](implementing-sirikit-images/prep02.png "Select Class Library")](implementing-sirikit-images/prep02.png#lightbox)
4. 输入**名称**`MonkeyChatCommon`，并单击 "**创建**" 按钮： 

    [![](implementing-sirikit-images/prep03.png "Enter MonkeyChatCommon for the Name")](implementing-sirikit-images/prep03.png#lightbox)
5. 右键单击**解决方案资源管理器**中主应用的 "**引用**" 文件夹，然后选择 "**编辑引用 ...** "。选中 " **MonkeyChatCommon** " 项目，并单击 **"确定"** 按钮： 

    [![](implementing-sirikit-images/prep05.png "Check the MonkeyChatCommon project")](implementing-sirikit-images/prep05.png#lightbox)
6. 在**解决方案资源管理器**中，将公共共享代码从主应用拖到本机库。
7. 对于 MonkeyChat，将主应用中的 " **DataModels** " 和 "**处理器**" 文件夹拖到本机库中： 

    [![](implementing-sirikit-images/prep06.png "The DataModels and Processors folders in the Solution Explorer")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 启动 Visual Studio 并打开 MonkeyChat 应用。
2. 在**解决方案资源管理器**中右键单击解决方案名称，然后选择 "**添加** > "**新建项目 ...** "。
3. 选择 **" C# Visual** > **共享项目**"，然后单击 "**下一步**" 按钮： 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Select Class Library")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. 输入**名称**`MonkeyChatCommon`，并单击 "**创建**" 按钮。
5. 右键单击**解决方案资源管理器**中主应用的 "**引用**" 文件夹，然后选择 "**编辑引用 ...** "。选中 " **MonkeyChatCommon** " 项目，并单击 **"确定"** 按钮： 

    [![](implementing-sirikit-images/prep05w.png "Check the MonkeyChatCommon project")](implementing-sirikit-images/prep05w.png#lightbox)
6. 在**解决方案资源管理器**中，将公共共享代码从主应用拖到共享项目。
7. 对于 MonkeyChat，将主应用中的 " **DataModels** " 和 "**处理器**" 文件夹拖到本机库。

-----

编辑已移动到本机库的任何文件，并更改命名空间以与库的命名空间相匹配。 例如，将 `MonkeyChat` 更改为 `MonkeyChatCommon`：

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

接下来，返回主应用，并在应用使用已移动的某个类的任何位置为本机库的命名空间添加 `using` 语句：

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

### <a name="architecting-the-app-for-extensions"></a>为扩展构建应用程序

通常情况下，应用程序将注册多个意向，开发人员需要确保应用程序的设计可用于适当数量的意图扩展。

在应用需要多个意向的情况下，开发人员可以选择将其所有意向处理置于一个意向扩展中，或为每个意向创建一个单独的意图扩展。

如果为每个意向选择创建一个单独的意图扩展，则开发人员最终可能会在每个扩展中复制大量样板代码，并产生大量的处理器和内存开销。

若要帮助在这两个选项之间进行选择，请查看是否有任何方法自然属于一起。 例如，进行音频和视频调用的应用可能希望在单个意向扩展中包括这两种方法，因为它们处理类似的任务，可能会提供最多的代码重用。

对于不适合现有组的任何意向或意向组，请在应用的解决方案中创建一个新的意图扩展来包含它们。

### <a name="setting-the-required-entitlements"></a>设置所需的权利

任何包含 SiriKit 集成的 Xamarin iOS 应用都必须具有正确的权利集。 如果开发人员没有正确设置这些所需的权利，则他们将无法在实际的 iOS 10 （或更高版本）硬件上安装或测试应用，因为 iOS 10 模拟器不支持 SiriKit。

请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击**解决方案资源管理器**中的 `Entitlements.plist` 文件，将其打开进行编辑。
2. 切换到 "**源**" 选项卡。
3. 添加 `com.apple.developer.siri`**属性**，将 "**类型**" 设置为 "`Boolean`"，并将**值**设置为 "`Yes`： 

    [![](implementing-sirikit-images/setup01.png "Add the com.apple.developer.siri Property")](implementing-sirikit-images/setup01.png#lightbox)
4. 保存对该文件所做的更改。
5. 双击 "**解决方案资源管理器**中的**项目文件**，将其打开以进行编辑。
6. 选择 " **IOS 捆绑签名**"，并确保在 "**自定义权利**" 字段中选择 `Entitlements.plist` 文件： 

    [![](implementing-sirikit-images/setup02.png "Select the Entitlements.plist file in the Custom Entitlements field")](implementing-sirikit-images/setup02.png#lightbox)
7. 单击“确定”按钮保存更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击**解决方案资源管理器**中的 `Entitlements.plist` 文件，将其打开进行编辑。
2. 添加 `com.apple.developer.siri`**属性**，将 "**类型**" 设置为 "`Boolean`"，并将**值**设置为 "`Yes`： 

    [![](implementing-sirikit-images/setup01w.png "Add the com.apple.developer.siri Property")](implementing-sirikit-images/setup01w.png#lightbox)
3. 保存对该文件所做的更改。
4. 双击 "**解决方案资源管理器**中的**项目文件**，将其打开以进行编辑。
5. 选择 " **IOS 捆绑签名**"，并确保在 "**自定义权利**" 字段中选择了 "`Entitlements.plist` 文件。

-----

完成后，应用程序的 `Entitlements.plist` 文件应如下所示（在外部编辑器中打开）：

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

### <a name="correctly-provisioning-the-app"></a>正确预配应用

由于 Apple 在 SiriKit 框架中放置了严格的安全性，因此实现 SiriKit 的任何 Xamarin iOS 应用程序都_必须_具有正确的应用程序 ID 和权利（请参阅上面的部分），并且必须使用正确的预配配置文件进行签名。

在 Mac 上执行以下操作：

1. 在 web 浏览器中，导航到[https://developer.apple.com](https://developer.apple.com)并登录到你的帐户。
2. 单击 "**证书**、**标识符**和**配置文件**"。
3. 选择 "**预配配置文件**"，选择 "**应用 id**"，然后单击 " **+** " 按钮。
4. 输入新配置文件的**名称**。
5. 输入 Apple 命名建议后的**捆绑 ID** 。
6. 向下滚动到 "**应用服务**" 部分，选择 " **SiriKit** "，然后单击 "**继续**" 按钮： 

    [![](implementing-sirikit-images/setup03.png "Select SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. 验证所有设置，然后**提交**应用 ID。
8. 选择 "**预配配置文件** > **开发**"，单击 " **+** " 按钮，选择 " **Apple ID**"，然后单击 "**继续**"。
9. 单击 "全**选"，** 然后单击 "**继续**"。
10. 再次单击 "**全选**"，然后单击 "**继续**"。
11. 使用 Apple 命名建议输入**配置文件名称**，然后单击 "**继续**"。
12. 启动 Xcode。
13. 从 "Xcode" 菜单中选择 "**首选项 ...** "
14. 选择 "**帐户**"，然后单击 "**查看详细信息 ...** " 鼠标 

    [![](implementing-sirikit-images/setup04.png "Select Accounts")](implementing-sirikit-images/setup04.png#lightbox)
15. 单击左下角的 "**下载所有配置文件**" 按钮： 

    [![](implementing-sirikit-images/setup05.png "Download All Profiles")](implementing-sirikit-images/setup05.png#lightbox)
16. 请确保在 Xcode 中安装了上面创建的**预配配置文件**。
17. 打开要将 SiriKit 支持添加到 Visual Studio for Mac 中的项目。
18. 双击**解决方案资源管理器**中的 `Info.plist` 文件。
19. 确保**捆绑标识符**与上面 Apple 开发人员门户中创建的标识符匹配： 

    [![](implementing-sirikit-images/setup06.png "The Bundle Identifier")](implementing-sirikit-images/setup06.png#lightbox)
20. 在**解决方案资源管理器**中，选择**项目**。
21. 右键单击该项目，然后选择 "**选项**"。
22. 选择 " **IOS 捆绑签名**"，选择上面创建的**签名标识**和**预配配置文件**： 

    [![](implementing-sirikit-images/setup07.png "Select the Signing Identity and Provisioning Profile")](implementing-sirikit-images/setup07.png#lightbox)
23. 单击“确定”按钮保存更改。

> [!IMPORTANT]
> 测试 SiriKit 仅适用于实际的 iOS 10 硬件设备，而不适用于 iOS 10 模拟器。 如果在实际硬件上安装 SiriKit 启用的 Xamarin iOS 应用时遇到问题，请确保在 Apple 开发人员门户和 Visual Studio for Mac 中正确配置了所需的权利、应用 ID、签名标识符和预配配置文件。

### <a name="requesting-siri-authorization"></a>请求 Siri 授权

在应用添加任何用户特定词汇或意向扩展连接到 Siri 之前，它必须请求用户授权才能访问 Siri。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

编辑应用的 `Info.plist` 文件，切换到 "**源**" 视图，并添加 `NSSiriUsageDescription` 键，其中包含描述应用使用 Siri 的方式以及要发送的数据类型的字符串值。 例如，MonkeyChat 应用可能会说 "MonkeyChat 联系人将发送到 Siri"：

[![](implementing-sirikit-images/request01.png "The NSSiriUsageDescription in the Info.plist editor")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

编辑应用的 `Info.plist` 文件，并添加 `NSSiriUsageDescription` 键，其中包含一个描述应用使用 Siri 的方式以及将发送哪些类型的数据的字符串值。 例如，MonkeyChat 应用可能会说 "MonkeyChat 联系人将发送到 Siri"：

[![](implementing-sirikit-images/request01w.png "The NSSiriUsageDescription in the Info.plist editor")](implementing-sirikit-images/request01w.png#lightbox)

-----

当应用程序首次启动时调用 `INPreferences` 类的 `RequestSiriAuthorization` 方法。 编辑 `AppDelegate.cs` 类，使 `FinishedLaunching` 方法类似于以下内容：

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

第一次调用此方法时，会显示一条警报，提示用户允许应用访问 Siri。 此警报中将显示开发人员添加到上述 `NSSiriUsageDescription` 的消息。 如果用户最初拒绝访问，则他们可以使用 "**设置**" 应用来授予对应用的访问权限。

无论何时，应用都可以通过调用 `INPreferences` 类的 `SiriAuthorizationStatus` 方法来检查应用是否能够访问 Siri。

### <a name="localization-and-siri"></a>本地化和 Siri

在 iOS 设备上，用户可以选择与系统默认值不同的 Siri 语言。 使用本地化数据时，应用需要使用 `INPreferences` 类的 `SiriLanguageCode` 方法从 Siri 获取语言代码。 例如:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>添加用户特定词汇

用户特定词汇将提供应用的各个用户独有的词或短语。 这些设置将在运行时从主应用（而不是应用扩展）作为一组有序项提供，按用户最重要的使用优先级排序，并在列表的开头提供最重要的术语。

用户特定词汇必须属于以下类别之一：

- 联系人姓名（不受 contact 框架管理）。
- 照片标记。
- 相册名称。
- 健身名称。

选择要注册为自定义词汇的术语时，仅选择不熟悉该应用的人员可能会误解的字词。 切勿注册常见条款，如 "我的健身" 或 "我的唱片集"。 例如，MonkeyChat 应用将注册与用户的通讯簿中的每个联系人关联的昵称。

此应用通过调用 `INVocabulary` 类的 `SetVocabularyStrings` 方法，并从主应用传入 `NSOrderedSet` 集合来提供用户特定词汇。 应用应始终首先调用 `RemoveAllVocabularyStrings` 方法，以便在添加新字词之前删除任何现有术语。 例如:

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

此代码准备就绪后，可以按如下所示调用：

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
> Siri 将自定义词汇视为提示，并将尽可能多地合并术语。 但是，自定义词汇的空间有限，_只_注册可能会造成混淆的术语，从而使注册术语的总数保持最小值非常重要。

有关详细信息，请参阅我们的[用户特定词汇参考](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[指定自定义词汇引用](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

### <a name="adding-app-specific-vocabulary"></a>添加特定于应用的词汇

特定于应用程序的词汇定义应用程序的所有用户都知道的特定词和短语，如车辆类型或健身名称。 由于这些是应用程序的一部分，因此在应用程序捆绑包中将它们定义为 `AppIntentVocabulary.plist` 文件中。 此外，应本地化这些字词和短语。

应用特定词汇条款必须属于以下类别之一：

- 向选项。
- 健身名称。

应用特定词汇文件包含两个根级别的密钥：

- `ParameterVocabularies`**必需**-定义应用的自定义术语和意向参数。
- `IntentPhrases` **Optional** -包含使用 `ParameterVocabularies`中定义的自定义术语的示例短语。

`ParameterVocabularies` 中的每个条目都必须指定该术语适用的 ID 字符串、术语和意向。 此外，一个术语可能适用于多个意向。

有关可接受的值和所需文件结构的完整列表，请参阅 Apple 的[应用词汇文件格式参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)。

若要将 `AppIntentVocabulary.plist` 文件添加到应用项目，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 右键单击 "**解决方案资源管理器**中的项目名称，然后选择"**添加** > **新文件 ...** " > **iOS**：

    [![](implementing-sirikit-images/plist01.png "Add a property list")](implementing-sirikit-images/plist01.png#lightbox)
2. 双击**解决方案资源管理器**中的 `AppIntentVocabulary.plist` 文件，将其打开进行编辑。
3. 单击 " **+** " 以添加密钥，将 "**名称**" 设置为 "`ParameterVocabularies`"，并将 "**类型**" 设置为 "`Array`：

    [![](implementing-sirikit-images/plist02.png "Set the Name to ParameterVocabularies and the Type to Array")](implementing-sirikit-images/plist02.png#lightbox)
4. 展开 `ParameterVocabularies` 然后单击 " **+** " 按钮，然后将**类型**设置为 `Dictionary`：

    [![](implementing-sirikit-images/plist03.png "Set the Type to Dictionary")](implementing-sirikit-images/plist03.png#lightbox)
5. 单击 " **+** " 添加新密钥，将 "**名称**" 设置为 "`ParameterNames`"，并将 "**类型**" 设置为 "`Array`：

    [![](implementing-sirikit-images/plist04.png "Set the Name to ParameterNames and the Type to Array")](implementing-sirikit-images/plist04.png#lightbox)
6. 单击 " **+** " 以添加一个新密钥，其**类型**为 `String`，值为可用参数名称之一。 例如，`INStartWorkoutIntent.workoutName`：

    [![](implementing-sirikit-images/plist05.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05.png#lightbox)
7. 将 `ParameterVocabulary` 键添加到 `Array`**类型**的 `ParameterVocabularies` 键：

    [![](implementing-sirikit-images/plist06.png "Add the ParameterVocabulary key to the ParameterVocabularies key with the Type of Array")](implementing-sirikit-images/plist06.png#lightbox)
8. 添加**类型**为 `Dictionary`的新密钥：

    [![](implementing-sirikit-images/plist07.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist07.png#lightbox)
9. 添加具有 `String`**类型**的 `VocabularyItemIdentifier` 项，并为该术语指定唯一 ID：

    [![](implementing-sirikit-images/plist08.png "Add the VocabularyItemIdentifier key with the Type of String and specify a unique ID")](implementing-sirikit-images/plist08.png#lightbox)
10. 添加 `Array`**类型**的 `VocabularyItemSynonyms` 项：

    [![](implementing-sirikit-images/plist09.png "Add the VocabularyItemSynonyms key with the Type of Array")](implementing-sirikit-images/plist09.png#lightbox)
11. 添加**类型**为 `Dictionary`的新密钥：

    [![](implementing-sirikit-images/plist10.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist10.png#lightbox)
12. 添加具有 `String`**类型**的 `VocabularyItemPhrase` 项和应用正在定义的术语：

    [![](implementing-sirikit-images/plist11.png "Add the VocabularyItemPhrase key with the Type of String and the term the app are defining")](implementing-sirikit-images/plist11.png#lightbox)
13. 添加 "`VocabularyItemPronunciation`" 项，其**类型**为 "`String`" 和 "拼音"：

    [![](implementing-sirikit-images/plist12.png "Add the VocabularyItemPronunciation key with the Type of String and the phonetic pronunciation of the term")](implementing-sirikit-images/plist12.png#lightbox)
14. 添加 `Array`**类型**的 `VocabularyItemExamples` 项：

    [![](implementing-sirikit-images/plist13.png "Add the VocabularyItemExamples key with the Type of Array")](implementing-sirikit-images/plist13.png#lightbox)
15. 使用以下术语的示例用法添加几个 `String` 密钥：

    [![](implementing-sirikit-images/plist14.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist14.png#lightbox)
16. 针对应用需要定义的任何其他自定义术语，重复以上步骤。
17. 折叠 `ParameterVocabularies` 键。
18. 添加 `Array`**类型**的 `IntentPhrases` 项：

    [![](implementing-sirikit-images/plist15.png "Add the IntentPhrases key with the Type of Array")](implementing-sirikit-images/plist15.png#lightbox)
19. 添加**类型**为 `Dictionary`的新密钥：

    [![](implementing-sirikit-images/plist16.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist16.png#lightbox)
20. 在示例中添加具有 `String` 和意向**类型**的 `IntentName` 项：

    [![](implementing-sirikit-images/plist17.png "Add the IntentName key with the Type of String and Intent for the example")](implementing-sirikit-images/plist17.png#lightbox)
21. 添加 `Array`**类型**的 `IntentExamples` 项：

    [![](implementing-sirikit-images/plist18.png "Add the IntentExamples key with the Type of Array")](implementing-sirikit-images/plist18.png#lightbox)
22. 使用以下术语的示例用法添加几个 `String` 密钥：

    [![](implementing-sirikit-images/plist19.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist19.png#lightbox)
23. 对于应用程序提供的示例使用情况，请重复上述步骤。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 右键单击 "**解决方案资源管理器**中的项目名称，然后选择"**添加 > 新项 ... "> Apple > 属性列表 > 信息。 info.plist**：

    [![](implementing-sirikit-images/plist01.w157-sml.png "Add a new Info.plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. 双击**解决方案资源管理器**中的 `AppIntentVocabulary.plist` 文件，将其打开进行编辑。
3. 单击 " **+** " 以添加密钥，将 "**名称**" 设置为 "`ParameterVocabularies`"，并将 "**类型**" 设置为 "`Array`：

    [![](implementing-sirikit-images/plist02w.png "Set the Name to ParameterVocabularies and the Type to Array")](implementing-sirikit-images/plist02w.png#lightbox)
4. 展开 `ParameterVocabularies` 然后单击 " **+** " 按钮，然后将**类型**设置为 `Dictionary`：

    [![](implementing-sirikit-images/plist03w.png "Set the Type to Dictionary")](implementing-sirikit-images/plist03w.png#lightbox)
5. 单击 " **+** " 添加新密钥，将 "**名称**" 设置为 "`ParameterNames`"，并将 "**类型**" 设置为 "`Array`：

    [![](implementing-sirikit-images/plist04w.png "Set the Name to ParameterNames and the Type to Array")](implementing-sirikit-images/plist04w.png#lightbox)
6. 单击 " **+** " 以添加一个新密钥，其**类型**为 `String`，值为可用参数名称之一。 例如，`INStartWorkoutIntent.workoutName`：

    [![](implementing-sirikit-images/plist05w.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05w.png#lightbox)
7. 将 `ParameterVocabulary` 键添加到 `Array`**类型**的 `ParameterVocabularies` 键：

    [![](implementing-sirikit-images/plist06w.png "Add the ParameterVocabulary key to the ParameterVocabularies key with the Type of Array")](implementing-sirikit-images/plist06w.png#lightbox)
8. 添加**类型**为 `Dictionary`的新密钥：

    [![](implementing-sirikit-images/plist07w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist07w.png#lightbox)
9. 添加具有 `String`**类型**的 `VocabularyItemIdentifier` 项，并为该术语指定唯一 ID：

    [![](implementing-sirikit-images/plist08w.png "Add the VocabularyItemIdentifier key with the Type of String and specify a unique ID for the term")](implementing-sirikit-images/plist08w.png#lightbox)
10. 添加 `Array`**类型**的 `VocabularyItemSynonyms` 项：

    [![](implementing-sirikit-images/plist09w.png "Add the VocabularyItemSynonyms key with the Type of Array")](implementing-sirikit-images/plist09w.png#lightbox)
11. 添加**类型**为 `Dictionary`的新密钥：

    [![](implementing-sirikit-images/plist10w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist10w.png#lightbox)
12. 添加具有 `String`**类型**的 `VocabularyItemPhrase` 项和应用正在定义的术语：

    [![](implementing-sirikit-images/plist11w.png "Add the VocabularyItemPhrase key with the Type of String and the term the app are defining")](implementing-sirikit-images/plist11w.png#lightbox)
13. 添加 "`VocabularyItemPronunciation`" 项，其**类型**为 "`String`" 和 "拼音"：

    [![](implementing-sirikit-images/plist12w.png "Add the VocabularyItemPronunciation key with the Type of String and the phonetic pronunciation of the term")](implementing-sirikit-images/plist12w.png#lightbox)
14. 添加 `Array`**类型**的 `VocabularyItemExamples` 项：

    [![](implementing-sirikit-images/plist13w.png "Add the VocabularyItemExamples key with the Type of Array")](implementing-sirikit-images/plist13w.png#lightbox)
15. 使用以下术语的示例用法添加几个 `String` 密钥：

    [![](implementing-sirikit-images/plist14w.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist14w.png#lightbox)
16. 针对应用需要定义的任何其他自定义术语，重复以上步骤。
17. 折叠 `ParameterVocabularies` 键。
18. 添加 `Array`**类型**的 `IntentPhrases` 项：

    [![](implementing-sirikit-images/plist15w.png "Add the IntentPhrases key with the Type of Array")](implementing-sirikit-images/plist15w.png#lightbox)
19. 添加**类型**为 `Dictionary`的新密钥：

    [![](implementing-sirikit-images/plist16w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist16w.png#lightbox)
20. 在示例中添加具有 `String` 和意向**类型**的 `IntentName` 项：

    [![](implementing-sirikit-images/plist17w.png "Add the IntentName key with the Type of String and Intent for the example")](implementing-sirikit-images/plist17w.png#lightbox)
21. 添加 `Array`**类型**的 `IntentExamples` 项：

    [![](implementing-sirikit-images/plist18w.png "Add the IntentExamples key with the Type of Array")](implementing-sirikit-images/plist18w.png#lightbox)
22. 使用以下术语的示例用法添加几个 `String` 密钥：

    [![](implementing-sirikit-images/plist19w.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist19w.png#lightbox)
23. 对于应用程序提供的示例使用情况，请重复上述步骤。

-----

> [!IMPORTANT]
> 在开发过程中，将在测试设备上向 Siri 注册 `AppIntentVocabulary.plist`，Siri 合并自定义词汇可能需要一些时间。 因此，测试人员需要等待几分钟，然后再尝试在更新应用程序特定词汇后对其进行测试。

有关详细信息，请参阅[应用特定词汇参考](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[指定自定义词汇引用](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)。

## <a name="adding-an-intents-extension"></a>添加意向扩展

现在，应用已准备好采用 SiriKit，开发人员需要向解决方案中添加一个（或多个）意向扩展，以处理 Siri 集成所需的意图。

对于所需的每个意向扩展，请执行以下操作：

- 向 Xamarin iOS 应用解决方案添加意向扩展项目。
- `Info.plist` 文件中配置意向扩展。
- 修改意向扩展的主类。

有关详细信息，请参阅我们[的意向扩展参考](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[创建意向扩展参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1)。

### <a name="creating-the-extension"></a>创建扩展

若要向解决方案添加意向扩展，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在**Solution Pad**中右键单击**解决方案名称**，然后选择 "**添加** > "**添加新项目 ...** "。
2. 在对话框中，选择 " **iOS** > **扩展** > **意向扩展**"，然后单击 "**下一步**" 按钮： 

    [![](implementing-sirikit-images/intents05.png "Select Intent Extension")](implementing-sirikit-images/intents05.png#lightbox)
3. 接下来，输入目的扩展的**名称**，并单击 "**下一步**" 按钮： 

    [![](implementing-sirikit-images/intents06.png "Enter a Name for the Intent Extension")](implementing-sirikit-images/intents06.png#lightbox)
4. 最后，单击 "**创建**" 按钮，将目的扩展添加到 "应用" 解决方案： 

    [![](implementing-sirikit-images/intents07.png "Add the Intent Extension to the apps solution")](implementing-sirikit-images/intents07.png#lightbox)
5. 在**解决方案资源管理器**中，右键单击新创建的目的扩展的 "**引用**" 文件夹。 检查公共共享代码库项目（在上面创建的应用）的名称，然后单击 **"确定"** 按钮： 

    [![](implementing-sirikit-images/intents08.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在**解决方案资源管理器**中右键单击**解决方案名称**，然后选择 "**添加** > "**添加新项目 ...** "。
2. 在对话框中，选择 **" C# Visual > IOS 扩展 > 意向扩展**"，然后单击 "**下一步**" 按钮：

    [![](implementing-sirikit-images/intents05.w157-sml.png "Select Intent Extension")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. 接下来，输入目的扩展的**名称**，然后单击 **"确定"** 按钮。
4. 在**解决方案资源管理器**中，右键单击新创建的意向扩展的 "**引用**" 文件夹，然后选择 "**添加 > 引用**"。 检查公共共享代码库项目（在上面创建的应用）的名称，然后单击 **"确定"** 按钮：

    [![](implementing-sirikit-images/intents08w.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

对于应用将需要的目的扩展的数量，请重复这些步骤（基于上面[的扩展应用设计扩展](#architecting-the-app-for-extensions)部分）。

### <a name="configuring-the-infoplist"></a>配置信息。 info.plist

对于已添加到应用的解决方案的每个意向扩展，必须在 `Info.plist` 文件中配置才能使用该应用。

与任何典型的应用程序扩展一样，应用程序将具有 `NSExtension` 和 `NSExtensionAttributes`的现有密钥。 对于意向扩展，必须配置两个新属性：

[![](implementing-sirikit-images/intents01.png "The two new attributes that must be configured")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** -是必需的，由应用希望通过意向扩展提供支持的一系列意向类名称组成。
- **IntentsRestrictedWhileLocked** -用于指定扩展的锁定屏幕行为的应用程序的可选键。 它包含一系列意向类名称，应用需要用户登录才能使用目的扩展中的用户。

若要配置目的扩展的 `Info.plist` 文件，请在**解决方案资源管理器**中双击该文件以将其打开进行编辑。 接下来，切换到 "**源**" 视图，然后在编辑器中展开 `NSExtension` 和 `NSExtensionAttributes` 键：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

展开 `IntentsSupported` 项，并添加此扩展插件将支持的任何意向类的名称。 对于示例 MonkeyChat 应用，它支持 `INSendMessageIntent`：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "The INSendMessageIntent key")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "The INSendMessageIntent key")](implementing-sirikit-images/intents09w.png#lightbox)

-----

如果应用可以选择要求用户登录到设备以使用给定的目的，请展开 `IntentRestrictedWhileLocked` 项，并添加具有受限访问权限的类名。 对于示例 MonkeyChat 应用，用户必须登录以发送聊天消息，以便添加 `INSendMessageIntent`：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "The added INSendMessageIntent key")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "The added INSendMessageIntent key")](implementing-sirikit-images/intents10w.png#lightbox)

-----

有关可用意向域的完整列表，请参阅 Apple 的[意向域参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>配置主类

接下来，开发人员需要将充当目的扩展的主入口点的主类配置为 Siri。 它必须是符合 `IINIntentHandler` 委托 `INExtension` 的子类。 例如:

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

应用必须在意向扩展主类 `GetHandler` 方法上实现一个孤立方法。 此方法通过 SiriKit 传递意向，应用必须返回与给定意向的类型匹配的**意向处理程序**。

由于示例 MonkeyChat 应用仅处理一个意向，因此它在 `GetHandler` 方法中返回自身。 如果扩展处理了多个意向，开发人员将为每个意向类型添加一个类，并基于传递到该方法的 `Intent` 在此处返回一个实例。

### <a name="handling-the-resolve-stage"></a>处理解析阶段

解析阶段是指目的扩展将阐明并验证从 Siri 传入的参数，并通过用户的会话进行设置。

对于从 Siri 发送的每个参数，都有一个 `Resolve` 方法。 应用需要为每个参数实现此方法，应用可能需要 Siri 的帮助才能获得用户的正确答案。

在示例 MonkeyChat 应用中，目的扩展需要一个或多个收件人才能向发送消息。 对于列表中的每个收件人，该扩展都需要执行一个联系人搜索，该搜索可能具有以下结果：

- 只找到一个匹配的联系人。
- 找到两个或多个匹配联系人。
- 找不到匹配的联系人。

此外，MonkeyChat 还需要消息正文的内容。 如果用户未提供此内容，则 Siri 需要提示用户输入内容。

目的扩展需要妥善处理每个情况。

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

有关详细信息，请参阅[解析阶段参考](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[解析和处理意向参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1)。

### <a name="handling-the-confirm-stage"></a>处理确认阶段

在确认阶段，目的扩展会进行检查以查看它是否具有满足用户请求的所有信息。 应用程序想要在 Siri 上发送确认，并提供关于要进行的操作的所有支持详细信息，以便可以向用户确认这是预期的操作。

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

有关详细信息，请参阅["确认阶段参考"](~/ios/platform/sirikit/understanding-sirikit.md)。

### <a name="processing-the-intent"></a>处理意向

这是目的扩展实际执行任务以满足用户的请求并将结果传递回 Siri 以便通知用户的点。

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

有关详细信息，请参阅["句柄阶段参考"](~/ios/platform/sirikit/understanding-sirikit.md)。

## <a name="adding-an-intents-ui-extension"></a>添加意向 UI 扩展

可选意向 UI 扩展提供了机会，使应用的 UI 和品牌进入 Siri 体验，并使用户感觉连接到应用。 通过此扩展，应用可以将品牌以及视觉对象和其他信息引入到脚本中。

[![](implementing-sirikit-images/intentsui01.png "An example Intents UI Extension output")](implementing-sirikit-images/intentsui01.png#lightbox)

与意向扩展一样，开发人员可以针对意向 UI 扩展执行以下步骤：

- 向 Xamarin iOS 应用解决方案添加意向 UI 扩展项目。
- `Info.plist` 文件中配置意向 UI 扩展。
- 修改意向 UI 扩展的主类。

有关详细信息，请参阅我们[的意向 UI 扩展参考](~/ios/platform/sirikit/understanding-sirikit.md)和 Apple[提供自定义界面参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1)。

### <a name="creating-the-extension"></a>创建扩展

若要向解决方案添加意向 UI 扩展，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在**Solution Pad**中右键单击**解决方案名称**，然后选择 "**添加** > "**添加新项目 ...** "。
2. 从对话框中选择 " **iOS** > **扩展** > **意向 UI 扩展**"，然后单击 "**下一步**" 按钮： 

    [![](implementing-sirikit-images/intents11.png "Select Intent UI Extension")](implementing-sirikit-images/intents11.png#lightbox)
3. 接下来，输入目的扩展的**名称**，并单击 "**下一步**" 按钮： 

    [![](implementing-sirikit-images/intents12.png "Enter a Name for the Intent Extension")](implementing-sirikit-images/intents12.png#lightbox)
4. 最后，单击 "**创建**" 按钮，将目的扩展添加到 "应用" 解决方案： 

    [![](implementing-sirikit-images/intents13.png "Add the Intent Extension to the apps solution")](implementing-sirikit-images/intents13.png#lightbox)
5. 在**解决方案资源管理器**中，右键单击新创建的目的扩展的 "**引用**" 文件夹。 检查公共共享代码库项目（在上面创建的应用）的名称，然后单击 **"确定"** 按钮： 

    [![](implementing-sirikit-images/intents14.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在**解决方案资源管理器**中右键单击**解决方案名称**，然后选择 "**添加** > "**添加新项目 ...** "
2. 从对话框中选择 " **iOS** > **扩展** > **意向 UI 扩展**"，然后单击 "**下一步**" 按钮。
3. 接下来，输入目的扩展的**名称**，然后单击 **"确定"** 按钮。
4. 在**解决方案资源管理器**中，右键单击新创建的目的扩展的 "**引用**" 文件夹。 检查公共共享代码库项目（在上面创建的应用）的名称，然后单击 **"确定"** 按钮。
    
-----

### <a name="configuring-the-infoplist"></a>配置信息。 info.plist

配置意向 UI 扩展的 `Info.plist` 文件以使用应用。

与任何典型的应用程序扩展一样，应用程序将具有 `NSExtension` 和 `NSExtensionAttributes`的现有密钥。 对于意向扩展，有一个必须配置的新属性：

[![](implementing-sirikit-images/intents03.png "The one new attribute that must be configured")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported**是必需的，由应用希望通过意向扩展支持的一系列意向类名称组成。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要配置意向 UI 扩展的 `Info.plist` 文件，请在**解决方案资源管理器**中双击该文件以将其打开进行编辑。 接下来，切换到 "**源**" 视图，然后在编辑器中展开 `NSExtension` 和 `NSExtensionAttributes` 键：

[![](implementing-sirikit-images/intents04.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要配置意向 UI 扩展的 `Info.plist` 文件，请在**解决方案资源管理器**中双击该文件以将其打开进行编辑。 在编辑器中展开 `NSExtension` 和 `NSExtensionAttributes` 键：

[![](implementing-sirikit-images/intents04w.png "Tthe NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

展开 `IntentsSupported` 项，并添加此扩展插件将支持的任何意向类的名称。 对于示例 MonkeyChat 应用，它支持 `INSendMessageIntent`：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "The INSendMessageIntent key")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "The INSendMessageIntent key")](implementing-sirikit-images/intents15w.png#lightbox)

-----

有关可用意向域的完整列表，请参阅 Apple 的[意向域参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)。

### <a name="configuring-the-main-class"></a>配置主类

将充当意向 UI 扩展的主入口点的主类配置为 Siri。 它必须是符合 `IINUIHostedViewController` 接口 `UIViewController` 的子类。 例如:

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

Siri 会将 `INInteraction` 类实例传递到意向 UI 扩展内 `UIViewController` 实例的 `Configure` 方法。

`INInteraction` 对象向扩展提供三个关键信息：

1. 正在处理的目的对象。
2. 意向扩展的 `Confirm` 和 `Handle` 方法中的意向响应对象。
3. 定义应用和 Siri 之间的交互状态的交互状态。

`UIViewController` 实例是与 Siri 交互的原则类，因为它继承自 `UIViewController`，它可以访问 UIKit 的所有功能。

当 Siri 调用 `UIViewController` 的 `Configure` 方法时，它会在视图上下文中传递，指出视图控制器将托管在 Siri Snippit 或 Map 卡片中。

Siri 还会传入完成处理程序，应用程序在完成配置后，需要返回所需的视图大小。

### <a name="design-the-ui-in-ios-designer"></a>在 iOS 设计器中设计 UI

在 iOS 设计器中布局用户界面扩展的用户界面。 双击**解决方案资源管理器**中的扩展 `MainInterface.storyboard` 文件，将其打开进行编辑。 拖动所有必需的 UI 元素以生成用户界面并保存更改。

> [!IMPORTANT]
> 尽管可以向意向 UI 扩展的 `UIViewController`添加交互式元素（例如 `UIButtons` 或 `UITextFields`），但这些元素被严格禁止为非交互式的意向 UI，用户将无法与它们进行交互。

### <a name="wire-up-the-user-interface"></a>连接用户界面

在 iOS 设计器中创建意向 UI 扩展的用户界面后，编辑 `UIViewController` 子类并重写 `Configure` 方法，如下所示：

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

### <a name="overriding-the-default-siri-ui"></a>重写默认的 Siri UI

意向 UI 扩展将始终与其他 Siri 内容一起显示，如应用程序图标和 UI 顶部的名称，或者，根据目的，按钮（如 "发送" 或 "取消"）可能会显示在底部。

在某些情况下，应用程序可以在默认情况下替换 Siri 显示给用户的信息，例如消息或映射，应用程序可以将默认体验替换为应用程序定制的体验。

如果意向 UI 扩展需要重写默认 Siri UI 的元素，则 `UIViewController` 子类将需要实现 `IINUIHostedViewSiriProviding` 接口，并选择使用来显示特定的接口元素。

将以下代码添加到 `UIViewController` 子类，告诉 Siri 意向 UI 扩展已经显示消息内容：

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>注意事项

Apple 建议在设计和实现意向 UI 扩展时，开发人员应考虑以下注意事项：

- 请**关注内存使用情况**-由于意向 UI 扩展是临时的且仅在短时间内显示，因此，系统会对内存约束产生比完全应用使用的更严格的内存约束。
- **考虑最小和最大视图大小**-确保意向 UI 扩展在每个 iOS 设备类型、大小和方向上都看起来良好。 此外，应用发送回 Siri 的所需大小可能无法授予。
- 再次**使用灵活的自适应布局模式**，以确保 UI 在每个设备上都能正常显示。

## <a name="summary"></a>总结

本文介绍了 SiriKit，并演示了如何将其添加到 Xamarin iOS 应用以提供用户可使用 Siri 和 iOS 设备上的地图应用程序访问的服务。

## <a name="related-links"></a>相关链接

- [ElizaChat 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [SiriKit 编程指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [意向框架参考](https://developer.apple.com/reference/intents)
- [意向 UI 框架引用](https://developer.apple.com/reference/intentsui)
- [意向域参考](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
