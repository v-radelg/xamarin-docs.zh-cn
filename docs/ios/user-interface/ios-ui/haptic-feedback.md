---
title: 提供 Haptic 反馈在 Xamarin.iOS
description: 本文档介绍如何提供 haptic 反馈 Xamarin.iOS 应用程序中。 它讨论 UIImpactFeedbackGenerator、 UINotificationFeedbackGenerator 和 UISelectionFeedbackGenerator。
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b2c381c59ba1574e80babc2c7e68535a3deffe35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384614"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>提供 Haptic 反馈在 Xamarin.iOS

<a name="Overview" />

## <a name="overview"></a>概述

在 iPhone 7 和 iPhone 7 以及 Apple 已包含提供以物理方式吸引用户的其他方法的新 haptic 响应。 Haptic 反馈 （通常简称为 Haptics） 用户界面设计中使用触摸 （通过强制、 振动或运动） 有的意义。 使用这些新的边用反馈选项来获取用户的注意力并加强其操作。

将详细介绍以下主题：

- [有关 Haptic 反馈](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>有关 Haptic 反馈

多个内置的 UI 元素已提供 haptic 反馈，例如选取器、 交换机和滑块。 iOS 10 现在将能够以编程方式触发 haptics 使用的具体子类`UIFeedbackGenerator`类。

开发人员可以使用下列任一`UIFeedbackGenerator`子类以编程方式触发器 haptic 反馈：

- `UIImpactFeedbackGenerator` -使用此反馈生成器来补充操作或任务，如当视图滑入位置或两个屏幕上的对象发生碰撞呈现"thud"。
- `UINotificationFeedbackGenerator` -将此反馈生成器用于通知，如操作完成、 失败或任何其他类型的警告。
- `UISelectionFeedbackGenerator` -将此反馈生成器用于主动更改如选取列表中的项的选择。

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

使用此反馈生成器操作或任务，如当视图滑入位置或两个屏幕上的对象发生碰撞呈现"thud"补充。

使用触发器的影响反馈的以下代码：

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

当开发人员创建的新实例`UIImpactFeedbackGenerator`类，它们提供`UIImpactFeedbackStyle`指定作为反馈的强度：

- `Heavy`
- `Medium`
- `Light`

`Prepare`方法的`UIImpactFeedbackGenerator`调用以通知系统 haptic 反馈即将发生，以便它可以尽量减少延迟。

`ImpactOccurred`方法然后触发 haptic 反馈。

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

此反馈生成器用于通知，如操作完成、 失败或任何其他类型的警告。

使用以下代码到触发器通知反馈：

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

新实例`UINotificationFeedbackGenerator`创建类并将其`Prepare`方法调用以通知系统 haptic 反馈即将发生，以便它可以尽量减少延迟。

`NotificationOccurred`调用以触发 haptic 反馈与给定`UINotificationFeedbackType`的：

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

此反馈生成器用于主动更改如选取列表中的项选择。

使用触发器所选内容的反馈的以下代码：

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

新实例`UISelectionFeedbackGenerator`创建类并将其`Prepare`方法调用以通知系统 haptic 反馈即将发生，以便它可以尽量减少延迟。

`SelectionChanged`方法然后触发 haptic 反馈。

## <a name="summary"></a>总结

本文只讨论了 iOS 10 和如何在 Xamarin.iOS 中实现这些中提供 haptic 反馈的新类型。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
