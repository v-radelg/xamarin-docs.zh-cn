---
title: 如何解决 PathTooLongException 错误？
description: 此文章介绍了如何解决 PathTooLongException 生成的应用程序时可能发生的。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 443c3cc742ceb919e64a781e18c5a97c342abb44
ms.sourcegitcommit: 450106d5f05b4473bf7f5b9100b2eaf18c9110de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67522921"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何解决 PathTooLongException 错误？

## <a name="cause"></a>原因

在 Xamarin.Android 项目中生成的路径名称可能会相当长。
例如，可以在生成期间生成如下所示的路径：

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

在 Windows 上 (其中的路径的最大长度是[260 个字符](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx))、 一个**PathTooLongException**无法生成时生成的项目，如果生成的路径超过了最大长度。 

## <a name="fix"></a>修补程序

`UseShortFileNames` MSBuild 属性设置为`True`默认情况下绕过此错误。 当此属性设置为`True`，生成过程将使用较短的路径名称以减少生成的可能性**PathTooLongException**。
例如，当`UseShortFileNames`设置为`True`，在以上路径缩短为类似于以下路径：

**C:\\某些\\目录\\解决方案\\项目\\obj\\调试\\lp\\1\\jl\\资产**

若要手动设置此属性，请将以下 MSBuild 属性添加到项目 **.csproj**文件：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如果设置此标志不能解决**PathTooLongException**错误，另一种方法是指定[常见的中间输出根](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/)通过设置你的解决方案中的项目`IntermediateOutputPath`中项目 **.csproj**文件。 尝试使用相对较短的路径。 例如：

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

有关设置生成属性的详细信息，请参阅[Build 过程](~/android/deploy-test/building-apps/build-process.md)。
