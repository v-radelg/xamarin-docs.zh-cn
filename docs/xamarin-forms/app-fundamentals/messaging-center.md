---
title: Xamarin.Forms MessagingCenter
description: 本文介绍如何使用 Xamarin.Forms MessagingCenter 发送和接收消息，以减少如视图模型等类之间的耦合度。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: b40617dc9ed2054540ce04d5527fae8de6e2285b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644898"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

_Xamarin.Forms 具有简单的消息传送服务，用于发送和接收消息。_

<a name="Overview" />

## <a name="overview"></a>概述

借助 Xamarin.Forms `MessagingCenter`只需一个简单的消息协定，而不必知道任何关于彼此的信息，便能实现视图模型和其他组件的相互通信。

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>MessagingCenter 的工作原理

`MessagingCenter` 分为两部分：

-  **订阅** - 侦听具有特定签名的消息，并在接收这些消息时执行某些操作。 多个订阅者可以侦听同一条消息。
-  **发送** - 发布供侦听器执行操作的消息。 如果没有侦听器订阅该消息，则忽略该消息。

`MessagingCenter` 是一个静态类，在整个解决方案中使用 `Subscribe` 和 `Send` 方法。

消息具有字符串 `message` 参数，用作定位消息的方式  。 `Subscribe` 和 `Send` 方法使用泛型参数进一步控制消息的接收方式 - 具有相同 `message` 文本但泛型类型参数不同的两则消息不会发送给同一个订阅者。

`MessagingCenter` 的 API 很简单：

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

下面介绍了这些方法。

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>使用 MessagingCenter

消息可能作为用户交互的结果发送（例如单击按钮）、作为系统事件发送（例如更改状态的控件）或作为某些其他事件发送（例如异步下载完成）。 订阅者可能会侦听用户界面的外观更改、保存数据或触发某个其他操作。

要详细了解如何使用 `MessagingCenter` 类，请参阅[在松散耦合组件之间通信](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)。

### <a name="simple-string-message"></a>简单字符串消息

最简单的消息只包含 `message` 参数中的一个字符串。 侦听简单字符串消息的 `Subscribe` 方法如下所示 - 请注意指定发送方应为 `MainPage` 类型的泛型类型  。 解决方案中的任何类都可以使用以下语法订阅消息：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

在 `MainPage` 类中，以下代码发送消息  。 `this` 参数是 `MainPage` 的一个实例。

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

字符串不会更改 - 它指示消息类型并用于确定通知哪些订阅者  。 此类消息用于指示发生了某些事件，例如“上传已完成”这类不需要更多消息的事件。

### <a name="passing-an-argument"></a>传递参数

若要使用消息传递参数，请在 `Subscribe` 泛型参数和操作签名中指定参数类型。

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

若要使用参数发送消息，请在 `Send` 方法调用中包含类型泛型参数和参数的值。

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

示例使用 `string` 参数，但可以传递任何 C# 对象。

### <a name="unsubscribe"></a>取消订阅

对象可以取消订阅消息签名，以便将来不会传递任何消息。 `Unsubscribe` 方法语法应反映消息签名（因此可能需要包括消息参数的泛型类型参数）。

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>总结

MessagingCenter 是减少耦合度的简单方法，尤其是视图模型之间的耦合度。 它可以用于发送和接收简单消息或在类之间传递参数。 类应取消订阅不再想要接收的消息。


## <a name="related-links"></a>相关链接

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
- [松散耦合组件之间的通信](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)
