---
title: Xamarin 中的 Siri 快捷方式
description: 本文档介绍如何在 iOS 12 中使用 Siri 快捷方式。 它讨论了 NSUserActivities、自定义意向、指定声音快捷方式以及相关的快捷方式。
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/08/2018
ms.openlocfilehash: 40b7adbed3489d449e583b22fa477287d11bdf42
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031872"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Xamarin 中的 Siri 快捷方式

在[iOS 10](~/ios/platform/sirikit/index.md)中，Apple 引入了 SiriKit，使你能够构建与 Siri 交互的消息传递、VoIP 呼叫、付款、workouts、行程预订和照片搜索应用。

在[iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md)中，SiriKit 获得了更多类型的应用的支持，并更灵活地进行 UI 自定义。

iOS 12 添加了 Siri 快捷方式，允许所有类型的应用向 Siri 公开其功能。 Siri 了解某些基于应用的任务与用户最相关的情况，并使用此知识通过_快捷方式_建议可能的操作。 点击快捷方式或使用语音命令调用它将打开应用或运行后台任务。

快捷方式应用于加快用户完成常见任务的能力，在许多情况下，甚至无需打开相关应用。

## <a name="sample-app-soup-chef"></a>示例应用： Soup Chef

为了更好地了解 Siri 快捷方式，请参阅[Soup Chef](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef)示例应用。 Soup Chef 允许用户从虚部 Soup 餐馆下订单，查看其订单历史记录，并定义在通过与 Siri 交互进行排序时使用的短语。

> [!TIP]
> 在 iOS 12 模拟器或设备上测试 Soup Chef 之前，请启用以下两个设置，这些设置在调试快捷方式时非常有用：
>
> - 在 "**设置**" 应用中，启用 "**开发人员 > 显示最新的快捷方式**。
> - 在 "**设置**" 应用中，启用 "**开发人员 > 在锁定屏幕上显示捐赠**。
>
> 利用这些调试设置，可以轻松地在 iOS 锁屏界面和搜索屏幕上查找最近创建的（而不是预测的）快捷方式。

使用示例应用：

- 在 iOS 12 模拟器或[设备](#testing-on-device)上安装并运行 Soup Chef 示例应用。
- 单击右上角的 " **+** " 按钮以创建新订单。
- 选择一种类型的 soup，指定数量和选项，然后点击 "**下订单**"。
- 在 "**订单历史记录**" 屏幕上，点击新创建的订单以查看其详细信息。
- 在 "订单详细信息" 屏幕的底部，点击 "**添加到 Siri**"。
- 录制一个语音短语，使其与订单相关联，然后点击 "**完成**"。
- 最小化 Soup Chef，调用 Siri，并使用刚刚记录的语音短语再次放置顺序。
- Siri 完成订单后，重新打开 Soup Chef，并注意到 "**订单历史记录**" 屏幕上列出了新订单。

示例应用演示了如何：

- [使用 NSUserActivity 快捷方式打开应用](#using-an-nsuseractivity-shortcut-to-open-an-app)。
- [使用自定义意向快捷方式来执行任务](#using-a-custom-intent-shortcut-to-perform-a-task)。
- [为自定义意向提供用户界面](#providing-a-user-interface-for-a-custom-intent)。
- [创建语音快捷方式](#creating-a-voice-shortcut)。

## <a name="infoplist-and-entitlementsplist"></a>Info.plist 和 info.plist

在深入 Soup Chef 代码之前，请先查看其**信息 info.plist** **和文件。**

### <a name="infoplist"></a>Info.plist

**SoupChef**项目中的**Info.plist**文件将**绑定标识符**定义为 `com.xamarin.SoupChef`。 此捆绑标识符将用作本文档后面部分介绍的意向和意向 UI 扩展的捆绑标识符的前缀。

**Info.plist**文件还包含以下内容：

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

此 `NSUserActivityTypes` 键/值对表示 Soup Chef 知道如何处理 `OrderSoupIntent`，以及[`NSUserActivity`](xref:Foundation.NSUserActivity) [`ActivityType`](xref:Foundation.NSUserActivity.ActivityType)为 "SoupChef. viewMenu"。

传递给应用本身的活动和自定义意向（相对于其扩展）将在 `AppDelegate` （ [`ContinueUserActivity`](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*)方法的[`UIApplicationDelegate`](xref:UIKit.UIApplicationDelegate)中进行处理。

### <a name="entitlementsplist"></a>Entitlements.plist

**SoupChef**项目中的**info.plist**文件包含以下内容：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

此配置指示该应用使用 "SoupChef" 应用组。 **SoupChefIntents**应用扩展使用此相同的应用组，这允许两个项目共享[`NSUserDefaults`](xref:Foundation.NSUserDefaults)
数据。

`com.apple.developer.siri` 键指示应用与 Siri 交互。

> [!NOTE]
> **SoupChef**项目的生成配置将**自定义权利**设置为**info.plist**。

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>使用 NSUserActivity 快捷方式打开应用

若要创建用于打开应用以显示特定内容的快捷方式，请创建 `NSUserActivity`，并将其附加到想要打开快捷方式的屏幕的视图控制器。

### <a name="setting-up-an-nsuseractivity"></a>设置 NSUserActivity

在菜单屏幕上，`SoupMenuViewController` 创建一个 `NSUserActivity` 并将其分配给视图控制器的[`UserActivity`](xref:UIKit.UIResponder.UserActivity)属性：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

`UserActivity` 将活动_donates_属性设置为 Siri。 从这一捐赠中，Siri 可以获得有关此活动与用户的关系以及在将来更好地提出建议的时间和位置的信息。

`NSUserActivityHelper` 是**SoupKit**类库中**SoupChef**解决方案中包含的实用工具类。 它创建 `NSUserActivity`，并设置与 Siri 和搜索相关的各种属性：

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

请注意以下事项：

- 将 `EligibleForPrediction` 设置为 `true` 指示 Siri 可以预测此活动，并将其呈现为快捷方式。
- [`ContentAttributeSet`](xref:Foundation.NSUserActivity.ContentAttributeSet)数组是一种标准[`CSSearchableItemAttributeSet`](xref:CoreSpotlight.CSSearchableItemAttributeSet) ，用于在 iOS 搜索结果中包含 `NSUserActivity`。
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase)是一种短语，在将短语分配给快捷方式时，Siri 会向用户提供一个可能的选择。

### <a name="handling-an-nsuseractivity-shortcut"></a>处理 NSUserActivity 快捷方式

若要处理用户调用的 `NSUserActivity` 快捷方式，iOS 应用程序必须重写 `AppDelegate` 类的 `ContinueUserActivity` 方法，并根据传入 `NSUserActivity` 对象的 `ActivityType` 字段进行响应：

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

此方法调用 `HandleUserActivity`，它将查找 segue 到菜单屏幕并调用它：

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

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>向 NSUserActivity 分配短语

若要将短语分配到 `NSUserActivity`，请打开 "iOS**设置**" 应用，然后选择 **"Siri & 搜索" > 我的快捷方式 "** 。 然后，选择快捷方式（在本例中为 "订单午餐"）并录制一个短语。

调用 Siri 并使用此短语会将 Soup Chef 打开到菜单屏幕。

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>使用自定义意向快捷方式执行任务

### <a name="defining-a-custom-intent"></a>定义自定义意向

若要提供一个快捷方式，使用户能够快速完成与应用相关的特定任务，请创建自定义意向。 自定义意向表示用户可能需要完成的任务、与该任务相关的参数，以及由任务执行导致的潜在响应。 根据自定义意向的定义方式，调用它可以打开应用或运行后台任务。

使用 Xcode 10 创建自定义意向。 在[SoupChef 存储库](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，自定义意向在**OrderSoupIntentCodeGen**（目标 C 项目）中定义。 打开此项目，然后在 "**项目导航器**" 中选择**intentdefinition**文件以查看**OrderSoup**目的。

注意下列事项：

- 意向的**类别** **为。** 有各种预定义的类别可用于自定义意向;选择与自定义意向将启用的任务最匹配的项。 由于这是一个 soup 的订购应用程序， **OrderSoupIntent**使用**订单**。
- **确认**复选框指示 Siri 在执行任务之前是否必须请求确认。 对于 Soup Chef 中的**Order Soup**意向，此选项是在用户进行购买后启用的。
- Intentdefinition 文件的**参数**部分定义了与快捷方式相关的参数。 若要设置 soup，Soup Chef 必须知道 soup 的类型、其数量以及任何关联的选项。
每个参数都具有类型;预定义类型无法表示的参数将设置为**Custom**。
- **快捷方式类型**接口描述了在建议快捷方式时可以使用的各种参数组合 Siri。 使用关联的 "**标题**" 和 "**副标题**" 部分可以定义在向用户提供建议的快捷方式时 Siri 将使用的消息。
- 对于可以在不打开应用程序以进一步进行用户交互的情况下执行的任何快捷方式，都应选择 "**支持后台执行**" 复选框。

### <a name="defining-custom-intent-responses"></a>定义自定义意向响应

嵌套在**OrderSoup**意向下面的**响应**项表示由 soup 顺序产生的潜在响应。

在**OrderSoup**意向的响应定义中，注意以下事项：

- 响应的**属性**可用于自定义传回给用户的消息。 **OrderSoup**意向响应具有**soup**和**waitTime**属性。
- **响应模板**指定可用于在完成目的任务后指示状态的各种成功和失败消息。
- 对于表明成功的响应，应选择 "**成功**" 复选框。
- **OrderSoupIntent**成功响应使用**soup**和**waitTime**属性来提供友好且有用的消息，用于描述 soup 订单何时准备就绪。

### <a name="generating-code-for-the-custom-intent"></a>为自定义意向生成代码

生成包含此自定义意向定义的 Xcode 项目将导致 Xcode 生成代码，该代码可用于以编程方式与自定义意向及其响应交互。

查看此生成的代码：

- 打开**AppDelegate**。
- 将导入添加到自定义意向的标头文件中： `#import "OrderSoupIntent.h"`
- 在类的任何方法中，将对的引用添加到 `OrderSoupIntent`。
- 右键单击 `OrderSoupIntent`，然后选择 "**跳转到定义**"。
- 右键单击新打开的文件**OrderSoupIntent**，然后选择 "**在查找器中显示**"。
- 这会打开一个**查找**器窗口，其中包含生成的代码的 .h 文件和. m 文件。

此生成的代码包括：

- `OrderSoupIntent` –表示自定义意向的类。
- `OrderSoupIntentHandling` –一种协议，用于定义将用于确认是否应执行意向以及实际执行此目的的方法。
- `OrderSoupIntentResponseCode` –定义各种响应状态的枚举。
- `OrderSoupIntentResponse` –表示对意向执行的响应的类。

### <a name="creating-a-binding-to-the-custom-intent"></a>创建自定义意向的绑定

若要使用 Xcode 在 Xamarin iOS 应用程序中生成的代码，请为C#其创建绑定。

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>创建静态库和C#绑定定义

在[SoupChef 存储库](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，查看**OrderSoupIntentStaticLib**文件夹，并打开**OrderSoupIntentStaticLib .xcodeproj** Xcode 项目。

此**Cocoa 触控静态库**项目包含 Xcode 生成的**OrderSoupIntent**和**OrderSoupIntent**文件。

#### <a name="configuring-the-static-library-project-build-settings"></a>配置静态库项目生成设置

在 Xcode**项目导航器**中，选择顶级项目 " **OrderSoupIntentStaticLib**"，并导航到 "**生成阶段 > 编译源**"。
请注意，此处列出了**OrderSoupIntent** （导入**OrderSoupIntent**）。 在 "将**二进制文件链接到库**" 中，请注意，包括了**意向. framework**和**Foundation。**
设置好这些设置后，框架将会正确生成。

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>生成静态库和生成C#绑定定义

若要生成静态库并为C#其生成绑定定义，请执行以下步骤：

- [安装客观 Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie)，该工具用于通过 Xcode 创建的 .h 文件和. m 文件生成绑定定义。

- 将系统配置为使用 Xcode 10 命令行工具：

  > [!WARNING]
  > 更新选定的命令行工具将影响系统上安装的所有 Xcode 版本。 使用 Soup Chef 示例应用完成后，请务必将此设置恢复为原始配置。

  - 在 Xcode 中，选择 " **Xcode > 首选项" > 位置**，并将**命令行工具**设置为系统上可用的最新 Xcode 10 安装。

- 在终端中，`cd` 到**OrderSoupIntentStaticLib**目录。

- 类型 `make`，它将生成：

  - 静态库**libOrderSoupIntentStaticLib**
  - 在**bo**输出目录中， C#绑定定义：
    - **ApiDefinitions.cs**
    - **StructsAndEnums.cs**

依赖于此静态库及其关联的绑定定义的**OrderSoupIntentBindings**项目会自动生成这些项。
但是，通过上述过程手动运行将确保它按预期方式生成。

若要详细了解如何创建静态库并使用客观 Sharpie 创建C#绑定定义，请查看如何将[iOS 目标 C 库绑定到绑定](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library)。

#### <a name="creating-a-bindings-library"></a>创建绑定库

创建静态库和C#绑定定义后，在 Xamarin iOS 项目中使用 Xcode 生成的与意向相关的代码所需的剩余部分就是绑定库。

在[Soup Chef 存储库](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef)中，打开**SoupChef**文件。 除此之外，此解决方案包含**OrderSoupIntentBinding**，这是上面生成的静态库的绑定库。

特别要注意的是，此项目包括：

- **ApiDefinitions.cs** –由客观 Sharpie 生成并添加到此项目中的文件。 此文件的**生成操作**设置为**ObjcBindingApiDefinition**。
- **StructsAndEnums.cs** –客观 Sharpie 上面生成的另一个文件，并将其添加到此项目中。 此文件的**生成操作**设置为**ObjcBindingCoreSource**。
- 对**libOrderSoupIntentStaticLib**的**本机引用**，这是上面生成的静态库。

> [!NOTE]
> **ApiDefinitions.cs**和**StructsAndEnums.cs**都包含 `[Watch (5,0), iOS (12,0)]`之类的属性。 由于目标 Sharpie 生成的这些属性不是此项目所必需的，因此已将其注释掉。

有关创建C#绑定库的详细信息，请参阅关于[IOS 目标-C 库的绑定](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project)演练。

请注意， **SoupChef**项目包含对**OrderSoupIntentBinding**的引用，这意味着它现在可以访问C#它所包含的类、接口和枚举：

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>将 intentdefinition 文件添加到解决方案

C#在**SoupChef**解决方案中， **SoupKit**项目包含应用及其扩展之间共享的代码。 **Intentdefinition**文件已放置在**SoupKit**的**lproj**目录中，它具有**Content**的**生成操作**。 生成过程将此文件复制到 Soup Chef 应用程序捆绑包中，以便应用程序正常运行。

### <a name="donating-an-intent"></a>捐赠意向

为了使 Siri 建议一个快捷方式，必须首先了解快捷方式的相关时间。

若要为 Siri 这一理解，Soup Chef _donates_每次用户提出 Siri 订单时要 Soup。 基于这一捐赠–在捐赠时，其所包含的参数为 "Siri"，它会学习何时建议在将来提供快捷方式。

**SoupChef**使用 `SoupOrderDataManager` 类来放置捐赠。
当调用为用户提供 soup 顺序时，`PlaceOrder` 方法依次调用[`DonateInteraction`](xref:Intents.INInteraction.DonateInteraction*)：

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

提取意向后，会将其包装在[`INInteraction`](xref:Intents.INInteraction)中。
为 `INInteraction` 提供了一个[`Identifier`](xref:Intents.INInteraction.Identifier*)
与顺序的唯一 ID 匹配（这在删除不再有效的意向捐赠后会有所帮助）。 然后，对 Siri 进行交互。

对 `order.Intent` getter 的调用通过设置其 `Quantity`、`Soup`、`Options`和 image 来获取表示顺序的 `OrderSoupIntent`，在用户记录 Siri 的短语以与意向关联时，将使用调用短语作为建议:

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

#### <a name="removing-invalid-donations"></a>删除无效捐赠

务必要删除不再有效的捐赠，使 Siri 不会导致无用或混乱的快捷方式建议。

在 Soup Chef 中，可以使用 "**配置" 菜单**屏幕将菜单项标记为 "不可用"。 Siri 不应再建议使用排序快捷菜单项的快捷方式，因此 `SoupMenuManager` 的 `RemoveDonation` 方法将删除不再可用的菜单项的捐赠。 它通过以下方法实现此操作：

- 查找与现在不可用菜单项关联的订单。
- 抓取其标识符。
- 删除具有相同标识符的交互。

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

### <a name="creating-an-intents-extension"></a>创建意向扩展

Siri 调用意向时运行的代码放置在一个意向扩展中，该扩展可作为新项目添加到与现有 Xamarin iOS 应用（如 Soup Chef）相同的解决方案。 在**SoupChef**解决方案中，扩展称为**SoupChefIntents**。

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – info.plist 和 info.plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – Info. info.plist

**SoupChefIntents**项目中的**Info.plist**将**绑定标识符**定义为 `com.xamarin.SoupChef.SoupChefIntents`。

**Info.plist**文件还包含以下内容：

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

在上面的**信息中。 info.plist**：

- `IntentsRestrictedWhileLocked` 列出只应在设备解锁时进行处理的方法。
- `IntentsSupported` 列出此扩展处理的意向。
- `NSExtensionPointIdentifier` 指定应用扩展的类型（有关详细信息，请参阅[Apple 的文档](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)）。
- `NSExtensionPrincipalClass` 指定应该用于处理此扩展所支持的方法的类。

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – info.plist

**SoupChefIntents**项目中的**Info.plist**具有**应用组**功能。 此功能配置为使用与**SoupChef**项目相同的应用组：

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Soup Chef 将数据与 `NSUserDefaults`保持不变。 为了在应用程序和应用扩展之间共享数据，它们在**info.plist**文件中引用了同一应用组。

> [!NOTE]
> **SoupChefIntents**项目的生成配置将**自定义权利**设置为**info.plist**。

#### <a name="handling-an-ordersoupintent-background-task"></a>处理 OrderSoupIntent 后台任务

意向扩展会根据自定义意向为快捷方式执行必要的后台任务。

Siri 调用 `IntentHandler` 类的[`GetHandler`](xref:Intents.INExtension.GetHandler*)方法（在**info.plist**中定义为 `NSExtensionPrincipalClass`）来获取扩展 `OrderSoupIntentHandling`的类的实例，该实例可用于处理 `OrderSoupIntent`：

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

在共享代码的**SoupKit**项目中定义的 `OrderSoupIntentHandler`实现了两个重要的方法：

- `ConfirmOrderSoup` –确认是否确实要执行与意向关联的任务
- `HandleOrderSoup` –通过调用传入的完成处理程序来放置 soup 订单并响应用户

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>处理打开应用程序的 OrderSoupIntent

应用程序必须正确地处理不在后台运行的意向。
它们的处理方式与 `NSUserActivity` 快捷键相同，`AppDelegate`的 `ContinueUserActivity` 方法：

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

意向 UI 扩展为意向扩展提供自定义用户界面。 在**SoupChef**解决方案中， **SoupChefIntentsUI**是一种提供**SoupChefIntents**接口的意向 UI 扩展。

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – info.plist 和 info.plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – Info. info.plist

**SoupChefIntentsUI**项目中的**Info.plist**将**绑定标识符**定义为 `com.xamarin.SoupChef.SoupChefIntentsui`。

**Info.plist**文件还包含以下内容：

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

在上面的**信息中。 info.plist**：

- `IntentsSupported` 指示 `OrderSoupIntent` 由此意向 UI 扩展处理。
- `NSExtensionPointIdentifier` 指定应用扩展的类型（有关详细信息，请参阅[Apple 的文档](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15)）。
- `NSExtensionMainStoryboard` 指定用于定义此扩展的主接口的情节提要

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – info.plist

**SoupChefIntentsUI**项目不需要**info.plist**文件。

### <a name="creating-the-user-interface"></a>创建用户界面

由于**info.plist** for **SoupChefIntentsUI**将 `NSExtensionMainStoryboard` 密钥设置为 `MainInterface`，因此**MAININTERACE**文件将定义意向 UI 扩展的接口。

在此情节提要中，有一个类型为**IntentViewController**的视图控制器。 它引用了两个视图：

- **invoiceView**，类型 `InvoiceView`
- **confirmationView**，类型 `ConfirmOrderView`

> [!NOTE]
> **InvoiceView**和**confirmationView**的接口在**主情节提要**中定义为辅助视图。 Visual Studio for Mac 和 Visual Studio 2017 中的 iOS 设计器不支持查看或编辑辅助视图;为此，请在**Xcode 的 Interface Builder 中打开**。

`IntentViewController` 实现[`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
接口，用于在使用 Siri 时提供自定义接口。 [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
调用方法以自定义接口，并显示确认或发票，具体取决于是否要确认交互（[`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)）或是否已成功执行（[`INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)）：

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
> 有关 `ConfigureView` 方法的详细信息，请观看 Apple 的 WWDC 2017 演示文稿， [SiriKit 中的新增功能](https://developer.apple.com/videos/play/wwdc2017/214/)。

## <a name="creating-a-voice-shortcut"></a>创建语音快捷方式

Soup Chef 提供了一个接口，用于为每个订单分配一个语音快捷方式，使其可以使用 Siri 对 Soup 进行排序。 事实上，用于记录和分配语音快捷方式的接口是由 iOS 提供的，并且只需要很少的自定义代码。

在 `OrderDetailViewController`中，当用户点击该表的 "**添加到 Siri** " 行时， [`RowSelected`](xref:UIKit.UITableViewSource.RowSelected*)方法会显示一个用于添加或编辑语音快捷方式的屏幕：

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

根据当前显示的顺序是否存在现有的语音快捷方式 `RowSelected` 显示[`INUIEditVoiceShortcutViewController`](xref:IntentsUI.INUIEditVoiceShortcutViewController)或[`INUIAddVoiceShortcutViewController`](xref:IntentsUI.INUIAddVoiceShortcutViewController)类型的视图控制器。
在每种情况下，`OrderDetailViewController` 会将自身设置为视图控制器的 `Delegate`，这就是它也实现的原因[`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
和[`IINUIEditVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate)。

## <a name="testing-on-device"></a>在设备上测试

若要在设备上运行 Soup Chef，请按照以下说明进行操作。 另请阅读[有关自动预配的说明](#automatic-provisioning)。

### <a name="app-group-app-ids-provisioning-profiles"></a>应用程序组，应用程序 Id，预配配置文件

在[Apple 开发人员门户](https://developer.apple.com/)的 "**证书、Id & 配置文件**" 部分中，执行以下操作：

- 创建应用组，以在 Soup Chef 应用及其扩展之间共享数据。 例如： **com.lookout.enterprise.yourcompanyname. SoupChef**

- 创建三个应用 Id：一个用于应用本身，一个用于意向扩展，另一个用于意向 UI 扩展。 例如:

  - 应用： **com.lookout.enterprise.yourcompanyname. SoupChef**
    - 对于此应用程序 ID，请分配 SiriKit 和**应用组**功能。

  - 意向扩展： **com.lookout.enterprise.yourcompanyname. SoupChef**
    - 对于此应用程序 ID，分配**应用组**功能。

  - 意向 UI 扩展： **com.lookout.enterprise.yourcompanyname. SoupChef. Intentsui**
    - 此应用 ID 不需要特殊功能。

- 创建以上应用 Id 后，编辑分配给应用的**应用组**功能和意向扩展，并指定上面创建的特定应用组。

- 创建三个新的开发预配配置文件，每个新的应用 Id 各有一个。

- 下载这些预配配置文件，然后双击每个配置文件进行安装。 如果 Visual Studio for Mac 或 Visual Studio 2017 已在运行，请重新启动它以确保它注册了新的预配配置文件。

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>编辑 info.plist、info.plist 和源代码

在 Visual Studio for Mac 或 Visual Studio 2017 中，执行以下操作：

- 更新解决方案中的各种**info.plist**文件。 将应用、意向扩展和意向 UI 扩展**捆绑标识符**设置为上面定义的应用 id：

  - 应用： **com.lookout.enterprise.yourcompanyname. SoupChef**
  - 意向扩展： **com.lookout.enterprise.yourcompanyname. SoupChef**
  - 意向 UI 扩展： **com.lookout.enterprise.yourcompanyname. SoupChef. Intentsui**

- 更新**SoupChef**项目的**info.plist**文件：
  - 对于 "**应用组**" 功能，请将组设置为前面创建的新应用组（在上面的示例中为**com.lookout.enterprise.yourcompanyname. SoupChef**）。
  - 请确保已启用**SiriKit** 。

- 更新**SoupChefIntents**项目的**info.plist**文件：
  - 对于 "**应用组**" 功能，请将组设置为前面创建的新应用组（在上面的示例中为**com.lookout.enterprise.yourcompanyname. SoupChef**）。

- 最后，打开**NSUserDefaultsHelper.cs**。 将 `AppGroup` 变量设置为新应用组的值（例如，将其设置为 `group.com.yourcompanyname.SoupChef`）。

### <a name="configuring-the-build-settings"></a>配置生成设置

在 Visual Studio for Mac 或 Visual Studio 2017 中：

- 打开**SoupChef**项目的选项/属性。 在 " **IOS 捆绑签名**" 选项卡上，将 "**签名标识**" 设置为 "自动"，并将**配置文件**设置为前面创建的新应用特定的配置文件。

- 打开**SoupChefIntents**项目的选项/属性。 在 " **IOS 捆绑签名**" 选项卡上，将 "**签名标识**" 设置为 "自动"，并将**配置文件**设置为之前创建的特定于扩展插件的预配配置文件。

- 打开**SoupChefIntentsUI**项目的选项/属性。 在 " **IOS 捆绑签名**" 选项卡上，将 "**签名标识**" 设置为 "自动"，并将**配置文件**设置为前面创建的新意向 UI 扩展插件配置文件。

进行这些更改后，应用将在 iOS 设备上运行。

### <a name="automatic-provisioning"></a>自动预配

请注意，可以使用[自动预配](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning)在 IDE 中直接完成许多预配任务。
但是，自动预配不会设置应用组。 你将需要使用你要使用的应用组的名称手动配置**info.plist**文件，访问 Apple 开发人员门户以创建应用组，将该应用组分配给自动预配创建的每个应用 ID、重新生成预配配置文件（应用、意向扩展、意向 UI 扩展）以包含新创建的应用组，然后下载并安装它们。

## <a name="related-links"></a>相关链接

- [Soup Chef （Xamarin）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef)
- [Soup Chef （Swift）](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit （Apple）](https://developer.apple.com/sirikit/)
- [Siri 快捷方式简介– WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [用 Siri 快捷方式生成语音-WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Siri 的 Siri 快捷方式-WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [SiriKit 中的新增功能-WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [创建意向应用扩展（Apple）](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
