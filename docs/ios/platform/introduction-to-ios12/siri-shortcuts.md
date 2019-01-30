---
title: 在 Xamarin.iOS Siri 快捷方式
description: 本文档介绍如何在 iOS 12 中使用 Siri 快捷方式。 它讨论了 NSUserActivities，自定义的目的，将分配语音快捷方式、 相关的快捷方式，和的详细信息。
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 8b3b9a5b110432f33e06edf7ab51c582681e4ea3
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233726"
---
# <a name="siri-shortcuts-in-xamarinios"></a>在 Xamarin.iOS Siri 快捷方式

在中[iOS 10](~/ios/platform/sirikit/index.md)，Apple 引入了 SiriKit，从而可以生成消息传送、 VoIP 呼叫、 付款、 锻炼到，骑预订，和照片 Siri 与之交互的搜索应用。

在中[iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md)，SiriKit 获得更多类型的应用程序的支持和进行 UI 自定义更大的灵活性。

iOS 12 添加 Siri 快捷方式，从而允许所有类型的应用程序以向 Siri 公开其功能。 当某些基于应用的任务最相关的用户，并使用这一知识建议通过潜在操作，了解到使用 Siri_快捷方式_。 点击的快捷方式或使用语音命令调用将打开应用或运行后台任务。

应使用快捷方式，以加速用户的功能来完成常见任务-在许多情况下，而无需打开应用程序有问题。

## <a name="sample-app-soup-chef"></a>应用程序示例：Soup Chef

若要更好地了解 Siri 快捷方式，看一看[Soup Chef](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)示例应用程序。 Soup Chef 允许用户从虚部 soup 餐馆下订单、 查看其订单历史记录和定义通过使用 Siri 与交互排序 soup 时要使用的短语。

> [!TIP]
> 测试之前 Soup Chef iOS 12 模拟器或设备上，启用以下两个设置，当调试快捷方式时非常有用：
>
> - 在中**设置**应用程序中，启用**开发人员 > 显示最新快捷方式**。
> - 在中**设置**应用程序中，启用**开发人员 > 锁定屏幕上显示捐赠**。
>
> 这些调试，可以轻松地查找最近创建的设置进行 （而不是预测） 在 iOS 上的快捷方式锁定屏幕和搜索屏幕。

若要使用示例应用程序：

- 安装和运行 Soup Chef 示例应用 iOS 12 模拟器上或[设备](#testing-on-device)。
- 单击 **+** 右上方以创建新订单中的按钮。 
- 选择一种类型的 soup，指定一个数量和选项，然后点击**下订单**。
- 上**订单历史记录**屏幕上，点击新创建的顺序，以便查看其详细信息。
- 在订单详细信息屏幕的底部，点击**将添加到 Siri**。
- 记录与顺序相关联并点击的语音短语**完成**。
- 最大程度减少 Soup Chef、 调用 Siri，并通过使用你刚才录制语音短语重新放置顺序。
- 使用 Siri 完成订单后，重新打开 Soup Chef，并请注意上, 会列出新订单**订单历史记录**屏幕。

示例应用演示了如何：

- [使用 NSUserActivity 快捷方式打开应用](#using-an-nsuseractivity-shortcut-to-open-an-app)。
- [使用自定义意向快捷方式执行某项任务](#using-a-custom-intent-shortcut-to-perform-a-task)。
- [提供一个用户界面的自定义意向](#providing-a-user-interface-for-a-custom-intent)。
- [创建语音快捷方式](#creating-a-voice-shortcut)。

## <a name="infoplist-and-entitlementsplist"></a>Info.plist 和 Entitlements.plist

之前为 Soup Chef 代码更深入地深入，看一看其**Info.plist**并**Entitlements.plist**文件。

### <a name="infoplist"></a>Info.plist

**Info.plist**中的文件**SoupChef**项目定义**捆绑包标识符**作为`com.xamarin.SoupChef`。 此捆绑包标识符将用于作为前缀的捆绑包标识符的 Intents 和 Intents UI 扩展在本文档后面所述。

**Info.plist**文件还包含如下内容：

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

这`NSUserActivityTypes`Soup Chef，知道如何处理键/值对表示`OrderSoupIntent`，和一个[ `NSUserActivity` ](xref:Foundation.NSUserActivity)拥有[ `ActivityType` ](xref:Foundation.NSUserActivity.ActivityType)的"com.xamarin.SoupChef.viewMenu"。

在处理活动和自定义传递给应用本身，而不是其扩展的目的`AppDelegate`( [ `UIApplicationDelegate` ](xref:UIKit.UIApplicationDelegate)由[ `ContinueUserActivity` ](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*)方法。

### <a name="entitlementsplist"></a>Entitlements.plist

**Entitlements.plist**中的文件**SoupChef**项目包含如下内容：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

此配置指示该应用使用"group.com.xamarin.SoupChef"应用组。 **SoupChefIntents**应用扩展使用此相同的应用程序组，它允许两个项目来共享 [`NSUserDefaults`](xref:Foundation.NSUserDefaults)
数据。

`com.apple.developer.siri`键指示应用程序与使用 Siri 交互。

> [!NOTE]
> **SoupChef**项目的生成配置集**自定义权利**到**Entitlements.plist**。

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>使用 NSUserActivity 快捷方式打开应用

若要创建将打开一个应用来显示特定内容的快捷方式，创建`NSUserActivity`并将其附加到为您希望快捷方式可打开的屏幕的视图控制器。

### <a name="setting-up-an-nsuseractivity"></a>设置 NSUserActivity

在菜单屏幕上，`SoupMenuViewController`创建`NSUserActivity`并将其分配到视图控制器[ `UserActivity` ](xref:UIKit.UIResponder.UserActivity)属性：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

设置`UserActivity`属性_无偿提供_siri 活动。 从本捐献的时隔 Siri 可获取有关此活动时间和位置与用户相关且学习更好地建议将来的信息。

`NSUserActivityHelper` 一个实用工具类，包含在**SoupChef**解决方案，请在**SoupKit**类库。 它会创建`NSUserActivity`和设置与 Siri 和搜索相关的各种属性：

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

尤其注意以下问题：

- 设置`EligibleForPrediction`到`true`指示 Siri 可以预测此活动，它会呈现为一种快捷方式。
- [ `ContentAttributeSet` ](xref:Foundation.NSUserActivity.ContentAttributeSet)数组是一种标准[ `CSSearchableItemAttributeSet` ](https://developer.xamarin.com/api/type/CoreSpotlight.CSSearchableItemAttributeSet/)用于包括`NSUserActivity`iOS 搜索结果中。
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase) 是使用 Siri 向用户作为一个潜在的选择将建议分配给某个快捷方式短语时的短语。

### <a name="handling-an-nsuseractivity-shortcut"></a>处理 NSUserActivity 快捷方式

若要处理`NSUserActivity`快捷方式调用的用户，iOS 应用程序必须重写`ContinueUserActivity`方法`AppDelegate`根据响应的类，`ActivityType`字段的传入的`NSUserActivity`对象：

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

此方法调用`HandleUserActivity`，它查找到菜单屏幕 segue 并对其进行调用：

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>将短语分配给 NSUserActivity

若要将分配到的短语`NSUserActivity`，打开 iOS**设置**应用程序，然后选择**Siri 和搜索 > 自定义快捷方式**。 然后，选择 （在此情况下，"顺序午餐"） 的快捷方式，并记录一个短语。

调用 Siri 并使用此短语将打开到菜单屏幕 Soup Chef。

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>使用自定义意向快捷方式来执行任务

### <a name="defining-a-custom-intent"></a>定义自定义意向

若要提供使用户可以快速完成特定任务与应用相关的快捷方式，创建自定义意向。 自定义意向表示用户可能想要完成，参数与该任务，并且该任务执行后生成的可能响应相关的任务。 具体取决于如何定义自定义意向，调用它可以打开您的应用程序或运行后台任务。

使用 Xcode 10 来创建自定义的目的。 在[SoupChef 存储库](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)，在中定义自定义意向**OrderSoupIntentCodeGen**，OBJECTIVE-C 的项目。 打开此项目并选择**Intents.intentdefinition**中的文件**项目导航器**若要查看**OrderSoup**意向。

注意下列事项：

- 其目的有**类别**的**顺序**。 有各种预定义的类别，可用于自定义的目的;选择最符合你的自定义目标将启用该任务。 由于这是排序应用程序中，soup **OrderSoupIntent**使用**顺序**。
- **确认**复选框指示是否使用 Siri 必须请求执行任务前的显示确认。 有关**顺序 Soup**意向中 Soup Chef，启用此选项是因为用户购买。
- **参数**.intentdefinition 文件的部分定义与一种快捷方式相关的参数。 若要订购 soup，Soup Chef 必须知道 soup、 其数量和任何关联的选项的类型。
每个参数的类型;不能由预定义类型的参数设置为**自定义**。
- **快捷方式类型**接口描述了建议您的快捷方式时，可以使用 Siri 的各种参数组合。 关联**标题**并**副标题**部分允许您定义的消息向用户显示的建议的快捷方式时，将使用 Siri。
- **支持后台执行**而无需打开进一步的用户交互的应用可以执行的任何快捷方式，应选择复选框。

### <a name="defining-custom-intent-responses"></a>定义自定义意向响应

**响应**项下嵌套**OrderSoup**意向表示 soup 订单中产生的潜在响应。

在中**OrderSoup**意向的响应定义的信息，请注意以下事项：

- 响应**属性**可用于自定义消息发送回用户。 **OrderSoup**意向响应具有**soup**并**waitTime**属性。
- **响应模板**指定各种可用于指示状态意向的任务完成后的成功和失败消息。
- **成功**指示成功的响应，应选择复选框。
 - **OrderSoupIntent**成功响应使用**soup**并**waitTime**属性来提供友好且有用的消息描述当 soup 顺序将为准备就绪。

### <a name="generating-code-for-the-custom-intent"></a>生成自定义意向的代码

生成包含此自定义意向定义的 Xcode 项目会导致 Xcode 生成可用于以编程方式与自定义意向和触发器及其响应进行交互的代码。

若要查看此生成的代码：

- 打开**AppDelegate.m**。
- 将导入添加到自定义意向的标头文件： `#import "OrderSoupIntent.h"`
- 在类中的任何方法，添加对引用`OrderSoupIntent`。
- 右键单击`OrderSoupIntent`，然后选择**跳转到定义**。
- 在新打开文件中，右键单击**OrderSoupIntent.h**，然后选择**在查找器中显示**。
- 这将打开**Finder**窗口，其中包含包含生成的代码的.h 和.m 文件。

此生成的代码包括：

- `OrderSoupIntent` – 一个表示自定义意向的类。
- `OrderSoupIntentHandling` – 一种协议，用于定义将用于确认意图应执行的方法和实际执行的方法。
- `OrderSoupIntentResponseCode` – 一个枚举值，定义了各种响应状态。
- `OrderSoupIntentResponse` – 一个类，表示对意向的执行的响应。

### <a name="creating-a-binding-to-the-custom-intent"></a>创建绑定到自定义意向

若要使用 Xcode 生成 Xamarin.iOS 应用程序中的代码，创建C#为其绑定。

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>创建静态库和C#绑定定义

在中[SoupChef 存储库](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)，看一看**OrderSoupIntentStaticLib**文件夹，并打开**OrderSoupIntentStaticLib.xcodeproj** Xcode 项目。

这**Cocoa Touch 静态库**项目包含**OrderSoupIntent.h**并**OrderSoupIntent.m**由 Xcode 生成的文件。

#### <a name="configuring-the-static-library-project-build-settings"></a>配置静态库项目生成设置

在 Xcode 中**项目导航器**，选择顶级项目， **OrderSoupIntentStaticLib**，并导航到**生成阶段 > 编译源**。
请注意， **OrderSoupIntent.m** (它导入**OrderSoupIntent.h**) 会在此处列出。 在**二进制与库链接**，注意**Intents.framework**并**Foundation.framework**包括在内。
在准备好这些设置，框架将正确生成。

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>生成静态库和生成C#绑定定义

若要生成静态库并生成C#绑定定义该信息，请执行以下步骤：

- [安装目标 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie)，用来从由 Xcode 创建的.h 和.m 文件生成绑定定义的工具。

- 在系统配置为使用 Xcode 10 命令行工具：

    > [!WARNING]
    > 正在更新所选的命令行工具将会影响所有已安装在系统上的 Xcode 版本。 完成后使用 Soup Chef 示例应用程序，应确保还原此设置为其原始配置。

    - 在 Xcode 中，选择**Xcode > 首选项 > 位置**并设置**命令行工具**到最新的 Xcode 10 安装系统上可用。

- 在终端中，`cd`到**OrderSoupIntentStaticLib**目录。

- 类型`make`，哪些生成：

    - 静态库， **libOrderSoupIntentStaticLib.a**
    - 在中**bo**输出目录中，C#绑定定义：
        - **ApiDefinitions.cs**
        - **StructsAndEnums.cs**

**OrderSoupIntentBindings**项目，它依赖于此静态库和其关联的绑定定义，会自动生成这些项。
但是，手动运行完成上述过程将确保按预期方式生成。

有关如何创建静态库和使用目标 Sharpie 若要创建的详细信息C#绑定定义，看一看[绑定 iOS OBJECTIVE-C 库](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library)演练。

#### <a name="creating-a-bindings-library"></a>创建绑定库

使用静态库和C#绑定定义创建，其余部分使用 Xcode 生成所需在 Xamarin.iOS 项目中与意图相关的代码是一个绑定库。

在中[Soup Chef 存储库](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)，请打开**SoupChef.sln**文件。 此外，此解决方案包含**OrderSoupIntentBinding**，上述生成静态库的绑定库。

请注意，具体而言，此项目包括：

- **ApiDefinitions.cs** – 文件由目标 Sharpie 上面生成并添加到此项目。 此文件的**生成操作**设置为**ObjcBindingApiDefinition**。
- **StructsAndEnums.cs** – 另一个文件由目标 Sharpie 上面生成并添加到此项目。 此文件的**生成操作**设置为**ObjcBindingCoreSource**。
- 一个**本机引用**到**libOrderSoupIntentStaticLib.a**，上面生成的静态库。

> [!NOTE]
> 这两**ApiDefinitions.cs**并**StructsAndEnums.cs**包含属性，如`[Watch (5,0), iOS (12,0)]`。 这些属性，生成的目标 Sharpie 已被注释掉，因为它们不需要为此项目。

有关创建的详细信息C#绑定库，请查阅[： 绑定 iOS OBJECTIVE-C 库](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project)演练。

请注意， **SoupChef**项目包含对引用**OrderSoupIntentBinding**，这意味着，它现在可以访问，在C#，类、 接口和枚举，它包含：

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>将.intentdefinition 文件添加到你的解决方案

在C# **SoupChef**解决方案中， **SoupKit**项目包含应用程序和其扩展之间共享的代码。 **Intents.intentdefinition**文件置于**Base.lproj**目录**SoupKit**，并且它具有**生成操作**的**内容**。 生成过程将此文件复制到 Soup Chef 应用程序包中，这是必需的应用程序才能正常工作的。

### <a name="donating-an-intent"></a>捐赠意向

为了使 Siri 建议一种快捷方式，它必须首先了解当的快捷方式是相关。

若要使此理解，Soup Chef Siri_无偿提供_意向 siri 每次用户将放入 soup 顺序。 基于此捐赠 – 时它已捐赠，其中已捐赠，它包含的参数 – Siri 了解何时在将来建议快捷方式。

**SoupChef**使用`SoupOrderDataManager`类将捐赠。
若要订购 soup 用户，在调用时`PlaceOrder`方法又调用[ `DonateInteraction` ](https://developer.xamarin.com/api/member/Intents.INInteraction.DonateInteraction/):

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

正在提取意向之后, 它将包装在[ `INInteraction` ](https://developer.xamarin.com/api/type/Intents.INInteraction/)。
`INInteraction`给定 [`Identifier`](https://developer.xamarin.com/api/property/Intents.INInteraction.Identifier/)
（这会有助于稍后删除不再有效的意向捐赠时） 的顺序的唯一 ID 与相匹配。 然后，交互将捐赠给使用 Siri。

在调用`order.Intent`getter 提取`OrderSoupIntent`，通过设置表示顺序其`Quantity`， `Soup`， `Options`，和图像，并调用短语作为一项建议，用户记录 Siri 将相关联的短语时其目的：

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>删除无效的捐赠

请务必删除不再有效，以便使用 Siri 不会使无用或令人困惑的快捷方式的建议的捐赠。

在 Soup Chef**配置菜单**屏幕可用于将标记一个菜单项不可用。 Siri 应不再建议的快捷方式进行排序的不可用的菜单项，因此`RemoveDonation`方法的`SoupMenuManager`删除捐赠的菜单项将不再可用。 之所以如此是因为：

- 查找与现在不可用的菜单项关联的订单。
- 获取其标识符。
- 正在删除具有该相同标识符的交互。

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>创建的 Intents 扩展

运行时使用 Siri 调用意向的代码置于的 Intents 扩展，可以为新项目添加到现有的 Xamarin.iOS 应用程序如 Soup Chef 与相同的解决方案。 在中**SoupChef**解决方案，该扩展被称为**SoupChefIntents**。

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – Info.plist 和 Entitlements.plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – Info.plist

**Info.plist**中**SoupChefIntents**项目定义**捆绑包标识符**作为`com.xamarin.SoupChef.SoupChefIntents`。

**Info.plist**文件还包含如下内容：

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

在上面**Info.plist**:

- `IntentsRestrictedWhileLocked` 列出在设备处于解锁状态时应仅处理的方式。
- `IntentsSupported` 列出此扩展插件处理的方式。
- `NSExtensionPointIdentifier` 指定应用扩展的类型 (请参阅[Apple 的文档](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)有关详细信息)。
- `NSExtensionPrincipalClass` 指定应该用于处理此扩展插件支持的意图的类。

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – Entitlements.plist

**Entitlements.plist**中**SoupChefIntents**项目具有**应用组**功能。 此功能配置为使用与相同的应用组**SoupChef**项目：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Soup Chef 仍然存在数据与`NSUserDefaults`。 为了在共享应用程序和应用扩展之间的数据，它们引用同一个应用程序组中的其**Entitlements.plist**文件。

> [!NOTE]
> **SoupChefIntents**项目的生成配置集**自定义权利**到**Entitlements.plist**。

#### <a name="handling-an-ordersoupintent-background-task"></a>处理 OrderSoupIntent 后台任务

Intents 扩展执行基于自定义意向的快捷方式的必要的后台任务。

Siri 调用[ `GetHandler` ](https://developer.xamarin.com/api/member/Intents.INExtension.GetHandler/)方法`IntentHandler`类 (中定义**Info.plist**作为`NSExtensionPrincipalClass`) 来获取扩展的类的实例`OrderSoupIntentHandling`，可以使用若要处理`OrderSoupIntent`:

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`在中定义**SoupKit**项目共享代码，实现两个重要的方法：

- `ConfirmOrderSoup` – 确认应实际执行与意图相关的任务
- `HandleOrderSoup` – 使 soup 顺序，并向用户提供响应通过调用传入的完成处理程序

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>处理打开应用 OrderSoupIntent

应用程序必须正确处理不会在后台运行的意图。
这些参数进行处理的方式相同`NSUserActivity`快捷方式，请在`ContinueUserActivity`方法的`AppDelegate`:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>为自定义意向提供用户界面

Intents UI 扩展提供自定义用户界面的 Intents 扩展。 在中**SoupChef**解决方案， **SoupChefIntentsUI**是为提供的接口的 Intents UI 扩展**SoupChefIntents**。

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – Info.plist 和 Entitlements.plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – Info.plist

**Info.plist**中**SoupChefIntentsUI**项目定义**捆绑包标识符**作为`com.xamarin.SoupChef.SoupChefIntentsui`。

**Info.plist**文件还包含如下内容：

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

在上面**Info.plist**:

- `IntentsSupported` 指示`OrderSoupIntent`处理此 Intents UI 扩展插件。
- `NSExtensionPointIdentifier` 指定应用扩展的类型 (请参阅[Apple 的文档](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)有关详细信息)。
- `NSExtensionMainStoryboard` 指定定义此扩展的主接口的情节提要

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – Entitlements.plist

**SoupChefIntentsUI**项目不需要**Entitlements.plist**文件。

### <a name="creating-the-user-interface"></a>创建用户界面

由于**Info.plist**有关**SoupChefIntentsUI**设置`NSExtensionMainStoryboard`密钥`MainInterface`，则**MainInterace.storyboard**文件定义的接口Intents UI 扩展中。

在此情节提要，没有类型的单个视图控制器**IntentViewController**。 它引用了两个视图：

- **invoiceView**的类型 `InvoiceView`
- **confirmationView**的类型 `ConfirmOrderView`

> [!NOTE]
> 有关接口**invoiceView**并**confirmationView**中定义**Main.storyboard**作为辅助视图。 IOS 设计器在 Visual Studio for Mac 和 Visual Studio 2017 中不提供用于查看或编辑辅助视图; 支持若要执行此操作，打开**Main.storyboard** Xcode 的 Interface Builder 中。

`IntentViewController` 实现 [`IINUIHostedViewControlling`](https://developer.xamarin.com/api/type/IntentsUI.IINUIHostedViewControlling/)
接口，用于提供自定义接口时使用 Siri 意向。 的 [`ConfigureView`](https://developer.xamarin.com/api/member/IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView/)
调用方法以自定义界面，显示确认消息或发票，具体取决于是否正在确认交互 ([`INIntentHandlingStatus.Ready`](https://developer.xamarin.com/api/type/Intents.INIntentHandlingStatus/)) 或已成功执行了 ([ `INIntentHandlingStatus.Success`](https://developer.xamarin.com/api/type/Intents.INIntentHandlingStatus/)):

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> 有关详细信息`ConfigureView`方法中，Apple 的 WWDC 2017 演播[What's New in SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/)。

## <a name="creating-a-voice-shortcut"></a>创建语音快捷方式

Soup Chef 提供了一个接口来将语音快捷方式分配给每个订单，因而可能会使用 Siri 顺序 soup。 实际上，用来记录并指定语音的快捷方式的接口提供的 iOS 和需要很少的自定义代码。

在中`OrderDetailViewController`，当用户点击表的**将添加到 Siri**行， [ `RowSelected` ](xref:UIKit.UITableViewSource.RowSelected*)方法显示一个屏幕来添加或编辑的语音快捷方式：

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

基于现有语音快捷方式是否存在当前显示顺序`RowSelected`提供类型的视图控制器[ `INUIEditVoiceShortcutViewController` ](https://developer.xamarin.com/api/type/IntentsUI.INUIEditVoiceShortcutViewController/)或[ `INUIAddVoiceShortcutViewController` ](https://developer.xamarin.com/api/type/IntentsUI.INUIAddVoiceShortcutViewController/)。
每种情况下，`OrderDetailViewController`将自身设置为视图控制器的`Delegate`，正因如此，它还实现 [`IINUIAddVoiceShortcutViewControllerDelegate`](https://developer.xamarin.com/api/type/IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate/)
并[ `IINUIEditVoiceShortcutViewControllerDelegate` ](https://developer.xamarin.com/api/type/IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate/)。

## <a name="testing-on-device"></a>在设备上进行测试

若要在设备上运行 Soup Chef，请执行下面的说明。 请参阅有关[有关自动预配说明](#automatic-provisioning)。

### <a name="app-group-app-ids-provisioning-profiles"></a>应用组，应用 Id，预配配置文件

在中**证书、 Id 和配置文件**一部分[Apple 开发人员门户](https://developer.apple.com/)，执行以下操作：

- 创建应用程序组，以便 Soup Chef 应用和其扩展之间共享数据。 例如： **group.com.yourcompanyname.SoupChef**

- 创建三个应用程序 Id： 一个用于应用程序本身，一个用于的是 Intents 扩展，，一个用于 Intents UI 扩展。 例如：

    - 应用程序： **com.yourcompanyname.SoupChef**
        - 向此应用程序 ID，将分配 SiriKit 并**应用组**功能。

    - Intents 扩展： **com.yourcompanyname.SoupChef.Intents**
        - 向此应用程序 ID，将分配**应用组**功能。

    - Intents UI 扩展： **com.yourcompanyname.SoupChef.Intentsui**
        - 此应用程序 ID 需要任何特殊功能。

- 在创建更高版本的应用 Id 后, 编辑**应用组**上面创建分配给应用程序和 Intents 扩展并指定特定的应用程序组的功能。

- 创建三个新开发预配配置文件，分别为每个新的应用 Id。

- 下载这些预配配置文件，并双击每个证书即可安装。 如果已在运行 Visual Studio for Mac 或 Visual Studio 2017，重新启动，以确保它在注册新的预配配置文件。

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>编辑 Info.plist、 Entitlements.plist 和源代码

在 Visual Studio for Mac 或 Visual Studio 2017 中，执行以下步骤：

- 更新各种**Info.plist**解决方案中的文件。 设置应用程序、 Intents 扩展和 Intents UI 扩展**捆绑包标识符**到上面定义的应用 Id:

    - 应用程序： **com.yourcompanyname.SoupChef**
    - Intents 扩展： **com.yourcompanyname.SoupChef.Intents**
    - Intents UI 扩展： **com.yourcompanyname.SoupChef.Intentsui**

- 更新**Entitlements.plist**适用于文件**SoupChef**项目：
    - 有关**应用组**功能，将组设置为上面创建的新应用程序组 (在上面的示例中，它已**group.com.yourcompanyname.SoupChef**)。
    - 请确保**SiriKit**已启用。

- 更新**Entitlements.plist**适用于文件**SoupChefIntents**项目：
    - 有关**应用组**功能，将组设置为上面创建的新应用程序组 (在上面的示例中，它已**group.com.yourcompanyname.SoupChef**)。

- 最后，打开**NSUserDefaultsHelper.cs**。 设置`AppGroup`变量为新的应用程序组的值 (例如，将其设置为`group.com.yourcompanyname.SoupChef`)。

### <a name="configuring-the-build-settings"></a>配置生成设置

在 Visual Studio for Mac 或 Visual Studio 2017:

- 打开的选项/属性**SoupChef**项目。 上**iOS 捆绑签名**选项卡上，设置**签名标识**为自动和**预配配置文件**为特定于应用的预配配置文件创建。

- 打开的选项/属性**SoupChefIntents**项目。 上**iOS 捆绑签名**选项卡上，设置**签名标识**为自动和**预配配置文件**到新的 Intents 扩展特定预配配置文件创建上面。

- 打开的选项/属性**SoupChefIntentsUI**项目。 上**iOS 捆绑签名**选项卡上，设置**签名标识**为自动和**预配配置文件**到新的 Intents UI 扩展插件特定预配配置文件，上面的创建。

与这些更改后，应用将在 iOS 设备上运行。

### <a name="automatic-provisioning"></a>自动预配

请注意，可以使用[自动预配](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning)用于完成许多这些设置直接在 IDE 中的任务。
但是，自动预配不设置应用程序组。 将需要手动配置**Entitlements.plist**具有你想要使用，应用程序组的名称的文件，请访问 Apple 开发人员门户以创建应用程序组，将该应用程序组分配给由自动创建每个应用程序 ID预配，重新生成预配配置文件 （应用、 Intents 扩展、 Intents UI 扩展） 以包括新创建的应用组，然后下载并安装它们。

## <a name="related-links"></a>相关链接

- [Soup Chef (Xamarin)](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)
- [Soup Chef (Swift)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [使用 Siri 快捷方式 – WWDC 2018 简介](https://developer.apple.com/videos/play/wwdc2018/211/)
- [构建使用 Siri 快捷方式 – WWDC 2018 的声音](https://developer.apple.com/videos/play/wwdc2018/214/)
- [使用 Siri 手表表盘 – WWDC 2018 Siri 快捷方式](https://developer.apple.com/videos/play/wwdc2018/217/)
- [什么是 SiriKit – WWDC 2017 中的新增功能](https://developer.apple.com/videos/play/wwdc2017/214/)
- [创建意向应用程序扩展 (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
