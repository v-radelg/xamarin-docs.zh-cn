---
title: 应安装哪些 Android SDK 包？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2018
ms.openlocfilehash: 24c70c2e869f59091a1519af6d1165dbea9cc467
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725058"
---
# <a name="which-android-sdk-packages-should-i-install"></a>应安装哪些 Android SDK 包？

安装 Android SDK 不会自动包括开发所需的所有最低包。 尽管各个开发人员的需求各不相同，但通常需要以下包才能用 Xamarin 进行开发：

## <a name="tools"></a>工具

从 SDK 管理器的 "工具" 文件夹中安装最新的工具：

- Android SDK 工具
- Android SDK 平台-工具
- Android SDK 生成工具

## <a name="android-platforms"></a>Android 平台

为已设置为最小 & 目标的 Android 版本安装 "SDK 平台"。

例如：

- 目标 API 23
- 最小 API 23

仅需安装适用于 API 23 的 SDK 平台

- 目标 API 23
- 最小 API 15

需要安装 API 15 和23的 SDK 平台。 请注意，不需要在最小值和目标值之间安装 API 级别（即使你 backporting 了这些 API 级别）。

## <a name="system-images"></a>系统映像

仅当你想要使用来自 Google 的现成 Android 模拟器时，才需要这些属性。 有关详细信息，请参阅[Android Emulator 安装程序](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>附送项目
通常不需要进行 Android SDK 额外的工作，但请注意，这可能是必需的，因为它们可能是必需的，具体取决于使用情况。
