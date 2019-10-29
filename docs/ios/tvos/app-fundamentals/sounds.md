---
title: 在 Xamarin 中通过 Avaudioplayer 播放声音在 tvOS 中播放声音
description: 本文介绍如何使用 helper 类在 Xamarin iOS 应用程序中使用 Avaudioplayer 播放声音控制声音播放。
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 4dddde8d4408df6a9b9d73c0a3efff62f563591a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030777"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>在 Xamarin 中通过 Avaudioplayer 播放声音在 tvOS 中播放声音

## <a name="about-the-avaudioplayer"></a>关于 Avaudioplayer 播放声音

`AVAudioPlayer` 用于播放内存或文件中的音频数据。 Apple 建议使用此类在应用程序中播放音频，除非要进行网络流式处理或需要低延迟音频 i/o。

可以使用 `AVAudioPlayer` 执行以下操作：

- 播放带有可选循环的任何持续时间的声音。
- 通过可选同步，同时播放多个声音。
- 每个声音播放的控制量、播放速率和立体声位置。
- 支持功能，如快进或倒带。
- 获取播放级别计数数据。

`AVAudioPlayer` 支持以任意音频格式提供的声音，如 `.aif`、`.wav` 或 `.mp3`。

## <a name="playing-sounds-in-tvos"></a>在 tvOS 中播放声音

由于 tvOS 支持与 iOS 相同的音频工具箱类，请参阅 Avaudioplayer 播放声音文档中的 iOS[播放声音](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer)，详细了解如何在 tvOS 应用中播放音频。

## <a name="related-links"></a>相关链接

- [Avaudioplayer 播放声音引用](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
