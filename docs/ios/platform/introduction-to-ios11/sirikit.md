---
title: IOS 11 中 SiriKit 更新
description: 本文档介绍如何使用 SiriKit，iOS 11 中。 具体而言，它将检查如何使用任务和便笺以及如何提供应用程序的可选名称。
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/07/2017
ms.openlocfilehash: 7e895dc2865880ec2789a40f8cdf047a20f8693b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400277"
---
# <a name="sirikit-updates-in-ios-11"></a>IOS 11 中 SiriKit 更新

SiriKit iOS 10 中引入了许多服务域 （包括锻炼，持续一段时间的预订，并调用）。 请参阅[SiriKit 部分](~/ios/platform/sirikit/index.md)SiriKit 概念以及如何在应用中实现 SiriKit。

![使用 Siri 任务列表演示](sirikit-images/sirikit.png)

SiriKit，iOS 11 中的添加这些新的和更新的意向域：

- [**列出并说明**](#listsnotes) – 新 ！ 提供用于应用的 API 来处理任务和便笺。
- **可视化代码**– 新 ！ 使用 Siri 可以显示共享联系人信息或参与支付交易的 QR 代码。
- **付款**– 添加付款的交互，搜索和传输意图。
- **写预订**– 添加了取消持续一段时间和反馈意图。

其他新功能包括：

- [**备用的应用名称**](#alternativenames) – 提供别名，可帮助客户告知 Siri 面向您的应用程序通过提供其他名称/发音。
- **启动锻炼**– 提供了在后台启动健身的功能。

下面介绍了其中一些功能。 有关其他详细信息，请参阅[Apple 的 SiriKit 文档](https://developer.apple.com/documentation/sirikit)。

<a name="listsnotes" />

## <a name="lists-and-notes"></a>列表和说明

新的列表和说明域提供应用的 API 来处理任务并通过使用 Siri 语音请求的说明。

**任务**

- 具有标题和完成状态。
- 选择性地包含了截止时间和位置。

**备注**

- 具有标题和内容的字段。

任务和便笺可以组织成组。 本部分的其余部分将介绍如何实现 SiriKit，此新的域使用[TasksNotes SiriKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)。

### <a name="how-to-process-a-sirikit-request"></a>如何处理 SiriKit 请求

通过执行以下步骤处理 SiriKit 请求：

1. **解决**– 验证参数和进一步信息从用户请求 （如果需要）。
2. **确认**– 最终验证和验证可以处理该请求。
3. **处理**– 执行操作 （更新数据或执行网络操作）。

前两个步骤是可选的 （尽管建议），并最终步骤是必需的。
有更多详细的说明中[SiriKit 部分](~/ios/platform/sirikit/index.md)。

### <a name="resolve-and-confirm-methods"></a>解决和确认方法

这些可选方法让你从用户执行验证、 选择默认值或请求其他信息的代码。

例如，对于`IINCreateTaskListIntent`接口，所需的方法是`HandleCreateTaskList`。 有四种可选方法，提供更好地控制 Siri 交互：

- `ResolveTitle` – 验证标题、 设置的默认标题 （如果适用），或发出信号不需要的数据。
- `ResolveTaskTitles` – 验证用户所说的任务的列表。
- `ResolveGroupName` – 验证组名称，选择默认组，或发出信号不需要的数据。
- `ConfirmCreateTaskList` -验证你的代码可以执行请求的操作，但不会执行它 (只`Handle*`方法应修改数据)。

### <a name="handle-the-intent"></a>处理意向

有六个意向列表和说明域中的，三个任务和三个用于说明。
必须实现以处理这些意向的方法是：

- 对于任务：
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- 有关说明：
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

每个方法都具有特定意向类型传递给它，其中包含使用 Siri 从用户的请求已分析的所有信息 (并且可能在更新`Resolve*`和`Confirm*`方法)。
您的应用程序必须分析的数据提供，则执行某些操作来存储或以其他方式处理数据，并返回 Siri，并向用户显示的结果。

### <a name="response-codes"></a>响应代码

所需`Handle*`和可选`Confirm*`方法指示通过其传递对对象的值设置为其完成处理程序的响应代码。 响应来自`INCreateTaskListIntentResponseCode`枚举：

- `Ready` – 在确认阶段返回 (ie。 从`Confirm*`方法，但不能从`Handle*`方法)。
- `InProgress` – 用于长时间运行任务 （如网络/服务器操作）。
- `Success` -成功的操作的详细信息响应 (只能从`Handle*`方法)。
- `Failure` – 意味着出现了错误，并且无法完成该操作。
- `RequiringAppLaunch` -无法处理的目的，但该操作可以在应用程序。
- `Unspecified` -不要使用： 将向用户显示错误消息。

了解有关这些方法和在 Apple 的响应的详细信息[SiriKit 列出并说明文档](https://developer.apple.com/documentation/sirikit/lists_and_notes)。

### <a name="implementing-lists-and-notes"></a>实现列表和说明

[TasksNotes SiriKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)使用以下步骤将 SiriKit 支持添加到一个空白 iOS 应用创建的。

首先，若要添加 SiriKit 的支持，请执行以下步骤将 iOS 应用程序：

1. 刻度线**SiriKit**中**Entitlements.plist**。
2. 添加**隐私-Siri 使用说明**关键**Info.plist**，以及为客户一条消息。
3. 调用`INPreferences.RequestSiriAuthorization`在应用中，以提示用户允许使用 Siri 交互的方法。
4. 将 SiriKit 添加到你在开发人员门户上的应用 ID 并重新创建预配配置文件以包括新的权利。

然后将新的扩展项目添加到您的应用程序处理 Siri 请求：

1. 在解决方案上右键单击并选择**添加 > 添加新项目...**.
2. 选择**iOS > 扩展 > Intents 扩展**模板。
3. 将添加两个新项目：意向和 IntentUI。 自定义 UI 是可选的因此该示例仅包含中的代码**意向**项目。

扩展项目是在其中将处理所有 SiriKit 请求。 为单独的扩展，它自动没有任何方式与主应用程序 – 通信通常解决此问题通过实现共享的文件存储中使用应用组。

#### <a name="configure-the-intenthandler"></a>配置 IntentHandler

`IntentHandler`类是入口点，对于使用 Siri 请求 – 每个意向传递给`GetHandler`方法，它将返回一个对象，就可以处理请求。

下面的代码显示了一个简单的实现：

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

类必须继承`INExtension`，因为此示例将处理列表，并说明意向，它也能实现`IINNotebookDomainHandling`。

> [!NOTE]
> - 适用于接口以前缀为大写的.NET 中没有一种约定`I`，Xamarin iOS SDK 从绑定协议时所遵循的。
> - Xamarin 还将保留通过 iOS，类型名称和 Apple 使用前两个字符的类型名称以反映类型所属的框架。
> - 有关`Intents`框架中，类型都带有前缀`IN*`（例如。 `INExtension`) 但是这些_不_接口。
> - 遵循该协议 (这会变得中的接口C#) 具有两个最终`I`s，如`IINAddTasksIntentHandling`。

#### <a name="handling-intents"></a>处理意向

每个意向 （添加任务、 设置任务属性等） 实现在单个方法类似于如下所示。 此方法应执行三个主要功能：

1. **处理意向**– 通过 Siri 分析的数据可供在`intent`对象特定于类型的意图。 您的应用程序可能已经验证可使用可选数据`Resolve*`方法。
2. **验证和更新数据存储区**– 保存到文件系统 （使用应用组，以便主 iOS 应用也可以访问它），或通过网络请求的数据。
3. **提供响应**– 使用`completion`处理程序以将响应发送回 Siri 读取/显示给用户：

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

请注意，`null`传递响应 – 第二个参数，这是用户活动参数，以及时未提供，将使用默认值。
可以自定义活动类型设置，只要你的 iOS 应用支持通过`NSUserActivityTypes`中的键**Info.plist**。 然后可以打开您的应用程序时，处理这种情况，并执行特定操作 （例如打开到相关的视图控制器，并将数据加载从 Siri 操作）。

该示例还会`Success`结果，但在实际情况下，应添加正确的错误报告。

### <a name="test-phrases"></a>测试短语

以下测试短语应在示例应用程序中运行：

- "在 TasksNotes 使苹果、 香蕉，与梨购物列表"
- "添加任务 WWDC TasksNotes 中"
- "添加任务 WWDC 到中 TasksNotes 培训列表"
- "标记为已完成在 TasksNotes 参加 WWDC"
- "在 TasksNotes 提醒我在我回到家时购买 iphone"
- "标记为已完成在 TasksNotes 购买 iPhone"
- "提醒我 TasksNotes 在上午 8 点应保留主文件夹"

![创建新的列表示例](sirikit-images/createtasklist-sml.png) ![作为完整的示例设置的任务](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> IOS 11 模拟器支持使用 Siri （不同于较早版本） 进行测试。
>
> 如果在实际设备上测试，别忘了配置你的应用 ID 和预配 SiriKit 支持的配置文件。

<a name="alternativenames" />

## <a name="alternative-names"></a>可选名称

此新的 iOS 11 功能意味着你可以将配置应用以帮助用户使用 Siri 正确触发它的可选名称。 添加到以下项**Info.plist** iOS 应用项目文件：

![Info.plist 显示替代应用名称键和值](sirikit-images/alternative-names.png)

使用备用的应用程序名称集，以下短语也适用于示例应用程序 (其实际名为**TasksNotes**):

- "制作杂货列表苹果、 香蕉，与在梨_MonkeyNotes_"
- "添加任务中的 WWDC _MonkeyTodo_"


## <a name="troubleshooting"></a>疑难解答

运行示例，或将 SiriKit 添加到自己的应用程序时可能遇到一些错误：

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Objective C 中引发异常。姓名:NSInternalInconsistencyException 原因：使用类 < INPreferences:0x60400082ff00 > 从应用程序需要授权 com.apple.developer.siri。您是否在 Xcode 项目中启用 Siri 功能？_

- 在中勾选了 SiriKit **Entitlements.plist**。
- **Entitlements.plist**中配置**项目选项 > 生成 > iOS 捆绑签名**。

  [![显示权利正确设置项目选项](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- （适用于设备部署）应用程序 ID 已启用的 SiriKit，预配配置文件下载。


## <a name="related-links"></a>相关链接

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [TasksNotes SiriKit 示例](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [What's New 中 SiriKit (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/214/)
