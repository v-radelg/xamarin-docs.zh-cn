---
title: Android 生成错误 – LinkAssemblies 任务意外失败
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 552ad4d2de6e2560dc4301a9b5cc0ce6a5edb348
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197104"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Android 生成错误 – LinkAssemblies 任务意外失败

可能会看到一条错误消息`The "LinkAssemblies" task failed unexpectedly`时生成 Xamarin.Android 项目使用的窗体。 链接器处于活动状态时将发生这种情况 (通常在*版本*生成以减少应用包的大小); 以及执行因为 Android 目标不会更新到最新的 framework。 (详细信息：[适用于 Android 的要求的 Xamarin.Forms](~/get-started/requirements.md#android))

此问题的解决方法是确保具有支持最新 Android SDK 版本，并设置**目标框架**到**使用最新安装的平台**。 此外建议您设置**目标 Android 版本**到**使用目标框架版本**并**最低 Android 版本**为 API 15 或更高版本。 这被视为受支持的配置。

## <a name="setting-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 的设置

1.  右键单击 Android 项目。
2.  转到**生成 > 常规 > 目标 Framework**。
3.  设置**框架为目标：使用最新安装的平台**。
4.  仍在项目选项，请转到**生成 > Android 应用程序**。
5.  设置**最低 Android 版本**为 15 个或更高版本的 API 级别 &**目标 Android 版本**到**自动-使用目标框架版本**。

## <a name="setting-in-visual-studio"></a>在 Visual Studio 中的设置

1.  右键单击 Android 项目。
2.  转到**应用程序**项目选项中。
3.  设置**使用 Android 版本编译** & **目标 Android 版本**设置为**使用最新的平台** / **使用使用 SDK 版本编译**。
4.  设置**的最低 Android 目标**设置为 API 19 或更高。

更新这些设置之后，请清除并重新生成项目以确保所做的更改会选取。
