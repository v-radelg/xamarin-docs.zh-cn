---
title: Android Resource.designer.cs 文件不更新
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/19/2017
ms.openlocfilehash: 4683bbaa5aa48c7b5de5fb9a87a4cd3fbc0aeada
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019515"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Android Resource.designer.cs 文件不更新

> [!NOTE]
> 此问题已在 Xamarin Studio 5.1.4 及更高版本中得到解决。 但是，如果 Visual Studio for Mac 中出现问题，请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

Xamarin 5.1 以前损坏的 .csproj 文件中的 bug，通过部分或完全删除 .csproj 文件中的 xml 代码。 这会导致 Android 生成系统（如更新 Android Resource.designer.cs）的重要部分失败。 到7月15日的5.1.4 稳定版本，此 bug 已修复;但在许多情况下，必须手动修复项目文件，如下所述。

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>解决项目文件的两种可能方法

**是**

1. 创建全新的 Xamarin Android 应用程序项目，将所有项目属性设置为与旧项目匹配，并将所有资源、源文件等添加回项目。

   **或**

2. 创建原始项目的 .csproj 文件的备份副本，然后在文本编辑器中将其打开，并从完全生成的 .csproj 文件中重新添加回缺少的元素。

### <a name="if-this-does-not-solve-the-problem"></a>如果这不能解决问题

在试验这些元素后，你可能会注意到，在添加完元素并重新生成项目后，Resource.designer.cs 文件将会更新，但你仍可能需要关闭并重新打开解决方案以使代码完成识别Resource.designer.cs 中包含的新类型。 
