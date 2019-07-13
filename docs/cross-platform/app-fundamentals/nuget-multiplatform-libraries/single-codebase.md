---
title: 为 NuGet 创建新的多平台库
description: 本文档介绍如何使用 NuGet 创建多平台库为使用。 此方法很适合业务逻辑和算法，可以完全在.NET 基类库中表示，并会因此而无需特定于平台的代码的所有目标平台上运行。
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6371c2af15eab9c5124212eefd9cf70d07b945d4
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864722"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>为 NuGet 创建新的多平台库

创建一个多平台库项目使用 PCL 或.NET Standard，则意味着生成的 NuGet，可以添加到任何支持的目标配置文件，包括 ASP.NET 项目或使用 WinForms、 WPF 或 UWP 的桌面应用程序的.NET 项目。

库只能包含所选的 PCL 或.NET Standard 配置文件，以及添加的任何其他 Nuget 支持的代码。
这被适合于业务逻辑和可以完全在.NET 基类库中表示的算法。

创建一个程序集并将其内置于 NuGet 包。

如果以后需要特定于平台的功能[可以添加特定于平台的项目](#add-platforms)。

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>若要创建多平台库 NuGet 的步骤

1. 选择**文件 > 新的解决方案**(或右键单击现有的解决方案，然后选择**添加 > 新建项目**)。

2. 选择**多平台库**从**多平台 > 库**部分：

   [![](single-codebase-images/mulitplatform-library-sml.png "配置适用于单个代码库的多平台库")](single-codebase-images/mulitplatform-library.png#lightbox)

3. 输入**名称**并**说明**，然后选择**所有平台的单一**:

   [![](single-codebase-images/single-configure-sml.png "配置适用于单个代码库的多平台库")](single-codebase-images/single-configure.png#lightbox)

4. 完成向导。 在解决方案中创建一个库项目。

5. 右键单击新的库项目，然后选择**选项**。 **生成 > 常规**部分允许**目标框架**设置 – 选择.NET 可移植 PCL 配置文件或.NET Standard 版本：

   [![](single-codebase-images/single-choose-type-sml.png "选择 PCL 或.NET 标准库类型")](single-codebase-images/single-choose-type.png#lightbox)

6. 另外，请在**项目选项**窗口中，打开**NuGet 包 > 元数据**部分，然后输入[必需的元数据](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（以及任何可选的元数据）：

   [![](single-codebase-images/single-metadata-sml.png "输入所需的元数据")](single-codebase-images/single-metadata.png#lightbox)

7. 右键单击类库项目并选择**创建 NuGet 包**（或生成或部署解决方案） 和 **.nupkg** NuGet 包文件将保存在 **/bin/** （调试或发行版中的，具体取决于配置） 的文件夹：

   ![](single-codebase-images/create-nuget-package.png "NuGet 包文件将保存在 bin 文件夹中调试或发布，具体取决于配置")


## <a name="verifying-the-output"></a>验证输出

NuGet 包还可以 ZIP 文件，因此可以检查生成的包的内部结构。

此屏幕截图显示了基于 PCL 的 NuGet – 内容仅单个 PCL 程序集将包含：

![](single-codebase-images/nuget-output.png "NuGet 包中包含文件")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>添加特定于平台的代码

基于 PCL 的项目和基于.NET Standard 的项目不能包含特定于平台的引用 （如 iOS 或 Android 功能）。

如果现有 PCL 项目或.NET Standard 项目需要进行扩展以包含特定于平台的代码，这可以通过右键单击项目并选择**添加 > 添加平台实现...** :

[![](single-codebase-images/add-later-sml.png "添加平台实现菜单")](single-codebase-images/add-later.png#lightbox)

一个或多个平台项目可以添加到解决方案，并且现有 PCL 或.NET Standard 库可以根据需要转换为共享项目：

[![](single-codebase-images/add-later-platforms-sml.png "添加平台选项，例如 iOS、 Android 和共享项目")](single-codebase-images/add-later-platforms-sml.png#lightbox)

后转换为共享项目，请访问**项目选项 > NuGet 包 > 引用程序集**
[部分](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md)，并确保任何必需的配置文件选择 (以便NuGet 仍是与项目中以前使用兼容）。


## <a name="related-links"></a>相关链接

- [元数据指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
