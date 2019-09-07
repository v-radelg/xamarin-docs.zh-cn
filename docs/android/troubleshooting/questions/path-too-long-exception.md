---
title: 如何实现解决 PathTooLongException 错误？
description: 本文介绍如何解决在构建应用程序时可能出现的 PathTooLongException。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 915f557db7955dc7b8b9f1bc5e014a683740052b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760813"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何实现解决 PathTooLongException 错误？

## <a name="cause"></a>原因

Xamarin Android 项目中生成的路径名称可能相当长。
例如，可能会在生成过程中生成如下所示的路径：

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

在 Windows 上（路径的最大长度为[260 个字符](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)），生成项目时，如果生成的路径超过了最大长度，则可能会生成**PathTooLongException** 。 

## <a name="fix"></a>修补程序

MSBuild 属性设置为`True` ，以在默认情况下避开此错误。 `UseShortFileNames` 如果将此属性设置为`True`，则生成过程将使用较短的路径名称，以降低生成**PathTooLongException**的可能性。
例如，当设置`UseShortFileNames`为`True`时，上述路径将缩短为类似于以下内容的路径：

**C：\\某些\\目录\\解决方案项目\\obj\\Debuglp\\1jl资产\\\\\\\\**

若要手动设置此属性，请将以下 MSBuild 属性添加到项目 **.csproj**文件：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如果设置此标志不会修复**PathTooLongException**错误，另一种方法是通过在项目 **.csproj**文件中设置`IntermediateOutputPath` ，为解决方案中的项目指定[公共中间输出根](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/)。 尝试使用相对较短的路径。 例如:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

有关设置生成属性的详细信息，请参阅[生成过程](~/android/deploy-test/building-apps/build-process.md)。
