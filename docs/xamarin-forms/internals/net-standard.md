---
title: 在 Xamarin.Forms 中的.NET standard 2.0 支持
description: 本文介绍如何将转换为使用.NET Standard 2.0 的 Xamarin.Forms 应用程序。 .NET standard 是旨在所有.NET 实现上可用的.NET Api 的规范。
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c3e46592bb8760ff85eaeb5dce119897a97dfe89
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667849"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>在 Xamarin.Forms 中的.NET standard 2.0 支持

_本文介绍如何将转换为使用.NET Standard 2.0 的 Xamarin.Forms 应用程序。_

.NET standard 是旨在所有.NET 实现上可用的.NET Api 的规范。 它可以更轻松地在桌面应用程序、 移动应用和游戏，间共享代码和云服务，通过将相同的 Api 引入不同的平台。 有关受支持的.NET Standard 平台的信息，请参阅[.NET 实现支持](/dotnet/standard/net-standard#net-implementation-support)。

.NET standard 库都替换为可移植类库 (PCL)。 但是，面向.NET Standard 的库仍是 PCL，并且被称为基于.NET Standard 的 PCL。 某些 PCL 配置文件映射到.NET Standard 版本中，并且对于具有映射的配置文件，两个库类型将能够相互引用。 有关详细信息，请参阅[PCL 兼容性](/dotnet/standard/net-standard#pcl-compatibility)。

Xamarin.Forms 2.4 允许通过在 PCL 中替换为.NET Standard 2.0 库到目标.NET Standard 2.0 的 Xamarin.Forms 应用程序。 可以按如下所示实现此目的：

- 确保[.NET Core 2.0](https://www.microsoft.com/net/download/core)安装。
- 更新 Xamarin.Forms 解决方案，以使用 Xamarin.Forms 2.4，或更高版本。
- .NET Standard 库添加到解决方案，面向.NET Standard 2.0。
- 删除添加到.NET Standard 库的类。
- 将 Xamarin.Forms 2.4 （或更高版本） NuGet 包添加到.NET Standard 库。
- 在平台项目中，添加对.NET Standard 库的引用并删除对包含 Xamarin.Forms 用户界面逻辑的 PCL 项目的引用。
- PCL 项目中的文件复制到.NET Standard 库中。
- 删除包含 Xamarin.Forms 用户界面逻辑的 PCL 项目。


## <a name="related-links"></a>相关链接

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [代码共享选项](~/cross-platform/app-fundamentals/code-sharing.md)
