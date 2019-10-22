---
title: MacOS 上的辅助功能
description: 本文档介绍如何在 Xamarin 应用程序中使用 macOS 辅助功能。 本文介绍了介绍演示图板和代码、自定义控件和测试可访问性的 UI 元素。
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 087dcdc7024026e6a3ed3a05baca3b2648053cc8
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "70769950"
---
# <a name="accessibility-on-macos"></a>MacOS 上的辅助功能

本页介绍如何使用 macOS 辅助功能 Api 根据[辅助功能清单](~/cross-platform/app-fundamentals/accessibility.md)来构建应用。
有关其他平台 Api，请参阅[Android 辅助](~/android/app-fundamentals/accessibility.md)功能和[iOS 辅助功能](~/ios/app-fundamentals/accessibility.md)页面。

若要了解辅助功能 Api 在 macOS （以前称为 OS X）中的工作原理，请先查看[Os x 辅助功能模型](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html)。

## <a name="describing-ui-elements"></a>描述 UI 元素

AppKit 使用 `NSAccessibility` 协议来公开有助于使用户界面可访问的 Api。 这包括一个默认行为，该行为尝试为辅助功能属性设置有意义的值，例如设置按钮的 `AccessibilityLabel`。 标签通常是描述控件或视图的单个词或短语。

### <a name="storyboard-files"></a>情节提要文件

Xamarin 使用 Xcode Interface Builder 编辑情节提要文件。
在设计图面上选择控件时，可以在**标识检查器**中编辑辅助功能信息（如以下屏幕截图中所示）：

[![在 Xcode 的 Interface Builder 中添加辅助功能](accessibility-images/xcode.png "在 Xcode 的 Interface Builder 中添加辅助功能")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>代码

Xamarin 目前不会公开为 `AccessibilityLabel` 资源库。  添加以下 helper 方法以设置 "辅助功能" 标签：

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

然后可以在代码中使用此方法，如下所示：

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

@No__t_0 属性用于说明控件或视图的作用，只应在标签可能无法提供足够的信息时添加。 帮助文本仍应尽可能简短，例如 "删除文档"。

某些用户界面元素与可访问访问权限（例如，具有其自己的可访问性标签和帮助的输入旁边的标签）无关。
在这些情况下，请设置 `AccessibilityElement = false`，以便屏幕阅读器或其他辅助工具将跳过这些控件或视图。

Apple 提供的[辅助功能指导原则](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html)说明了可访问性标签和帮助文本的最佳实践。

## <a name="custom-controls"></a>自定义控件

有关[可访问的自定义控件](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html)的详细信息，请参阅 Apple 的准则准则。

## <a name="testing-accessibility"></a>测试辅助功能

macOS 提供可帮助测试辅助功能的**辅助功能检查器**。 该检查器包含在 Xcode 中。

首次启动时，**辅助功能检查器**将需要通过辅助功能控制计算机的权限：

![辅助功能检查器正在请求运行权限](accessibility-images/accessibility-inspector-1.png "辅助功能检查器正在请求运行权限")

解锁 "设置" 屏幕（如果需要，在左下角），然后勾选**辅助功能检查器**：

![启用辅助功能检查器的 "设置" 屏幕](accessibility-images/accessibility-inspector-2.png "启用辅助功能检查器的 "设置" 屏幕")

启用后，检查器将显示为可以在屏幕上移动的浮动窗口。 下面的屏幕截图显示了在示例 Mac 应用旁边运行的检查器。 当光标移动到窗口上时，检查器将显示每个控件的所有可访问属性：

[![辅助功能检查器运行示例](accessibility-images/accessibility-example.png "辅助功能检查器运行示例")](accessibility-images/accessibility-example-large.png#lightbox)

有关详细信息，请参阅[OS X 指南的测试可访问性](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html)。

## <a name="related-links"></a>相关链接

- [跨平台辅助功能](~/cross-platform/app-fundamentals/accessibility.md)
- [Mac 辅助功能](https://www.apple.com/accessibility/mac/)
