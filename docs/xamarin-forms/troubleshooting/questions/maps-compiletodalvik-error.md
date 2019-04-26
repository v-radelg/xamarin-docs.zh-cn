---
title: 为何 Xamarin.Forms.Maps Android 项目失败并 COMPILETODALVIK 意外顶级错误？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250424"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>为何 Xamarin.Forms.Maps Android 项目失败并 COMPILETODALVIK 意外顶级错误？

For Mac 或 Visual Studio; 在生成输出窗口中，可能会在 Visual Studio 的错误面板中出现此错误在使用 Xamarin.Forms.Maps Android 项目中。

这通常通过增加你的 Xamarin.Android 项目的 Java 堆大小来解决。 请执行以下步骤来增加堆大小：

## <a name="visual-studio"></a>Visual Studio

1. 右键单击 Android 项目，并打开项目选项。
2. 转到 **-> Android 选项高级**
3. 在 Java 堆大小文本框中输入 1g。
4. 重新生成项目。

![Visual Studio 项目选项的屏幕截图](maps-compiletodalvik-error-images/vsjavaheap.png "Android 生成 Visual Studio 中的选项")

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1.  右键单击 Android 项目，并打开项目选项。
2.  转到**生成-> Android 生成-> 高级**
3.  在 Java 堆大小文本框中输入 1g。
4.  重新生成项目。  

![Visual Studio for Mac 项目选项的屏幕截图](maps-compiletodalvik-error-images/xsjavaheap.png "Android 生成选项在 Visual Studio for Mac")

