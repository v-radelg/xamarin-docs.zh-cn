---
title: 手动为 Xamarin 创建 NuGet 包
description: 本文档包含提示以帮助您构建面向 Xamarin 平台的 NuGet 包。 它描述 NuGet 包 Xamarin 配置文件，PCL Nuget 平台依赖项，并链接到各种开源的示例。
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 2f66d8a3960741643013a1010162f52d283026d6
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855711"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>手动为 Xamarin 创建 NuGet 包

_此页面包含一些提示，以帮助您构建面向 Xamarin 平台的 NuGet 包。_

> [!NOTE]
> Xamarin Studio 6.2 （和 Visual Studio for Mac） 包括以下功能_自动_从 PCL、.NET Standard，或共享的项目生成 NuGet 包。 请参阅[用于代码共享的多平台库](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)更多详细信息的指南。

## <a name="nuget-package-xamarin-profiles"></a>NuGet 包 Xamarin 配置文件

NuGet 网站[支持多个.NET Framework 版本和配置文件](https://docs.nuget.org/create/enforced-package-conventions)讨论如何支持其他 Microsoft 框架与配置文件，但不包括使用 Xamarin 的目标框架名称。

目前正在使用的主要 Xamarin 目标框架：

* **MonoAndroid** - Xamarin.Android
* **Xamarin.iOS** -Xamarin.iOS [Unified API](~/cross-platform/macios/unified/index.md) （支持 64 位）
* **Xamarin.Mac** -Xamarin.Mac 的移动配置文件，它相当于 Xamarin.iOS 和 Xamarin.Android API 图面。

此外，还有针对较旧 iOS[经典 API](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -iOS 经典 API

一个 **.nuspec**针对所有这些文件将如下所示：

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

上述将忽略任何可移植类库。

大多数 **.nuspec**文件指定的目标框架的版本号，但它是可选的如果您的程序集适用于所有版本的目标框架。 因此，如果你的目标已**lib\MonoAndroid**这就意味着它适用于任何版本的 Xamarin.Android。

可以使用一组不带小数点的数字指定的版本，也可以指定它使用小数点。 而无需小数点 NuGet 将只需采取的每个数字并将其转换为一个版本的方法是插入。 每个数字之间。

在上述"MonoAndroid10"意味着"Android 1.0"。 这只是意味着项目的[目标框架](~/android/app-fundamentals/android-api-levels.md)需要 MonoAndroid 1.0 或更高版本。 在指定的版本`<TargetFrameworkVersion>`项目文件中的元素。

若要阐明：

- **MonoAndroid403**匹配 Android 4.0.3 和更高版本 （即 API 级别 15）
- **Xamarin.iOS10**匹配 Xamarin.iOS 1.0 和更高版本
- **Xamarin.iOS1.0**也匹配 Xamarin.iOS 1.0 和更高版本

## <a name="pcl-nugets-with-platform-dependencies"></a>PCL 平台依赖项的 Nuget

PCL 配置文件的哪些.NET framework Api，他们可以访问将受到限制，他们肯定不能访问特定于平台的代码。 这些第三方链接讨论了创建使用 PCL 和本机 Api 提供的 Xamarin 和其他平台兼容性的 NuGet 包的不同方法：

- [如何使可移植库工作](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [Bait 和 Switch PCL 技巧](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [创建 NuGet PCL 适用于 Xamarin.iOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

此外部[PCL 配置文件的 NuGet 目标名称的列表](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY)也是一个有用的参考。

## <a name="examples"></a>示例

可以参阅一些开放源代码示例：

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) – 编写应用程序使用 System.Net.Http，但删除此库，它将会极大地更快 (视图[源](https://github.com/paulcbetts/ModernHttpClient))。
- [**Splat** ](https://www.nuget.org/packages/Splat/) – 一个库来使跨平台的操作应 (视图[源](https://github.com/paulcbetts/Splat))。
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -用于呈现矢量图形的.NET 的跨平台库 (视图[源](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec))。

## <a name="related-links"></a>相关链接

- [Nugetizer 3000 自动 Nuget 创建](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
