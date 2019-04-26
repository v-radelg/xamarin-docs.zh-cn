---
title: 应安装哪些 Android SDK 包？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: 04a07d5b7f37222515136e5592f31a4583b02fe3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157359"
---
# <a name="which-android-sdk-packages-should-i-install"></a>应安装哪些 Android SDK 包？

安装 Android SDK 不会自动包括用于开发的所有最小所需的包。 虽然各开发人员需要不同，通常会使用 Xamarin.Android 进行开发所需的以下包：

## <a name="tools"></a>工具

从 SDK 管理器中的工具文件夹中安装最新的工具：

- Android SDK 工具
- Android SDK 平台工具
- Android SDK 生成工具

## <a name="android-platforms"></a>Android 平台

安装适用于已设置为最小值和目标设置的 Android 版本"平台 SDK"。 

示例：

- 目标 API 23
- 最小 API 23

只需为 API 23 安装 SDK 平台

- 目标 API 23
- 最小 API 15

需要安装适用于 API 15 和 23 的 SDK 平台。 请注意，不需要安装的最小值和目标之间的 API 级别 （即使您是反向移植到这两种 API 级别）。

## <a name="system-images"></a>系统映像

这些只是需要你想要使用来自 Google 的开箱 Android 仿真程序。 有关详细信息，请参阅[Android 仿真程序安装程序](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>附加程序
Android SDK Extras 通常不是必需的;但必须了解它们，因为它们可能需要根据你的用例很有用。

## <a name="further-reading"></a>其他阅读材料
以下指南介绍了这些选项，并进入的详细信息的不同程序包 SDK 管理器具有可用：[Android SDK Manager 安装指南](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

