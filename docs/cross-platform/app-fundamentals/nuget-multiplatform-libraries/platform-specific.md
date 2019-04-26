---
title: 创建新的特定于平台的类库项目的 NuGet
description: 本文档介绍如何创建包含多个平台的特定于平台的代码的单个 NuGet 包。
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 00a02973d6016ad63e4317279515acc2b4e2e81b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61267177"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>创建新的特定于平台的类库项目的 NuGet

面向特定平台，iOS 和 Android 等的多平台库项目最适用于共享的项目。

NuGet 可以包含特定于 iOS 和 Android 的代码，以及公用的.NET 代码。

多个程序集创建和生成到一个 NuGet 包。 NuGet 标准会确保包，可以添加到所有支持的项目类型，如 Xamarin.iOS 和 Android 项目。

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>创建跨平台库 NuGet 的步骤

1. 选择**文件 > 新的解决方案**(或右键单击现有的解决方案，然后选择**添加 > 新建项目**)。

2. 选择**多平台库**从**多平台 > 库**部分：

  [![](platform-specific-images/mulitplatform-library-sml.png "配置适用于单个代码库的多平台库")](platform-specific-images/multiplatform-library.png#lightbox)

3. 输入**名称**并**说明**，然后选择**特定于平台**:

  [![](platform-specific-images/specific-configure-sml.png "配置适用于 iOS 和 Android 的特定于平台的库")](platform-specific-images/specific-configure.png#lightbox)

4. 完成向导。 以下项目添加到解决方案中：

  - **Android 项目**– （可选） 可以将特定于 Android 的代码添加到此项目。
  - **iOS 项目**– （可选） 可以将特定于 iOS 的代码添加到此项目。
  - **NuGet 项目**– 对此项目中添加任何代码。 它引用其他项目，并包含输出的 NuGet 包的元数据配置。
  - **共享项目**– 应将常见的代码添加到此项目，包括特定于平台的代码`#if`编译器指令。

5. 右键单击 NuGet 项目并选择**选项**，然后打开**NuGet 包 > 元数据**部分，然后输入[必需的元数据](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)（为以及任何可选元数据）：

  [![](platform-specific-images/specific-metadata-sml.png "输入所需的元数据")](platform-specific-images/specific-metadata.png#lightbox)

6. 另外，请在**项目选项**窗口中，打开**引用程序集**部分，并选择共享的库将支持通过"bait 和 switch"PCL 配置文件：

  ![](platform-specific-images/specific-reference-assemblies.png "此外在项目选项窗口中，打开引用程序集部分，然后选择共享的库将支持通过 bait 和 switch 的 PCL 配置文件")

  > [!NOTE]
> "Bait 和 switch"意味着 PCL 程序集将仅包含由 （它不能包含特定于平台的代码） 库公开的 API。 当 NuGet 添加到 Xamarin 项目时，将针对在 PCL 中，编译共享的库，但特定于平台的程序集包含 iOS 或 Android 项目实际使用的代码。

7. 右键单击项目并选择**创建 NuGet 包**（或生成或部署解决方案） 和 **.nupkg** NuGet 包文件将保存在 **/bin/** 文件夹 (调试或发布，具体取决于配置） 时。

  ![](platform-specific-images/create-nuget-package.png "NuGet 包文件将保存在 bin 文件夹中调试或发布，具体取决于配置")


## <a name="verifying-the-output"></a>验证输出

NuGet 包还可以 ZIP 文件，因此可以检查生成的包的内部结构。

所选的以下屏幕截图显示特定于平台的 NuGet 支持 iOS 和 Android，并且有两个引用程序集的内容：

![](platform-specific-images/nuget-output.png "NuGet 包中包含文件")


## <a name="related-links"></a>相关链接

- [元数据指南](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
