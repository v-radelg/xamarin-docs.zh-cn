---
title: 在 Xamarin 中提供 Haptic 的反馈
description: 本文档介绍如何在 Xamarin iOS 应用程序中提供 haptic 反馈。 它讨论了 UIImpactFeedbackGenerator、UINotificationFeedbackGenerator 和 UISelectionFeedbackGenerator。
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 156af7a5336ac091c0202e38a3a59a32846e281a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003345"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>在 Xamarin 中提供 Haptic 的反馈

<a name="Overview" />

## <a name="overview"></a>概述

在 iPhone 7 和 iPhone 7 Plus 上，Apple 包含了新的 haptic 响应，这些响应提供了用于以物理方式吸引用户的其他方式。 Haptic 反馈（通常简称为 Haptics）在用户界面设计中使用触控（通过强制、vibrations 或运动）。 使用这些新的 tactile 反馈选项，使用户注意并强化其操作。

将详细介绍以下主题：

- [关于 Haptic 反馈](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>关于 Haptic 反馈

几个内置 UI 元素已经提供了 haptic 的反馈，如选取器、开关和滑杆。 iOS 10 现在添加了使用 `UIFeedbackGenerator` 类的具体子类以编程方式触发 haptics 的功能。

开发人员可以使用以下 `UIFeedbackGenerator` 子类之一以编程方式触发 haptic 反馈：

- `UIImpactFeedbackGenerator`-使用此反馈生成器来补充某个操作或任务（例如，当视图滑入时显示 "thud"，或者两个屏幕对象发生冲突）。
- `UINotificationFeedbackGenerator`-将此反馈生成器用于通知，如操作完成、失败或任何其他类型的警告。
- `UISelectionFeedbackGenerator`-对正在进行的选择（如从列表中选取一项）使用此反馈生成器。

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

使用此反馈生成器可以对某个操作或任务（例如，当视图滑入时提供 "thud" 或两个屏幕对象发生冲突）进行补充。

使用以下代码来触发影响反馈：

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

当开发人员创建 `UIImpactFeedbackGenerator` 类的新实例时，它们提供 `UIImpactFeedbackStyle` 指定反馈强度的：

- `Heavy`
- `Medium`
- `Light`

调用 `UIImpactFeedbackGenerator` 的 `Prepare` 方法，以通知系统 haptic 反馈即将发生，以便能够最大程度地减少延迟。

然后，`ImpactOccurred` 方法将触发 haptic 反馈。

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

此反馈生成器用于通知，如操作完成、失败或任何其他类型的警告。

使用以下代码来触发通知反馈：

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

将创建 `UINotificationFeedbackGenerator` 类的新实例，并调用其 `Prepare` 方法，以通知系统 haptic 反馈，以便能够最大程度地减少延迟。

调用 `NotificationOccurred` 来触发具有给定 `UINotificationFeedbackType` 的 haptic 反馈：

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

对于正在进行的选择（如从列表中选取一项），请使用此反馈生成器。

使用以下代码来触发选择反馈：

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

将创建 `UISelectionFeedbackGenerator` 类的新实例，并调用其 `Prepare` 方法，以通知系统 haptic 反馈，以便能够最大程度地减少延迟。

然后，`SelectionChanged` 方法将触发 haptic 反馈。

## <a name="summary"></a>总结

本文介绍了 iOS 10 中提供的新类型的 haptic 反馈，以及如何在 Xamarin 中实现它们。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
