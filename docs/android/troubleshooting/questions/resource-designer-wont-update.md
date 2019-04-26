---
title: Android Resource.designer.cs 文件不更新
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: ba3c2b07e7f35bf9fd84d10b74d034a02ca6a73d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153278"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Android Resource.designer.cs 文件不更新

> [!NOTE]
> 在 Xamarin Studio 5.1.4 和更高版本中解决此问题。 但是，如果 Visual Studio for Mac 发生问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。

在 Xamarin.Studio 5.1 中的 bug 以前通过部分或完全删除.csproj 文件中的 xml 代码损坏.csproj 文件。 这将导致重要部分的 Android 生成系统 （例如，更新 Android Resource.designer.cs） 失败。 截至 5.1.4 稳定版本在年 7 月 15 日，已修复此错误;但在许多情况下的项目文件按下面所述手动修复。


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>正在修复项目文件的两种可能方法

### <a name="either"></a>二者之一：

1) 创建一个全新的 Xamarin.Android 应用程序项目，将设置所有项目属性以匹配旧的项目，并添加到项目的所有资源、 源文件、 等。

### <a name="or"></a>或

2) 创建原始项目的.csproj 文件的备份副本，然后在文本编辑器中，将其打开，并从完全生成的.csproj 文件中添加缺少的元素中返回。

### <a name="if-this-does-not-solve-the-problem"></a>如果这没有解决问题

与这些元素一起进行实验之后, 您会注意到，之后将其添加回元素和重新生成项目，将更新 Resource.designer.cs 文件，但然后你可能仍需要关闭并重新打开解决方案以获取代码完成功能来识别Resource.designer.cs 中包含的新类型。 
