---
title: 在更新 Nuget 包后出现缺失包错误
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 49f0e57af1ad6b6e0f322eb9865ba99db4dd47ec
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199899"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>在更新 Nuget 包后出现缺失包错误

此问题主要在 Xamarin. Forms 示例应用解决方案上进行报告, 但对于使用 NuGet 包的任何项目, 可能会发生此问题。

如果在项目或解决方案中更新 Nuget 包后, 将看到一个错误, 该错误引用旧的包版本号, 例如:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

在此示例中, *1.3.1.6296*是与 Nuget 包更新一起删除的旧版本号。

如果已手动添加或编辑了引用旧包版本号的 .csproj 文件中的 XML 元素, 则 Nuget 不会删除或更新这些元素, 因此, 该项目现在正在查找已创建的包删除.

若要解决此问题, 请手动编辑 .csproj 文件, 并删除引用旧版本号的所有元素。

要删除的示例元素 (如果它们具有旧的包版本号):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```
