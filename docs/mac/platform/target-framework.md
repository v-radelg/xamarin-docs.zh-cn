---
title: 适用于 Xamarin.Mac 的目标框架
description: 本文介绍如何适用于 Xamarin.Mac，可用的目标框架 （基类库） 和在 Xamarin.Mac 项目中使用它们的含义。
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: e9e20b4966e9e6cb8a4ce3ad6724cf0ba2565c33
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865866"
---
# <a name="target-framework-for-xamarinmac"></a>适用于 Xamarin.Mac 的目标框架

_本文介绍如何适用于 Xamarin.Mac，可用的目标框架 （基类库） 和在 Xamarin.Mac 项目中使用它们的含义。_

![Target framework 选项适用于 Xamarin.Mac](target-framework-images/select-target.png "Target framework 选项适用于 Xamarin.Mac")

## <a name="background"></a>背景

每个.NET 程序或库依赖于提供的基类库 (BCL) 的功能。 此 BCL 包括如 mscorlib、 系统、 System.Net.Http 和 System.Xml 程序集提供通用功能内置于所有.NET 语言。

多年来，又那里已开发此 BCL，针对不同的用例优化的多个不同版本。 "桌面"BCL 包括一组更丰富的库，它可能太适合其他使用情况，尽管移动着重介绍确保 Api 安全的链接，这将删除未使用的代码，以减少应用程序占用空间。

这些不同的目标框架，更重要的负面影响之一是，在给定的程序中的程序集的所有*必须*目标兼容的 BCL 程序集。 如果这不是这种情况，您可以针对不同版本的链接的两个程序集**System.dll** disagreeing 有关给定类型的签名。 共享的库可以任一目标[.NET 标准 2](https://blog.xamarin.com/share-code-net-standard-2-0/)，这是常见的目标框架中或特定目标框架子集。

有三个目标框架选项适用于 Xamarin.Mac，每个都有不同的优点和缺点：

- **现代**（较旧的文档中称为移动 –） 非常类似哪些乘方 Xamarin.iOS，高度优化的性能和大小的子集。 此目标框架是链接器安全的因此，这些项目可以通过删除未使用的代码极大地减少了其最终占用空间。

- **完整**（较旧的文档中称为 XM 4.5） –"桌面"bcl，使用几个小删除非常相似的子集。 由于目标框架是几乎完全相同，到 net45 （及更高版本），它可以轻松地使用多个 nuget 无法提供的任一 netstandard2 或特定的 Xamarin.Mac 版本。 但是，由于 System.Configuration 用法是与链接不兼容。

- **不支持**（称为系统较旧的文档中）-而是将链接到提供的 Xamarin.Mac 对 BCL，使用当前系统安装 mono。 这提供了最大组程序集，包括一些已知问题 (例如 System.Drawing)。 此选项仅存在具有"最后手段"并且强烈建议使用它之前耗尽其他选项。 正如名称所示，使用情况不受正式支持渠道。

## <a name="setting-the-target-framework"></a>设置目标框架

若要更改为 Xamarin.Mac 项目的目标框架类型，请执行以下操作：

1. 在 Visual Studio for Mac 中打开 Xamarin.Mac 项目。
2. 在“解决方案资源管理器”   中，双击项目文件打开“项目选项”对话框。
3. 从**常规**选项卡上，选择的类型**目标框架**适合应用程序的需求：

    [![使用项目选项窗口中选择目标框架](target-framework-images/select-target-full.png "使用项目选项窗口中选择目标 framework")](target-framework-images/select-target-full-large.png#lightbox)

4. 单击“确定”  按钮保存更改。

您应该**清理**，然后**重新生成**切换的目标框架类型之后在 Xamarin.Mac 项目。

## <a name="summary"></a>总结

本文简要介绍不同类型的目标框架 （基类库） 提供给 Xamarin.Mac 应用程序，并且应使用每种类型的框架。


## <a name="related-links"></a>相关链接

- [iOS 和 Mac 代码共享](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [可移植类库](~/cross-platform/app-fundamentals/pcl.md)
- [程序集](~/cross-platform/internals/available-assemblies.md)
- [更新现有 Mac 应用程序](~/cross-platform/macios/unified/updating-mac-apps.md)
