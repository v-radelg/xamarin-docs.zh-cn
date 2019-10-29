---
title: 为 NuGet 创建新的特定于平台的库项目
description: 本文档介绍如何创建单个 NuGet 包，其中包含针对多个平台的平台特定代码。
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 925e08c600c695640c927ada26df376a252b3927
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016715"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>为 NuGet 创建新的特定于平台的库项目

面向特定平台（如 iOS 和 Android）的多平台库项目最适用于共享项目。

NuGet 可以同时包含 iOS 和 Android 特定的代码，以及这两种代码共有的 .NET 代码。

创建多个程序集，并将其内置于单个 NuGet 包中。 NuGet 标准确保可以将包添加到所有受支持的项目类型，如 Xamarin 和 Android 项目。

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>创建跨平台库 NuGet 的步骤

1. 选择 "**文件" > "新建解决方案**" （或者右键单击现有解决方案，然后选择 "**添加 >" 新建项目**"）。

2. 从多**平台 > 库**部分中选择多**平台库**：

    [![](platform-specific-images/mulitplatform-library-sml.png "Configure multi-platform library for a single code base")](platform-specific-images/multiplatform-library.png#lightbox)

3. 输入**名称**和**说明**，并选择 "**特定于平台**"：

    [![](platform-specific-images/specific-configure-sml.png "Configure platform-specific library for iOS and Android")](platform-specific-images/specific-configure.png#lightbox)

4. 完成向导。 以下项目将添加到解决方案中：

    - **Android 项目**–可以选择将特定于 android 的代码添加到此项目中。
    - **Ios 项目**–可以选择将 ios 特定代码添加到此项目中。
    - **NuGet 项目**–不向此项目添加任何代码。 它引用其他项目，包含 NuGet 包输出的元数据配置。
    - **共享项目**–应将公共代码添加到此项目，包括 `#if` 编译器指令内特定于平台的代码。

5. 右键单击 NuGet 项目，然后选择 "**选项**"，然后打开**nuget 包 "> 元数据**" 部分，并输入[所需的元数据](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（以及任何可选的元数据）：

    [![](platform-specific-images/specific-metadata-sml.png "Enter required metadata")](platform-specific-images/specific-metadata.png#lightbox)

6. 同时，在 "**项目选项**" 窗口中，打开 "**引用程序集**" 部分，然后选择共享库将通过 "bait 和交换机" 支持的 PCL 配置文件：

    ![](platform-specific-images/specific-reference-assemblies.png "Also in the Project Options window, open the Reference Assemblies section and choose   which PCL profiles the shared library will support via bait and switch")

    > [!NOTE]
    > "Bait and switch" 表示 PCL 程序集将仅包含由库公开的 API （不能包含特定于平台的代码）。 将 NuGet 添加到 Xamarin 项目后，将针对 PCL 编译共享库，但特定于平台的程序集包含 iOS 或 Android 项目实际使用的代码。

7. 右键单击该项目，然后选择 "**创建 NuGet 包**（或生成或部署解决方案）"，然后将**nupkg** NuGet 包文件保存在 **/bin/** 文件夹中（调试或发布，具体取决于配置）。

    ![](platform-specific-images/create-nuget-package.png "NuGet package file will be saved in the bin folder either Debug or Release, depending on configuration")

## <a name="verifying-the-output"></a>验证输出

NuGet 包也是 ZIP 文件，因此可以检查生成的包的内部结构。

此屏幕截图显示了支持 iOS 和 Android 的特定于平台的 NuGet 的内容，并选择了两个引用程序集：

![](platform-specific-images/nuget-output.png "Files contained in the NuGet package")

## <a name="related-links"></a>相关链接

- [元数据指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
