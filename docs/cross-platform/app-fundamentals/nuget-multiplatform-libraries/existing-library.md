---
title: 从现有库项目创建 NuGet
description: 本文档介绍如何从现有的库项目，从而允许代码与其他开发人员共享创建 NuGet 包。
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 7f407b22d1793d585ae40aeae8c2d9b7616784e6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61267791"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>从现有库项目创建 NuGet

现有 PCL 或.NET Standard 库可以转换成通过 Nuget**项目选项**窗口：

1. 在库项目上右键单击**Solution Pad** ，然后选择**选项**。

2. 转到**NuGet 包 > 元数据**部分，并输入所有[所需的信息](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)中**常规**选项卡：

  [![](existing-library-images/existing-metadata-sml.png "输入所需的元数据")](existing-library-images/existing-metadata.png#lightbox)

3. （可选）[添加的其他元数据](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)中**详细信息**选项卡。

4. 元数据配置后，可以右键单击该项目并选择**创建 NuGet 包**并 **.nupkg** NuGet 包文件将保存在 **/bin/**（调试或发行版中，根据配置） 的文件夹。

  ![](existing-library-images/create-nuget-package.png "右键单击菜单中选择创建 NuGet 包")

5. 若要创建 NuGet 包在_每个_生成或部署，请转到**NuGet 包 > 构建**部分和刻度线**生成项目时创建 NuGet 包**:

    [![](existing-library-images/existing-tickbox-sml.png "若要创建 NuGet 包的刻度线")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> 生成 NuGet 包可以降低生成过程。 如果未勾选此框，您仍可以生成 NuGet 包手动随时从项目上下文菜单 （上面的步骤 4 中所示）。

## <a name="verifying-the-output"></a>验证输出

NuGet 包还可以 ZIP 文件，因此可以检查生成的包的内部结构。

此屏幕截图显示了基于 PCL 的 NuGet – 内容仅单个 PCL 程序集将包含：

![](existing-library-images/nuget-output.png "NuGet 包中包含文件")


## <a name="related-links"></a>相关链接

- [元数据指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
