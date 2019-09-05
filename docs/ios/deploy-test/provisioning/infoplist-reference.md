---
title: Xamarin.iOS 的 Info.plist 引用
description: 本文档介绍可以在 Xamarin.iOS 应用程序的 Info.plist 文件中设置的各种键/值对。 应用执行特定任务（例如访问位置、照片、麦克风或照相机）时，这些键是必需的。
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 01/18/2017
ms.openlocfilehash: 9b766677c10a06b933504f3fdcba0b43e2c84a1c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284391"
---
# <a name="infoplist-reference-for-xamarinios"></a>Xamarin.iOS 的 Info.plist 引用

有关使用 Info.Plist 键的详细信息，请参阅[保护安全和隐私](~/ios/app-fundamentals/security-privacy.md)指南。 

## <a name="location"></a>位置 

访问用户位置也需要对 Info.plist 进行修改。 应设置如下与位置数据相关的键： 

- **NSLocationWhenInUseUsageDescription** - 用于用户在和你的应用互动时访问用户的位置。 
- **NSLocationAlwaysUsageDescription** - 用于应用在后台访问用户的位置时。

## <a name="photos"></a>照片 

NSPhotoLibraryUsageDescription  

## <a name="contacts"></a>联系人 

NSContactsUsageDescription 

## <a name="calendar-data"></a>日历数据 
    
NSCalendarsUsageDescription 

## <a name="reminder-data"></a>提醒数据 
    
NSRemindersUsageDescription 

## <a name="bluetooth-peripherals"></a>蓝牙外围设备 
    
NSBluetoothPeripheralUsageDescription 

## <a name="microphone"></a>麦克风 

NSMicrophoneUsageDescription 

## <a name="camera"></a>照相机 
    
NSCameraUsageDescription 

## <a name="reading-healthkit"></a>读取 HealthKit  

NSHealthShareUsageDescription 

NSHealthUpdateUsageDescription 

## <a name="background-modes"></a>后台模式 
    
UIBackgroundModes 

## <a name="homekit"></a>HomeKit 

NSHomeKitUsageDescription 


## <a name="related-links"></a>相关链接

- [Apple 参考指南。](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW10)
