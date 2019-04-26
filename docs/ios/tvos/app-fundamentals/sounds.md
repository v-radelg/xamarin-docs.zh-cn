---
title: 使用 AVAudioPlayer 在 Xamarin 中的 tvOS 中播放声音
description: 本文介绍如何使用一个帮助器类控制的声音 Xamarin.iOS 应用程序中使用 AVAudioPlayer 播放。
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: bc1e9febea9529c3fadbacd689404562952276fe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61414095"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>使用 AVAudioPlayer 在 Xamarin 中的 tvOS 中播放声音

## <a name="about-the-avaudioplayer"></a>有关 AVAudioPlayer

`AVAudioPlayer`是用于播放音频数据从内存或文件。 Apple 建议使用此类播放音频，在您的应用程序，除非正在流式处理的网络或需要较低的延迟音频 I/O。

可以使用`AVAudioPlayer`来执行以下操作：

- 播放声音的任何可选循环的持续时间。
- 可选同步同时播放多个声音。
- 控制音量、 播放速度和立体声定位的每个声音播放的声音。
- 支持快进或快退等功能。
- 获取播放计数数据的级别。

`AVAudioPlayer` 支持如提供 iOS、 tvOS 和 OS X 的任何音频格式的声音`.aif`，`.wav`或`.mp3`。

## <a name="playing-sounds-in-tvos"></a>TvOS 中播放声音

由于 tvOS 支持相同的音频工具箱类，因为 iOS，请参阅我们的 iOS[使用 AVAudioPlayer 播放声音](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer)Xamarin.tvOS 应用中播放音频的完整详细信息的文档。



## <a name="related-links"></a>相关链接

- [AVAudioPlayer 引用](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
