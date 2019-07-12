---
title: 更新现有 iOS 应用
description: 本文档介绍了必须遵循更新从经典 API 到 Unified API Xamarin.iOS 应用的步骤。
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4e471aaca2a7a5f247b21dd1c1863a01b062a716
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855744"
---
# <a name="updating-existing-ios-apps"></a>更新现有 iOS 应用

_按照以下步骤来更新现有的 Xamarin.iOS 应用程序以使用统一 API。_

更新现有应用以使用统一 API 需要更改项目文件本身以及命名空间和应用程序代码中使用的 Api。

## <a name="the-road-to-64-bits"></a>64 位之路

支持从 Xamarin.iOS 移动应用程序的 64 位设备体系结构需要新的统一 Api。 截至 2015 年 2 月 1 日，Apple 要求所有新的应用程序提交到 iTunes 应用商店支持 64 位体系结构。

Xamarin 提供了用于 Visual Studio for Mac 和 Visual Studio 自动执行迁移过程从经典 API 到 Unified API 的工具或手动将转换的项目文件。 虽然强烈建议使用自动工具，本文将介绍这两种方法。

### <a name="before-you-start"></a>开始之前...

到 Unified API 更新现有的代码之前，强烈建议您消除所有*编译警告*。 许多*警告*经典 API 中将变为错误后将迁移到统一。 在开始之前修复它们是更轻松，因为来自经典 API 的编译器消息通常提供有关要更新的提示。

## <a name="automated-updating"></a>自动更新

后已经修复了警告，选择现有的 iOS 项目在 Visual Studio for Mac 或 Visual Studio，并选择**迁移到 Xamarin.iOS Unified API**从**项目**菜单。 例如:

![](updating-ios-apps-images/beta-tool1.png "从项目菜单中选择迁移到 Xamarin.iOS Unified API")

你将需要同意此警告运行自动化的迁移之前 （显然您应确保此 adventure 在开始之前已备份/源代码管理）：

![](updating-ios-apps-images/beta-tool2.png "运行自动的迁移之前同意此警告")

该工具基本上可自动中所述的所有步骤**手动更新**部分下面提供，是将现有的 Xamarin.iOS 项目转换为 Unified API 的建议的方法。

## <a name="steps-to-update-manually"></a>若要手动更新的步骤

同样后已经修复了警告，, 请执行以下步骤手动更新 Xamarin.iOS 应用程序以使用新的统一 API:

### <a name="1-update-project-type--build-target"></a>1.更新项目类型和生成目标

更改在项目风格你**csproj**文件从`6BC8ED88-2882-458C-8E55-DFD12B67127B`到`FEACFBD2-3405-455C-9665-78FE426C6842`。 编辑**csproj**文件在文本编辑器中，替换中的第一项`<ProjectTypeGuids>`元素所示：

![](updating-ios-apps-images/csproj.png "编辑 csproj 文件中所示替换 ProjectTypeGuids 元素中的第一项的文本编辑器")

更改**导入**元素，其中包含`Xamarin.MonoTouch.CSharp.targets`到`Xamarin.iOS.CSharp.targets`所示：

![](updating-ios-apps-images/csproj2.png "更改包含到 Xamarin.iOS.CSharp.targets Xamarin.MonoTouch.CSharp.targets 所示的导入元素")

### <a name="2-update-project-references"></a>2.更新项目引用

展开 iOS 应用程序项目**引用**节点。 一开始会显示 * 中断- **monotouch**引用类似于以下屏幕截图 （因为我们只需更改的项目类型）：

![](updating-ios-apps-images/references.png "它一开始会显示中断 monotouch 引用类似于以下屏幕截图因为更改的项目类型")

右键单击到 iOS 应用程序项目**编辑引用**，然后单击**monotouch**引用和使用红色"X"按钮将其删除。

![](updating-ios-apps-images/references-delete-monotouch-sml.png "右键单击 iOS 应用程序项目到编辑引用，然后单击 monotouch 引用并且删除它使用红色 X 按钮")

现在滚动到引用列表和刻度线的末尾**Xamarin.iOS**程序集。

![](updating-ios-apps-images/references-add-xamarinios-sml.png "现在滚动至刻度线的 Xamarin.iOS 程序集的引用列表的末尾")

按**确定**保存项目的引用更改。

### <a name="3-remove-monotouch-from-namespaces"></a>3.从命名空间中删除 MonoTouch

删除**MonoTouch**中的命名空间前缀`using`语句或任何位置 classname 具有已完全限定的 （例如。 `MonoTouch.UIKit` 只需将成为`UIKit`)。

### <a name="4-remap-types"></a>4.重新映射类型

[本机类型](~/cross-platform/macios/nativetypes.md)已引入用于取代了以前使用过，例如实例的某些类型`System.Drawing.RectangleF`与`CoreGraphics.CGRect`（举例而言）。 可以上找到的类型的完整列表[本机类型](~/cross-platform/macios/nativetypes.md)页。

### <a name="5-fix-method-overrides"></a>5.解决方法重写

某些`UIKit`方法有其签名已更改为使用新[本机类型](~/cross-platform/macios/nativetypes.md)(如`nint`)。 如果自定义子类重写这些方法签名将不再匹配，并将导致错误。 通过更改以匹配新的签名使用本机类型的子类来修复这些方法重写。

示例包括更改`public override int NumberOfSections (UITableView tableView)`以返回`nint`并更改两者都返回类型和参数类型中的`public override int RowsInSection (UITableView tableView, int section)`到`nint`。

## <a name="considerations"></a>注意事项

将现有的 Xamarin.iOS 项目从经典 API 转换为新的统一 API，如果该应用程序依赖于一个或多个组件或 NuGet 包时，应考虑以下注意事项。

### <a name="components"></a>组件

你的应用程序中包含的任何组件还需要更新为 Unified API 或尝试进行编译时，您将看到冲突。 对于任何包含的组件，使用支持统一 API 的 Xamarin 组件存储区中的新版本替换当前版本和生成已清理。 任何组件都尚未转换由作者，将显示在组件存储中的唯一警告 32 位。

### <a name="nuget-support"></a>NuGet 支持

虽然我们提供对 NuGet 才能使用统一 API 支持的更改，没有新版本的 NuGet，因此我们评估如何获取 NuGet 能够识别新的 Api。

在此之前，就像这些组件，您将需要切换到支持统一的 Api 版本在项目中包含任何 NuGet 包之后执行干净的生成。

> [!IMPORTANT]
> 如果窗体中有错误 _"错误 3 不能在相同的 Xamarin.iOS 项目中包含 monotouch.dll 和 Xamarin.iOS.dll'-'Xamarin.iOS.dll 显式引用，而 monotouch.dll 引用的 ' xxx，版本 = 0.0.000，区域性 = 中性，PublicKeyToken = null"_ 后转换到统一的 Api 应用程序，它通常是因为尚未更新到 Unified API 的项目中具有的组件或 NuGet 包。 你将需要删除现有的组件/NuGet、 更新为支持统一 Api 的版本和生成已清理。

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>启用 64 位版本的 xamarin ios 应用

对于 Xamarin.iOS 移动应用程序已转换为 Unified API，开发人员仍需要启用 64 位计算机从应用程序的选项的应用程序的构建。 请参阅**启用 64 位版本的 Xamarin.iOS 应用程序**的[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md#enable-64)启用 64 位的详细说明的文档生成。

## <a name="finishing-up"></a>结束语

指示选择使用自动或手动方法来将 Xamarin.iOS 应用程序从经典部署模型转换为统一 Api，有多个实例，将进一步，需要手动干预。 请参阅我们[更新为 Unified API 的代码的提示](~/cross-platform/macios/unified/updating-tips.md)文档有关的已知问题和解决方法。

## <a name="related-links"></a>相关链接

- [将代码更新为 Unified API 的提示](~/cross-platform/macios/unified/updating-tips.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
