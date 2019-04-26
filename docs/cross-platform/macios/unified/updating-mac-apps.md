---
title: 更新现有 Mac 应用程序
description: 本文档介绍了必须遵循更新为 Unified API 从经典 API Xamarin.Mac 应用的步骤。
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 5e6034b079bba5e884872e4f2096d677fd3641d0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213493"
---
# <a name="updating-existing-mac-apps"></a>更新现有 Mac 应用程序

更新现有应用以使用统一 API 需要更改项目文件本身以及命名空间和应用程序代码中使用的 Api。

## <a name="the-road-to-64-bits"></a>64 位之路

支持从 Xamarin.Mac 应用程序的 64 位设备体系结构需要新的统一 Api。 截至 2015 年 2 月 1 日，Apple 要求所有新的应用程序提交到 Mac App Store 支持 64 位体系结构。

Xamarin 提供了用于 Visual Studio for Mac 和 Visual Studio 自动执行迁移过程从经典 API 到 Unified API 的工具或手动将转换的项目文件。 虽然强烈建议使用自动工具，本文将介绍这两种方法。

### <a name="before-you-start"></a>开始之前...

到 Unified API 更新现有的代码之前，强烈建议您消除所有*编译警告*。 许多*警告*经典 API 中将变为错误后将迁移到统一。 在开始之前修复它们是更轻松，因为来自经典 API 的编译器消息通常提供有关要更新的提示。

## <a name="automated-updating"></a>自动更新

后已经修复了警告，选择现有 Mac 项目在 Visual Studio for Mac 或 Visual Studio，并选择**迁移到 Xamarin.Mac Unified API**从**项目**菜单。 例如：

![](updating-mac-apps-images/beta-tool1.png "从项目菜单中选择迁移到 Xamarin.Mac Unified API")

你将需要同意此警告运行自动化的迁移之前 （显然您应确保此 adventure 在开始之前已备份/源代码管理）：

![](updating-mac-apps-images/migrate01.png "运行自动的迁移之前同意此警告")

有两种受支持的目标框架类型在 Xamarin.Mac 应用程序中使用统一 API 时可选择：

- **Xamarin.Mac Mobile 框架**-这是使用 Xamarin.iOS 和 Xamarin.Android 支持完整的子集的关注的.NET framework**桌面**框架。 这是推荐的框架，因为它提供了卓越的链接行为由于较小的平均二进制文件。
- **Xamarin.Mac.NET 4.5 Framework** -此框架，是的子集**桌面**框架。 但是，剪裁更少完整**桌面**framework 比**移动**framework 和应 _"上手"_ 与大多数 NuGet 包或第三方库。 这允许开发人员可以使用标准**桌面**时仍在使用受支持的框架，但此选项会生成更大的应用程序捆绑包的程序集。 这是建议的框架使用第三方.NET 程序集的情况，都不符合**Xamarin.Mac Mobile 框架**。 有关支持的程序集的列表，请参阅我们[程序集](~/cross-platform/internals/available-assemblies.md)文档。

有关目标框架的详细的信息和选择 Xamarin.Mac 应用程序的特定目标的影响，请参阅我们[目标框架](~/mac/platform/target-framework.md)文档。 

该工具基本上可自动中所述的所有步骤**手动更新**部分下面提供，是将现有的 Xamarin.Mac 项目转换为 Unified API 的建议的方法。

## <a name="steps-to-update-manually"></a>若要手动更新的步骤

同样，一旦已经修复了警告，请执行以下步骤手动更新 Xamarin.Mac 应用程序以使用新的统一 API:

### <a name="1-update-project-type--build-target"></a>1.更新项目类型和生成目标

更改在项目风格你**csproj**文件从`42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23`到`A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`。 编辑**csproj**文件在文本编辑器中，替换中的第一项`<ProjectTypeGuids>`元素所示：

![](updating-mac-apps-images/csproj.png "编辑 csproj 文件中所示替换 ProjectTypeGuids 元素中的第一项的文本编辑器")

更改**导入**元素，其中包含`Xamarin.Mac.targets`到`Xamarin.Mac.CSharp.targets`所示：

![](updating-mac-apps-images/csproj2.png "更改包含到 Xamarin.Mac.CSharp.targets Xamarin.Mac.targets 所示的导入元素")

添加以下行后的代码`<AssemblyName>`元素：

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

示例:

![](updating-mac-apps-images/csproj3.png "< 程序集名称 > 元素后添加以下代码行")

### <a name="2-update-project-references"></a>2.更新项目引用

展开的 Mac 应用程序项目**引用**节点。 一开始会显示 * 中断- **XamMac**引用类似于以下屏幕截图 （因为我们只需更改的项目类型）：

![](updating-mac-apps-images/references.png "它一开始会显示中断 XamMac 引用类似于以下屏幕截图")

单击**齿轮图标**旁边**XamMac**项，并选择**删除**删除损坏的引用。

接下来，右键单击**引用**中的文件夹**解决方案资源管理器**，然后选择**编辑引用**。 滚动到底部的引用列表，并选中除了**Xamarin.Mac**。

![](updating-mac-apps-images/references2.png "滚动到底部的引用列表，并选中除了 Xamarin.Mac")

按**确定**保存项目的引用更改。

### <a name="3-remove-monomac-from-namespaces"></a>3.从命名空间中删除 MonoMac

删除**MonoMac**中的命名空间前缀`using`语句或任何位置 classname 具有已完全限定的 （例如。 `MonoMac.AppKit` 只需将成为`AppKit`)。

### <a name="4-remap-types"></a>4.重新映射类型

[本机类型](~/cross-platform/macios/nativetypes.md)已引入用于取代了以前使用过，例如实例的某些类型`System.Drawing.RectangleF`与`CoreGraphics.CGRect`（举例而言）。 可以上找到的类型的完整列表[本机类型](~/cross-platform/macios/nativetypes.md)页。

### <a name="5-fix-method-overrides"></a>5.解决方法重写

某些`AppKit`方法有其签名已更改为使用新[本机类型](~/cross-platform/macios/nativetypes.md)(如`nint`)。 如果自定义子类重写这些方法签名将不再匹配，并将导致错误。 通过更改以匹配新的签名使用本机类型的子类来修复这些方法重写。 

## <a name="considerations"></a>注意事项

将现有的 Xamarin.Mac 项目从经典 API 转换为新的统一 API，如果该应用程序依赖于一个或多个组件或 NuGet 包时，应考虑以下注意事项。 

### <a name="components"></a>组件数

你的应用程序中包含的任何组件还需要更新为 Unified API 或尝试进行编译时，您将看到冲突。 对于任何包含的组件，使用支持统一 API 的 Xamarin 组件存储区中的新版本替换当前版本和生成已清理。 任何组件都尚未转换由作者，将显示在组件存储中的唯一警告 32 位。

### <a name="nuget-support"></a>NuGet 支持

虽然我们提供对 NuGet 才能使用统一 API 支持的更改，没有新版本的 NuGet，因此我们评估如何获取 NuGet 能够识别新的 Api。 

在此之前，就像这些组件，您将需要切换到支持统一的 Api 版本在项目中包含任何 NuGet 包之后执行干净的生成。

> [!IMPORTANT]
> 如果窗体中有错误 _"错误 3 不能在同一个 Xamarin.Mac 项目中包含 monomac.dll 和 Xamarin.Mac.dll-Xamarin.Mac.dll 显式引用，而 monomac.dll 引用的 ' xxx，版本 = 0.0.000，区域性 =neutral，PublicKeyToken = null"_ 后转换到统一的 Api 应用程序，它通常是因为尚未更新到 Unified API 的项目中具有的组件或 NuGet 包。 你将需要删除现有的组件/NuGet、 更新为支持统一 Api 的版本和生成已清理。

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>启用 64 位版本的 Xamarin.Mac 应用

对于 Xamarin.Mac 移动应用程序已转换为 Unified API，开发人员仍需要启用 64 位计算机从应用程序的选项的应用程序的构建。 请参阅**启用 64 位版本的 Xamarin.Mac 应用程序**的[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)启用 64 位的详细说明的文档生成。
    
## <a name="finishing-up"></a>结束语

指示选择使用自动或手动方法来将 Xamarin.Mac 应用程序从经典部署模型转换为统一 Api，有多个实例，将进一步，需要手动干预。 请参阅我们[更新为 Unified API 的代码的提示](~/cross-platform/macios/unified/updating-tips.md)文档有关的已知问题和解决方法。

## <a name="related-links"></a>相关链接

- [将代码更新为 Unified API 的提示](~/cross-platform/macios/unified/updating-tips.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
