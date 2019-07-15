---
title: Xamarin.Forms 应用程序生命周期
description: 此文章介绍了如何响应应用程序生命周期，包括生命周期方法、页面通知事件和模式导航事件。
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 41e8d073982bf7963b3a77a939bf28e52e86feaa
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675178"
---
# <a name="xamarinforms-app-lifecycle"></a>Xamarin.Forms 应用程序生命周期

[`Application`](xref:Xamarin.Forms.Application) 基类提供下列功能：

- [生命周期方法](#Lifecycle_Methods) `OnStart`、`OnSleep` 和 `OnResume`。
- [页导航](#page) [ `PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)、[`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)。
- [模式导航事件](#modal) `ModalPushing`、`ModalPushed`、`ModalPopping` 和 `ModalPopped`。

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>生命周期方法

[`Application`](xref:Xamarin.Forms.Application) 类包含三个虚拟方法，可以替代以响应生命周期更改：

- `OnStart` - 在启动应用程序时调用它。
- `OnSleep` - 每当应用程序转入后台时调用它。
- `OnResume` - 应用程序发送到后台后恢复时调用。

> [!NOTE]
>  没有终止应用程序的方法。 在正常情况下（即不崩溃），应用程序终止将从 OnSleep 状态发生，并且没有对代码的其他任何通知  。

若要观察何时调用这些方法，请在每个平台上实现 `WriteLine` 调用（如下所示）并进行测试。

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

> [!IMPORTANT]
> 在 Android 上，若主活动的 `[Activity()]` 属性缺少 `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation`，旋转时及首次启动应用程序时，将调用 `OnStart` 方法。

<a name="page" />

## <a name="page-notification-events"></a>页面通知事件

[`Application`](xref:Xamarin.Forms.Application) 类上有两个事件，可提供页面显示和消失通知：

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) - 页面即将在屏幕上显示时引发。
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) - 页面即将从屏幕上消失时引发。

这些事件可用于要在页面在屏幕上显示时进行跟踪的场景。

> [!NOTE]
> 在 [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) 和 [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) 事件后，从 [`Page`](xref:Xamarin.Forms.Page) 基类中将分别引发 [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) 和 [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) 事件。

<a name="modal" />

## <a name="modal-navigation-events"></a>模式导航事件

[`Application`](xref:Xamarin.Forms.Application) 类上有四个事件，每个都有其自己的参数，使你能够响应如下所示和解除的模式页面：

- `ModalPushing` - 按模式推送页面时将引发它。
- `ModalPushed` - 按模式推送页面后将引发它。
- `ModalPopping` - 按模式弹出页面时将引发它。
- `ModalPopped` - 按模式弹出页面后将引发它。

> [!NOTE]
> `ModalPoppingEventArgs` 类型的 `ModalPopping` 事件参数，包含 `Cancel` 属性。 如果 `Cancel` 设置为 `true`，将取消模式弹出。
