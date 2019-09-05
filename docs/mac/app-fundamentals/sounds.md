---
title: 在 Xamarin 中通过 Avaudioplayer 播放声音播放声音
description: 本文档介绍如何在 Xamarin for Mac 应用中使用 Avaudioplayer 播放声音播放声音。 它从较高的层面讨论了 Avaudioplayer 播放声音，并链接到了更完整地探讨它的其他文档。
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 10/19/2016
ms.openlocfilehash: b4a5ead3e3c02fbdd2ae5486a6ac637defeb5abd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283307"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>在 Xamarin 中通过 Avaudioplayer 播放声音播放声音

## <a name="about-the-avaudioplayer"></a>关于 Avaudioplayer 播放声音

`AVAudioPlayer`类用于从内存或文件播放音频数据。 Apple 建议使用此类在应用程序中播放音频，除非要进行网络流式处理或需要低延迟音频 i/o。

您可以使用`AVAudioPlayer`类来执行以下操作：

- 播放带有可选循环的任何持续时间的声音。
- 通过可选同步，同时播放多个声音。
- 每个声音播放的控制量、播放速率和立体声位置。
- 支持功能，如快进或倒带。
- 获取播放级别计数数据。

`AVAudioPlayer`支持使用 iOS、tvOS 和 macOS 提供的任何音频格式的声音，如 aif、.wav 或 mp3。

## <a name="playing-sounds-in-macos"></a>在 macOS 中播放声音

由于 macOS 支持与 iOS 相同的音频工具箱类，请参阅使用 Avaudioplayer 播放声音文档的 iOS[播放声音](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer)，详细了解如何在 Xamarin 应用中播放音频。

## <a name="related-links"></a>相关链接

- [Avaudioplayer 播放声音引用](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
