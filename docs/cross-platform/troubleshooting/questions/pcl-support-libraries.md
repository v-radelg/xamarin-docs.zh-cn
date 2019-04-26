---
title: 如何查看 PCL 中支持的库？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: asb3993
ms.author: amburns
ms.date: 07/27/2018
ms.openlocfilehash: 7e1017baf7daed68b5e55319a9ce13a4a2df5f2e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341260"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>如何查看 PCL 中支持的库？

- 您可以找到下的各种 PCL 目标平台支持的各种功能的概述*支持的功能*此页的一部分： [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- 另一种方法是使用[.NET 可移植性分析器](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b)以评估是否在现有库可以转换为 PCL 配置文件。

- 第三种可能性是浏览可能使用的实际配置文件的内容。 例如，使用配置文件 78，您可能会转到此处：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` 并查看在其中的所有程序集。

无论哪种方法选择，请注意，某些功能必须通过 NuGet 和 Microsoft BCL 库下载。
