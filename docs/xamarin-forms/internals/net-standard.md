---
title: Xamarin 中的 .NET Standard 2.0 支持。 Forms
description: 本文介绍如何将 Xamarin 应用程序转换为使用 .NET Standard 2.0。 .NET Standard 是一种适用于所有 .NET 实现的 .NET Api 规范。
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: eaef607e3e6095ccc7dfb1f5831d2384d11cab5f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760060"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Xamarin 中的 .NET Standard 2.0 支持。 Forms

_本文介绍如何将 Xamarin 应用程序转换为使用 .NET Standard 2.0。_

.NET Standard 是一种适用于所有 .NET 实现的 .NET Api 规范。 通过将相同的 Api 引入到不同的平台，可以更轻松地跨桌面应用程序、移动应用程序和游戏以及云服务共享代码。 有关 .NET Standard 支持的平台的信息，请参阅[.net 实现支持](/dotnet/standard/net-standard#net-implementation-support)。

.NET Standard 库是可移植类库（PCL）的替换。 但是，面向 .NET Standard 的库仍为 PCL，并称为基于 .NET Standard 的 PCL。 某些 PCL 配置文件映射到 .NET Standard 版本，对于具有映射的配置文件，这两个库类型将能够彼此引用。 有关详细信息，请参阅[PCL 兼容性](/dotnet/standard/net-standard#pcl-compatibility)。

Xamarin 2.4 通过将 PCL 替换为 .NET Standard 2.0 库来允许 Xamarin 应用程序以 .NET Standard 2.0 为目标。 可按如下所示实现此目的：

- 确保[.NET Core 2.0](https://www.microsoft.com/net/download/core)安装。
- 更新 Xamarin 解决方案以使用 Xamarin. Forms 2.4 或更高版本。
- 将 .NET Standard 库添加到解决方案，目标 .NET Standard 为2.0。
- 删除添加到 .NET Standard 库中的类。
- 将 Xamarin 2.4 （或更高版本） NuGet 包添加到 .NET Standard 库。
- 在平台项目中，添加对 .NET Standard 库的引用，并删除对包含 Xamarin. Forms 用户界面逻辑的 PCL 项目的引用。
- 将文件从 PCL 项目复制到 .NET Standard 库。
- 删除包含 Xamarin 用户界面逻辑的 PCL 项目。

## <a name="related-links"></a>相关链接

- [.NET 标准](~/cross-platform/app-fundamentals/net-standard.md)
- [代码共享选项](~/cross-platform/app-fundamentals/code-sharing.md)
