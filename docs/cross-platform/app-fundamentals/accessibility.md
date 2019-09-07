---
title: Xamarin 应用中的辅助功能
description: 本文档提供了创建辅助性应用程序的各种提示。 例如，它包括有关大字体、高对比度、自描述接口等的建议。
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 55d531036336cdd6c3ac7efa1c5ba21b09a7be9e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758137"
---
# <a name="accessibility-in-xamarin-apps"></a>Xamarin 应用中的辅助功能

_确保你的应用程序可由最广泛的受众使用_

可访问性指的是设计应用程序用户界面的概念，这些应用程序用户界面可正常运行操作系统显示和输入辅助功能，例如大类型、高对比度、放大、屏幕阅读（文本到语音转换）、视觉或 haptic 反馈提示，以及替代输入法。

桌面和移动平台（如 iOS、Android 和 Windows）提供了内置的 Api，可帮助开发人员构建可访问的应用，如[Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)和[Apple 的 VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)。

## <a name="platform-specific-apis"></a>平台特定的 Api

若要实施本文档中的指导原则，请使用每个平台提供的 Api：

- [**Android 辅助功能**](~/android/app-fundamentals/accessibility.md)
- [**iOS 辅助功能**](~/ios/app-fundamentals/accessibility.md)
- [**OS X 辅助功能**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>辅助功能清单

按照这些提示操作，确保你的应用程序可供最广泛的受众访问。 有关其他信息，请查看[Android 辅助功能测试清单](https://developer.android.com/training/accessibility/testing.html)和[Apple 的辅助功能页面](http://www.apple.com/accessibility/)。

### <a name="support-large-fonts-and-high-contrast"></a>支持大字体和高对比度

避免硬编码控件维度，而应改用可调整大小以适应更大字体大小的布局。
在高对比度模式下测试颜色方案，以确保它们是可读的。

### <a name="make-the-user-interface-self-describing"></a>使用户界面自描述

标记用户界面的所有元素，其中包含与在每个平台上读取 Api 的屏幕兼容的描述性文本和提示。

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>确保图像和图标具有替换文字说明

应用程序用户界面中的图像和图标（例如，按钮或状态指示器）应使用可访问的说明进行标记。

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>设计具有可访问导航的可视化树

使用适当的布局控件或 Api，以便使用替代输入方法在控件之间导航与使用触摸屏的逻辑流相同。

排除屏幕读取器中不必要的元素（例如，已访问的字段的修饰图像或标签）。

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>请勿仅依赖于音频或颜色提示

避免出现以下情况：唯一指示进度、完成或其他状态是声音或颜色更改。 设计用户界面以包括清晰的视觉提示（仅适用于强化的声音和颜色），或添加特定的辅助功能指示器。

选择颜色时，尝试避免难以区分颜色的调色板。

### <a name="captioning-for-video-text-for-audio"></a>视频字幕、音频文本

提供视频内容的标题和音频内容的可读脚本。 它还有助于提供调整音频或视频内容速度的控件，并确保音量和播放/暂停按钮易于查找和使用。

### <a name="localize"></a>Localize

辅助功能说明可以（并且应该）在应用程序支持多种语言的位置本地化。

## <a name="related-links"></a>相关链接

- [Android 辅助功能](~/android/app-fundamentals/accessibility.md)
- [iOS 辅助功能](~/ios/app-fundamentals/accessibility.md)
- [OS X 辅助功能](~/mac/app-fundamentals/accessibility.md)
- [Xamarin。窗体辅助功能](~/xamarin-forms/app-fundamentals/accessibility/index.md)
