---
title: Xamarin 的目标框架
description: 本文介绍适用于 Xamarin 的目标框架（基类库），以及在 Xamarin for Mac 项目中使用它们的含义。
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: a612c2c23ceff13ea1d602465573514547628e55
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769807"
---
# <a name="target-framework-for-xamarinmac"></a>Xamarin 的目标框架

_本文介绍适用于 Xamarin 的目标框架（基类库），以及在 Xamarin for Mac 项目中使用它们的含义。_

![Xamarin 的目标框架选项](target-framework-images/select-target.png "Xamarin 的目标框架选项")

## <a name="background"></a>后台

每个 .NET 程序或库都依赖于基类库（BCL）提供的功能。 此 BCL 包含程序集，例如 mscorlib、System、System .Net 和 System.object，它们提供内置于所有 .NET 语言的通用功能。

多年来，开发了多个不同版本的 BCL，针对不同用例进行了优化。 "桌面" BCL 包含一组更丰富的库，这些库可能会对其他用例而言太重型，而移动重点是确保 Api 可安全链接，这会删除未使用的代码以减少应用程序占用空间。

这些不同的目标框架的一个更重要的影响是，指定程序中的所有程序集都*必须*面向兼容的 BCL 程序集。 如果不是这种情况，则可以将两个程序集链接**到 disagreeing 的不同版本，这**是关于给定类型的签名的。 共享库可以针对[.NET Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/)，这是目标框架的公共子集或特定目标框架。

有三个目标框架选项可用于 Xamarin，每个选项具有不同的优点和折衷：

- **新式**（较旧文档中的 "移动"）–针对性能和大小进行了高度优化的功能，这是一个非常类似的部分。 此目标框架是链接器安全的，因此，这些项目可以通过删除未使用的代码来大幅减少最终的内存占用量。

- **完整**（在旧文档中称为 "XM 4.5"）-与 "桌面" BCL 非常相似的子集中，具有几个小删除。 由于目标框架与 net45 （和更高版本）几乎完全相同，因此可以轻松地使用许多不提供 netstandard2 或特定 Xamarin 版本的 nuget。 但是，由于使用的是系统配置，因此它与链接不兼容。

- **不受支持**（在旧文档中称为系统）–使用当前系统安装的 mono，而不是链接到 Xamarin 提供的 BCL。 这将提供最大的程序集，包括一些已知的问题（例如，System.web）。 此选项仅存在 "last 滑雪场"，强烈建议在使用其他选项之前将其排出。 顾名思义，官方支持渠道不支持用法。

## <a name="setting-the-target-framework"></a>设置目标框架

若要更改为 Xamarin Mac 项目的目标框架类型，请执行以下操作：

1. 在 Visual Studio for Mac 中打开 Xamarin.Mac 项目。
2. 在“解决方案资源管理器”中，双击项目文件打开“项目选项”对话框。
3. 从 "**常规**" 选项卡中，选择满足应用程序需求的**目标框架**类型：

    [![使用 "项目选项" 窗口选择目标框架](target-framework-images/select-target-full.png "使用 \"项目选项\" 窗口选择目标框架")](target-framework-images/select-target-full-large.png#lightbox)

4. 单击“确定”按钮保存更改。

在切换目标框架类型后，应**清除**并**重新生成**Xamarin Mac 项目。

## <a name="summary"></a>总结

本文简要介绍了适用于 Xamarin Mac 应用程序的不同类型的目标框架（基类库），以及何时使用每种类型的框架。

## <a name="related-links"></a>相关链接

- [iOS 和 Mac 代码共享](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [可移植类库](~/cross-platform/app-fundamentals/pcl.md)
- [程序集](~/cross-platform/internals/available-assemblies.md)
- [更新现有 Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)
