---
title: 编辑 NuGet 元数据
description: 本文档介绍如何使用项目选项编辑的多平台库的 NuGet 元数据。 它讨论了必需和可选元数据。
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3680b02003a844668b0b5c97e5d4c0d296ae3500
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266852"
---
# <a name="editing-nuget-metadata"></a>编辑 NuGet 元数据

_使用项目选项编辑的多平台库的 NuGet 元数据_

库项目类型 （如 PCL 或.NET Standard，或新的 NuGet 项目类型） 具有**NuGet 包**主题中**项目选项**窗口。

**元数据**部分将配置中使用的值[ **.nuspec** NuGet 包清单文件](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file)。

## <a name="required-information"></a>所需的信息

**常规**选项卡包含生成 NuGet 包，必须输入的四个字段：

[![](metadata-images/metadata-general-sml.png "NuGet 包所需的元数据窗口")](metadata-images/metadata-general.png#lightbox)

- **ID** – 包标识符，在 Nuget.org （或将分发包的任何位置） 中应该唯一。 按照这[指南](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)和仅使用有效的 URL 中的字符 (不含空格，并避免大部分特殊字符)。
- **版本**– 选择与一致的版本号[NuGet 的版本控制规则](https://docs.microsoft.com/nuget/create-packages/dependency-versions)。
- **作者**– 以逗号分隔列表的名称。
- **说明**– 当用户选择包时显示该程序包的功能的概述。

> [!NOTE]
> 请记住生成分发给 NuGet 或其他用户的新版本时递增的版本号。

有关详细信息，请参阅[所需元素引用](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements)有关详细信息，以及根据这些上详细说明[选择唯一包标识符并设置版本号](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)并[设置包类型](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type)。

> [!IMPORTANT]
> 必须输入此选项卡上的所有字段;否则，会显示一条错误消息：_"项目没有 NuGet 元数据以便不会创建 NuGet 包。项目选项中的元数据部分中，可以指定 NuGet 包元数据"_

## <a name="optional-metadata"></a>可选元数据

**详细信息**选项卡包含要包括在 NuGet 包清单文件中的可选字段。

[![](metadata-images/metadata-detail-sml.png "NuGet 包的可选元数据窗口")](metadata-images/metadata-detail.png#lightbox)

请参阅[可选的元素引用](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements)有关必需和可选字段的详细信息。

> [!NOTE]
> 如果 NuGet 包分发[NuGet.org](https://www.nuget.org)建议提供尽可能多的信息。


## <a name="related-links"></a>相关链接

- [.nuspec 引用](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
