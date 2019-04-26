---
title: 在 Xamarin.Mac 中播放声音使用 AVAudioPlayer
description: 本文档介绍如何播放声音使用 AVAudioPlayer Xamarin.Mac 应用中。 它讨论了在高级别和其他文档的更全面探讨链接 AVAudioPlayer。
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 10/19/2016
ms.openlocfilehash: 9aeb7bbfc2fddef1f690b5299ec060c475ea1ce7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61234838"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>在 Xamarin.Mac 中播放声音使用 AVAudioPlayer

## <a name="about-the-avaudioplayer"></a>有关 AVAudioPlayer

`AVAudioPlayer`类用于播放音频数据从内存或文件。 Apple 建议使用此类播放音频，在您的应用程序，除非正在流式处理的网络或需要较低的延迟音频 I/O。

可以使用`AVAudioPlayer`类来执行以下操作：

- 播放声音的任何可选循环的持续时间。
- 可选同步同时播放多个声音。
- 控制音量、 播放速度和立体声定位的每个声音播放的声音。
- 支持快进或快退等功能。
- 获取播放计数数据的级别。

`AVAudioPlayer` 支持 iOS、 tvOS 和 macOS 如.aif、.wav 或.mp3 提供任何音频格式的声音。

## <a name="playing-sounds-in-macos"></a>在 macOS 中播放声音

由于 macOS 支持相同的音频工具箱类，因为 iOS，请参阅我们的 iOS[使用 AVAudioPlayer 播放声音](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer)Xamarin.Mac 应用中播放音频的完整详细信息的文档。

## <a name="related-links"></a>相关链接

- [AVAudioPlayer 引用](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
