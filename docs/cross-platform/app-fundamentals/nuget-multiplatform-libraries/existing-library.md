---
title: 从现有库项目创建 NuGet
description: 本文档介绍如何从现有库项目创建 NuGet 包，以便与其他开发人员共享代码。
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: conceptdev
ms.author: crdun
ms.date: 04/20/2017
ms.openlocfilehash: e70cf426b2357570585c1c8f1cf21715858cb6f6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758161"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>从现有库项目创建 NuGet

可以通过 "**项目选项**" 窗口将现有 PCL 或 .NET Standard 库转换为 nuget：

1. 右键单击 " **Solution Pad**中的库项目，然后选择"**选项**"。

2. 请在 "**常规**" 选项卡中转到**NuGet 包 > 元数据**"部分，并输入所有[必需的信息](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)：

   [![](existing-library-images/existing-metadata-sml.png "输入所需的元数据")](existing-library-images/existing-metadata.png#lightbox)

3. （可选）在 "**详细信息**" 选项卡中[添加其他元数据](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)。

4. 配置元数据后，可以右键单击该项目，然后选择 "**创建 NuGet 包**"，然后将**nupkg** NuGet 包文件保存在 **/Bin/** 文件夹中（"调试" 或 "发布"，具体取决于配置）。

   ![](existing-library-images/create-nuget-package.png "从右键单击菜单中选择 \"创建 NuGet 包\"")

5. 若要在_每次_生成或部署时创建 nuget 包，请在生成项目时，请参阅**Nuget 包 > 生成**部分并勾选**创建 nuget 包**：

    [![](existing-library-images/existing-tickbox-sml.png "勾选以创建 NuGet 包")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> 构建 NuGet 包可能会降低生成过程的速度。 如果此框不是勾选，则你仍可随时从项目上下文菜单（如上面的步骤4所示）中手动生成 NuGet 包。

## <a name="verifying-the-output"></a>验证输出

NuGet 包也是 ZIP 文件，因此可以检查生成的包的内部结构。

此屏幕截图显示了基于 PCL 的 NuGet 的内容–只包含一个 PCL 程序集：

![](existing-library-images/nuget-output.png "NuGet 包中包含的文件")

## <a name="related-links"></a>相关链接

- [元数据指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
