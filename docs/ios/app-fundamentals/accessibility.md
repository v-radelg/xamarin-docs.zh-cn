---
title: IOS 上的辅助功能
description: 本文档介绍了 iOS 中的辅助功能，并讨论了可用于使应用程序可供多个用户使用的各种属性和功能。
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/18/2016
ms.openlocfilehash: 943cdfaee07bc4fd4ed3273840036055ad40b89a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766751"
---
# <a name="accessibility-on-ios"></a>IOS 上的辅助功能

本页介绍如何使用 iOS 辅助功能 Api 根据[辅助功能清单](~/cross-platform/app-fundamentals/accessibility.md)来构建应用。
有关其他平台 Api，请参阅[Android 辅助功能](~/android/app-fundamentals/accessibility.md)和[OS X 辅助功能](~/mac/app-fundamentals/accessibility.md)页面。

## <a name="describing-ui-elements"></a>描述 UI 元素

iOS 为开发`AccessibilityLabel`人员`AccessibilityHint`提供了和属性，以添加可供 VoiceOver 屏幕阅读器使用的描述性文本，使控件更易于访问。 控件还可以用一个或多个特征进行标记，这些特征在可访问模式下提供其他上下文。

某些控件可能不需要访问（例如，文本输入中的标签或纯粹装饰的图像）–提供此`IsAccessibilityElement`功能可在这些情况下禁用辅助功能。

**UI 设计器**

**Properties Pad**包含可访问性部分，允许在 IOS UI 设计器中选择控件时编辑这些设置：

![](accessibility-images/ios-designer-sml.png "辅助功能设置")

**C#**

也可以在代码中直接设置这些属性：

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>什么是 AccessibilityIdentifier？

`AccessibilityIdentifier`用于设置唯一键，该键可用于通过 UIAutomation API 引用用户界面元素。

的值`AccessibilityIdentifier`永远不会被口述或显示给用户。

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

`UIAccessibility.PostNotification`方法允许事件在直接交互之外（例如，当用户与特定控件交互时）向用户引发。

### <a name="announcement"></a>公告

可以从代码发送公告，通知用户某些状态已更改（例如后台操作已完成）。 在用户界面中，这可能伴随着一个直观的指示：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

屏幕布局时使用公告：`LayoutChanged`

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```

## <a name="accessibility-and-localization"></a>辅助功能和本地化

像标签和提示这样的可访问性属性可以像用户界面中的其他文本那样进行本地化。

**MainStoryboard.strings**

如果用户界面在情节提要中布局，则可以采用与其他属性相同的方式为辅助功能属性提供翻译。 在下面的示例中， `UITextField`的`Pqa-aa-ury` **本地化 ID**为，并且在西班牙语中设置了两个可访问性属性：

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

此文件将放在西班牙语内容的**lproj**目录中。

**Localizable.strings**

或者，可将翻译添加到本地化内容目录中的可**本地化的字符串**文件中（例如 西班牙语的**lproj** ）：

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

这些翻译可通过C# `LocalizedString`方法在中使用：

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

有关本地化内容的更多详细信息，请参阅[iOS 本地化指南](~/ios/app-fundamentals/localization/index.md)。

<a name="testing" />

## <a name="testing-accessibility"></a>测试辅助功能

通过导航到**一般 > 辅助功能 > VoiceOver**，在 "**设置**" 应用中启用 VoiceOver：

![](accessibility-images/settings-sml.png "设置语速")

**辅助功能**屏幕还提供了 "缩放"、"文本大小"、"颜色 & 对比度" 选项、"语音设置" 和其他配置选项的设置。

请按照以下[VoiceOver 说明](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)测试 iOS 设备上的辅助功能。

## <a name="simulator-testing"></a>模拟器测试

在模拟器中测试时，可**访问性检查器**可用于帮助验证辅助功能属性和事件是否正确配置。 在 "**设置**" 应用中打开检查器，方法是导航到 "**常规" > 辅助功能 > 辅助功能检查器**：

![](accessibility-images/settings-inspector-sml.png "启用辅助功能检查器")

启用后，检查器窗口将始终悬停在 iOS 屏幕上。
下面是在选择表视图行时的输出示例-请注意，**标签**包含提供行内容以及 "完成" 的句子（即勾选标记可见）：

![](accessibility-images/tableview-a11y-sml.png "使用辅助功能检查器")

如果检查器可见，请使用左上角的 "X" 图标来暂时显示和隐藏覆盖区，并启用/禁用辅助功能设置。

## <a name="related-links"></a>相关链接

- [跨平台辅助功能](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS 辅助功能（Apple）](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
