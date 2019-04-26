---
title: Xamarin 应用中的辅助功能
description: 本文档提供的辅助功能的应用创建的各种提示。 例如，它包括有关大字体、 高对比度、 自我描述的接口和的详细信息的建议。
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0ec264e0f3d381fdac46c79dd479da2bc768954f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282184"
---
# <a name="accessibility-in-xamarin-apps"></a>Xamarin 应用中的辅助功能

_确保您的应用程序的广泛的受众_

可访问性是指设计很好操作系统显示和输入协助功能，如大型类型，高对比度，放大的应用程序用户界面、 屏幕阅读功能 （文字到语音）、 visual 或 haptic 反馈提示的概念和其他输入的法。

如 iOS、 Android 和 Windows 桌面和移动平台提供了内置的 Api 可帮助开发人员构建可访问的应用，例如[Google TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)并[Apple 的 VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)。

## <a name="platform-specific-apis"></a>特定于平台的 Api

若要实现本文档中的指导原则，使用每个平台提供的 Api:

- [**Android 可访问性**](~/android/app-fundamentals/accessibility.md)
- [**iOS 可访问性**](~/ios/app-fundamentals/accessibility.md)
- [**OS X 辅助功能**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>可访问性清单

请遵循以下提示，以确保最广泛的受众可能可以访问您的应用程序。 请查看[Android 可访问性测试清单](https://developer.android.com/training/accessibility/testing.html)并[Apple 的辅助功能页](http://www.apple.com/accessibility/)有关其他信息。

### <a name="support-large-fonts-and-high-contrast"></a>支持大字体和高对比度

避免进行硬编码的控件尺寸和相反，更喜欢可以调整大小以容纳更大的字体大小的布局。
在高对比度模式，以确保它们是可读中测试的颜色方案。

### <a name="make-the-user-interface-self-describing"></a>使用户界面自描述

标记具有描述性文本和提示与屏幕读取每个平台上的 Api 兼容的应用用户界面的所有元素。

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>确保图像和图标都有一个备用文本说明

应使用辅助性说明标记图像和图标的应用程序用户界面 （如按钮或指示器的状态，例如） 的一部分。

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>设计与可访问导航记住的可视化树

使用适当的布局控件或 Api，以便使用备选输入的方法的控件之间的导航遵循与使用触摸屏的同一逻辑流。

从屏幕阅读器 （装饰性图像或标签的字段，例如已可访问） 中排除不需要的元素。

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>不要依赖于单独的音频或颜色提示

避免的情况下的进度，完成时或某些其他状态的唯一指示，声音或颜色更改。 可以将设计用户界面包括视觉提示 （使用声音和仅强化的颜色），或添加特定的可访问性指标。

在选择颜色时，尽量避免很难区分用户色盲的调色板。

### <a name="captioning-for-video-text-for-audio"></a>隐藏式字幕的视频、 音频文本

为视频内容和可读的脚本的音频内容提供隐藏式字幕。 该技术还有助于提供调整音频或视频内容的速度的控件，并确保该卷并播放/暂停按钮可轻松地查找和使用。

### <a name="localize"></a>Localize

可访问性说明可以 （而且应该） 进行本地化的应用程序支持多种语言。



## <a name="related-links"></a>相关链接

- [Android 可访问性](~/android/app-fundamentals/accessibility.md)
- [iOS 可访问性](~/ios/app-fundamentals/accessibility.md)
- [OS X 辅助功能](~/mac/app-fundamentals/accessibility.md)
- [Xamarin.Forms 可访问性](~/xamarin-forms/app-fundamentals/accessibility/index.md)
