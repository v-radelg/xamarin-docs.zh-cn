---
title: 在 macOS 上的辅助功能
description: 本文档介绍如何使用 macOS 中的 Xamarin.Mac 应用的可访问性功能。 它讨论了描述的 UI 元素中的情节提要和代码、 自定义控件和测试可访问性。
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fdda52309ffdb0d32cc42a4dff052cd9050b1e4f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378430"
---
# <a name="accessibility-on-macos"></a>在 macOS 上的辅助功能

此页介绍了如何使用 macOS 辅助功能 Api 来构建应用程序根据[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。
请参阅[Android 可访问性](~/android/app-fundamentals/accessibility.md)并[iOS 可访问性](~/ios/app-fundamentals/accessibility.md)页的其他平台 Api。

若要了解辅助功能 Api 中的工作原理 macOS （以前称为 OS X），首先回顾一下[OS X 辅助功能模型](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html)。

## <a name="describing-ui-elements"></a>描述 UI 元素

AppKit 使用`NSAccessibility`协议公开 Api 可帮助使用户界面可访问。 这包括尝试设置有意义的可访问性属性，例如，设置按钮的默认行为`AccessibilityLabel`。 标签通常是单独的词或短语描述控件或视图。

### <a name="storyboard-files"></a>情节提要文件

Xamarin.Mac 使用 Xcode 的 Interface Builder 编辑情节提要文件。
可以在中编辑的辅助功能信息**标识检查器**时选择控件在设计图面上 （如下面的屏幕截图中所示）：

[![在 Xcode 的 Interface Builder 中添加可访问性](accessibility-images/xcode.png "在 Xcode 的 Interface Builder 中添加可访问性")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>代码

作为当前不公开 Xamarin.Mac `AccessibilityLabel` setter。  添加以下帮助器方法用于设置可访问性标签：

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

所示，然后可以在代码中使用此方法：

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

`AccessibilityHelp`属性是有关视图的控件功能的说明，应仅添加时标签可能无法提供足够的信息。 帮助文本的示例"删除文档"每次仍应保留尽可能短。

某些用户界面元素不相关的可访问的访问 （例如旁边都有其自己的可访问性标签和帮助的输入的标签）。
在这些情况下，设置`AccessibilityElement = false`，以便这些控件或视图将被跳过屏幕阅读器或其他可访问性工具。

Apple 提供[辅助功能准则](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html)，介绍了可访问性标签和帮助文本的最佳实践。

## <a name="custom-controls"></a>自定义控件

请参阅 Apple 的[可访问的自定义控件的指导原则](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html)有关所需的其他步骤的详细信息。

## <a name="testing-accessibility"></a>测试辅助功能

macOS 提供**可访问性检查器**帮助测试可访问性功能。 与 Xcode 中包含该检查器。

它已启动，第一次**可访问性检查器**将需要通过可访问性控制计算机的权限：

![请求的权限来运行的可访问性检查器](accessibility-images/accessibility-inspector-1.png "要求运行权限的可访问性检查器")

解锁的设置屏幕 （如果需要，左下方） 和刻度线**可访问性检查器**:

![若要启用辅助功能检查器的设置屏幕](accessibility-images/accessibility-inspector-2.png "设置屏幕启用可访问性检查器")

启用后，该检查器将显示为可以在屏幕上四处移动的浮动窗口。 下面的屏幕截图显示了示例 Mac 应用旁边运行该检查器。 该时段内通过在移动光标时，该检查器将显示每个控件的所有可访问的属性：

[![可访问性检查器正在运行的示例](accessibility-images/accessibility-example.png "示例的可访问性检查器正在运行")](accessibility-images/accessibility-example-large.png#lightbox)

有关详细信息，请阅读[测试可访问性 for OS X 指南](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html)。



## <a name="related-links"></a>相关链接

- [跨平台可访问性](~/cross-platform/app-fundamentals/accessibility.md)
- [Mac 可访问性](https://www.apple.com/accessibility/mac/)
