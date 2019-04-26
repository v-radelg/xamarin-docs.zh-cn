---
title: 在 iOS 上的辅助功能
description: 本文档介绍了在 iOS 中，讨论各种属性和功能，可用于使你的应用程序可由任意数量的用户尽可能可访问性。
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/18/2016
ms.openlocfilehash: aa3e15797ae1dac621ea8a78345044be1387ebaa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61179518"
---
# <a name="accessibility-on-ios"></a>在 iOS 上的辅助功能

此页介绍了如何使用 iOS 可访问性 Api 构建应用程序根据[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。
请参阅[Android 可访问性](~/android/app-fundamentals/accessibility.md)并[OS X 辅助功能](~/mac/app-fundamentals/accessibility.md)页的其他平台 Api。

## <a name="describing-ui-elements"></a>描述 UI 元素

提供了 iOS`AccessibilityLabel`和`AccessibilityHint`属性开发人员能够添加说明性文本可以由语音朗读屏幕读取器可使控件更易于访问。 此外可以使用提供的其他上下文中可访问模式的一个或多个特征标记控件。

某些控件可能不需要的可访问 （例如，在文本输入或仅仅是装饰性的图像的标签） –`IsAccessibilityElement`提供以禁用在这些情况下可访问性。

**UI 设计器**

**Properties Pad**包含允许 iOS 用户界面设计器中选择一个控件时要编辑这些设置可访问性部分：

![](accessibility-images/ios-designer-sml.png "辅助功能设置")

**C#**

此外可以直接在代码中设置这些属性：

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>什么是 AccessibilityIdentifier？

`AccessibilityIdentifier`用于设置可用于指代 UIAutomation API 通过用户界面元素的唯一键。

值`AccessibilityIdentifier`永远不会读出或向用户显示。

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

`UIAccessibility.PostNotification`方法可将事件引发到外部 （例如，当它们与特定控件交互时） 的直接交互用户。

### <a name="announcement"></a>公告

若要通知的用户的某个状态发生更改 （例如，后台操作已完成），可以从代码发送公告。 这可能伴随着用户界面中的直观指示：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged

`LayoutChanged`使用公告时屏幕布局：

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>可访问性和本地化

辅助功能属性一样可以只是本地化的标签和提示，例如用户界面中的其他文本。

**MainStoryboard.strings**

如果情节提要中的用户界面布局方式，您可以与其他属性相同的方式提供辅助功能属性的翻译。 在以下示例中，`UITextField`已**本地化 ID**的`Pqa-aa-ury`和在西班牙语中设置的两个可访问性属性：

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

此文件会放**es.lproj**西班牙语内容目录。

**Localizable.strings**

或者，可以将翻译添加到**Localizable.strings**本地化内容目录 （例如中的文件。 **es.lproj**西班牙语):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

这些转换可在C#通过`LocalizedString`方法：

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

请参阅[iOS 本地化指南](~/ios/app-fundamentals/localization/index.md)有关本地化内容的详细信息。

<a name="testing" />

## <a name="testing-accessibility"></a>测试辅助功能

在中启用语音朗读**设置**通过导航到应用程序**常规 > 可访问性 > VoiceOver**:

![](accessibility-images/settings-sml.png "设置的语速")

**可访问性**屏幕还提供了有关缩放、 文本大小、 颜色和对比度选项、 语音设置和其他配置选项的设置。

请按照这些[VoiceOver 说明](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html)在 iOS 设备上的辅助功能进行测试。


## <a name="simulator-testing"></a>模拟器测试

在模拟器中测试时**可访问性检查器**是可用于帮助验证是否正确配置的辅助功能属性和事件。 检查器中打开**设置**通过导航到应用程序**常规 > 可访问性 > 可访问性检查器**:

![](accessibility-images/settings-inspector-sml.png "启用辅助功能检查器")

启用后，检查器窗口将鼠标悬停在 iOS 屏幕在所有时间。
下面是输出的示例，选择表视图行时，请注意**标签**包含提供的行，并且确保它"完成"的内容的句子 (ie。 刻度线是可见):

![](accessibility-images/tableview-a11y-sml.png "使用可访问性检查器")

该检查器可见时，使用在左上方的"X"图标以暂时显示和隐藏在覆盖区上并启用/禁用辅助功能设置。



## <a name="related-links"></a>相关链接

- [跨平台辅助功能](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS (Apple) 的辅助功能](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
