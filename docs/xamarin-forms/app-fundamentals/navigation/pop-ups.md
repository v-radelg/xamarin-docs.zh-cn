---
title: 显示弹出窗口
description: Xamarin.Forms 提供了两个弹出式用户界面元素：警报和操作表。 本文演示了如何使用警报和操作表 API 向用户询问简单的问题并指导用户完成任务。
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 36a253a46b76e4c883639ca683a8eab64700b064
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329307"
---
# <a name="displaying-pop-ups"></a>显示弹出窗口

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)

Xamarin.Forms 提供了两个弹出式用户界面元素：警报和操作表。本文演示了如何使用警报和操作表 API 向用户询问简单的问题并指导用户完成任务。_

显示警报或要求用户做出选择是一个常见的 UI 任务。 Xamarin.Forms 在 [`Page`](xref:Xamarin.Forms.Page) 类上有两个方法可以通过弹出窗口与用户交互：[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 和 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)。 在每个平台上使用相应的本机控件呈现。

## <a name="displaying-an-alert"></a>显示警报

所有 Xamarin.Forms 支持的平台都会弹出一个模式，提醒用户或向用户提出简单的问题。 要在 Xamarin.Forms 中显示这些警报，请在任何 [`Page`](xref:Xamarin.Forms.Page) 上使用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法。 以下代码行向用户显示一个简单的消息：

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "带有按钮的警告对话框")

本例不收集用户的信息。 警报以模式显示，一旦用户被解雇，就会继续与应用程序进行交互。

[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法还可以通过显示两个按钮并返回 `boolean` 来捕获用户的响应。 要从警报中获得响应，请为两个按钮和 `await` 方法提供文本。 在用户选择其中一个选项后，答案将返回到你的代码。 注意下面示例代码中的 `async` 和 `await` 关键字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![显示警报](pop-ups-images/alert2-sml.png "包含两个按钮的警报对话框")](pop-ups-images/alert2.png#lightbox "Alert Dialog with Two Buttons")

## <a name="guiding-users-through-tasks"></a>指导用户完成任务

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) 是 iOS 中的常见 UI 元素。 Xamarin.Forms[`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法可以在跨平台应用程序中包含此控件，并在 Android 和 UWP 中呈现本机备选方案。

要显示操作表（任何 [`Page`](xref:Xamarin.Forms.Page) 中的 `await`[`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)），请将消息和按钮标签作为字符串传递。 该方法返回供用户单击的按钮的字符串标签。 下面是简单示例：

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet 对话框")

`destroy` 按钮的呈现方式与其他按钮不同，可以保留 `null` 或指定为第三个字符串参数。 下面的示例使用 `destroy` 按钮：

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "带有销毁按钮的操作表对话框")](pop-ups-images/action2.png#lightbox "Action Sheet Dialog with Destroy Button")

## <a name="summary"></a>总结

本文演示了如何使用警报和操作表 API 向用户询问简单的问题并指导用户完成任务。 Xamarin.Forms 在 [`Page`](xref:Xamarin.Forms.Page) 类上有两个方法，用于通过弹出与用户交互：[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 和 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)，它们都在每个平台上使用相应的的本机控件呈现。



## <a name="related-links"></a>相关链接

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
