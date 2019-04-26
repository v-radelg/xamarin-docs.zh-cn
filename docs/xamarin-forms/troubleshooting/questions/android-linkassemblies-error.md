---
title: Android 生成错误 – LinkAssemblies 任务意外失败
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: f517aaa770fa7b2f1463954638f0afc95168bf65
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250733"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Android 生成错误 – LinkAssemblies 任务意外失败

可能会看到一条错误消息`The "LinkAssemblies" task failed unexpectedly`时生成 Xamarin.Android 项目使用的窗体。 链接器处于活动状态时将发生这种情况 (通常在*版本*生成以减少应用包的大小); 以及执行因为 Android 目标不会更新到最新的 framework。 (详细信息：[适用于 Android 的要求的 Xamarin.Forms](~/get-started/requirements.md#android))

此问题的解决方法是确保具有支持最新 Android SDK 版本，并设置**目标框架**到已安装最新的平台。 此外建议您设置**目标 Android 版本**到最新的已安装平台，并**最低 Android 版本**为 API 19 或更高。 这被视为受支持的配置。

## <a name="setting-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 的设置

1.  Android 项目中，右键单击并选择**选项**菜单中。
2.  在中**项目选项**对话框中，转到**生成 > 常规**。
3.  设置**使用 Android 版本编译：（目标框架）** 到已安装最新的平台。
4.  在中**项目选项**对话框中，转到**生成 > Android 应用程序**。
5.  设置**最低 Android 版本**为 API 级别 19 或更高版本，并**目标 Android 版本**到已安装最新的平台 (3) 中选择。

## <a name="setting-in-visual-studio"></a>在 Visual Studio 中的设置

1.  Android 项目中，右键单击并选择**属性**菜单中。
2.  在项目属性中，转到**应用程序**。
3.  设置**使用 Android 版本编译：（目标框架）** 到已安装最新的平台。
4.  在项目属性中，转到**Android 清单**。
5.  设置**最低 Android 版本**为 API 级别 19 或更高版本，并**目标 Android 版本**到已安装最新的平台 (3) 中选择。

更新这些设置之后，请清除并重新生成项目以确保所做的更改会选取。
