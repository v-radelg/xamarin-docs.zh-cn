---
title: 正在更新现有的 iOS 应用
description: 本文档介绍将 Xamarin iOS 应用程序从 Classic API 更新到 Unified API 时必须遵循的步骤。
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: c74efef96a15a950122041eb52dc09835bb8940b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279554"
---
# <a name="updating-existing-ios-apps"></a>正在更新现有的 iOS 应用

_按照以下步骤更新现有的 Xamarin iOS 应用以使用 Unified API。_

更新现有应用程序以使用 Unified API 需要更改项目文件本身以及应用程序代码中使用的命名空间和 Api。

## <a name="the-road-to-64-bits"></a>64位的道路

需要新的统一 Api，才能支持 Xamarin iOS 移动应用程序中的64位设备体系结构。 从2月1日开始，2015 Apple 要求向 iTunes 应用商店提交的所有新应用都支持64位体系结构。

Xamarin 为 Visual Studio for Mac 和 Visual Studio 提供了工具来自动执行从 Classic API 到 Unified API 的迁移过程，也可以手动转换项目文件。 尽管强烈建议使用自动工具，但本文将介绍这两种方法。

### <a name="before-you-start"></a>开始之前 。

将现有代码更新到 Unified API 之前，强烈建议您消除所有*编译警告*。 迁移到统一后，Classic API 中的许多*警告*都将变成错误。 在开始之前修复它们会更容易，因为来自 Classic API 的编译器消息通常会提供要更新的内容的提示。

## <a name="automated-updating"></a>自动更新

解决这些警告后，请在 Visual Studio for Mac 或 Visual Studio 中选择现有的 iOS 项目，然后从 "**项目**" 菜单中选择 "**迁移到 Xamarin" Unified API** 。 例如：

![](updating-ios-apps-images/beta-tool1.png "从 \"项目\" 菜单中选择 \"迁移到 Xamarin iOS Unified API")

你将需要在自动迁移运行之前同意此警告（显然，你应该确保在此艾德公司上着手之前具有备份/源代码管理）：

![](updating-ios-apps-images/beta-tool2.png "在自动迁移运行之前同意此警告")

该工具主要自动执行下面提供的 "**手动更新**" 一节中所述的所有步骤，这是将现有 Xamarin 项目转换到 Unified API 的建议方法。

## <a name="steps-to-update-manually"></a>手动更新的步骤

同样，在修复警告后，请按照以下步骤手动更新 Xamarin iOS 应用以使用新 Unified API：

### <a name="1-update-project-type--build-target"></a>1.更新项目类型 & 生成目标

将 **.csproj**文件中的项目风格从`6BC8ED88-2882-458C-8E55-DFD12B67127B`更改为。 `FEACFBD2-3405-455C-9665-78FE426C6842` 在文本编辑器中编辑 **.csproj**文件，并按如下所示替换`<ProjectTypeGuids>`元素中的第一项：

![](updating-ios-apps-images/csproj.png "在文本编辑器中编辑 .csproj 文件，并按如下所示替换 ProjectTypeGuids 元素中的第一项")

按照如下所示，将`Xamarin.MonoTouch.CSharp.targets`包含的导入元素更改为： `Xamarin.iOS.CSharp.targets`

![](updating-ios-apps-images/csproj2.png "按如下所示将包含 Monotouch.dialog 的 Import 元素更改为 Xamarin。")

### <a name="2-update-project-references"></a>2.更新项目引用

展开 "iOS 应用程序" 项目的 "**引用**" 节点。 它最初将显示类似于此屏幕截图的 * **monotouch.dialog**引用（因为我们只是更改了项目类型）：

![](updating-ios-apps-images/references.png "它最初将显示类似于此屏幕截图的 monotouch.dialog 引用，因为项目类型已更改")

右键单击 iOS 应用程序项目以**编辑引用**，然后单击 " **monotouch.dialog** " 引用并使用红色 "X" 按钮将其删除。

![](updating-ios-apps-images/references-delete-monotouch-sml.png "右键单击 iOS 应用程序项目以编辑引用，然后单击 \"monotouch.dialog\" 引用并使用红色 X 按钮删除它")

现在滚动到引用列表的末尾，并勾选**Xamarin**程序集。

![](updating-ios-apps-images/references-add-xamarinios-sml.png "现在滚动到引用列表的末尾，并勾选 Xamarin 程序集")

按 **"确定"** 以保存项目引用更改。

### <a name="3-remove-monotouch-from-namespaces"></a>3.从命名空间中删除 Monotouch.dialog

从语句中的命名空间中`using`删除 monotouch.dialog 前缀，或者在类的完全限定的位置（例如 `MonoTouch.UIKit`变成了`UIKit`）。

### <a name="4-remap-types"></a>4.重新映射类型

引入了[本机类型](~/cross-platform/macios/nativetypes.md)，这将替换以前使用的某些类型，如的实例`System.Drawing.RectangleF` `CoreGraphics.CGRect` （例如）。 可以在[本机类型](~/cross-platform/macios/nativetypes.md)页上找到类型的完整列表。

### <a name="5-fix-method-overrides"></a>5.修复方法替代

某些`UIKit`方法已将其签名更改为使用新的[本机类型](~/cross-platform/macios/nativetypes.md)（如`nint`）。 如果自定义子类重写这些方法，则签名将不再匹配，并将导致错误。 通过更改子类以匹配使用本机类型的新签名来修复这些方法重写。

示例包括`public override int NumberOfSections (UITableView tableView)` `nint`：更改以`nint`返回和更改中的`public override int RowsInSection (UITableView tableView, int section)`返回类型和参数类型。

## <a name="considerations"></a>注意事项

如果将现有的 Xamarin iOS 项目从 Classic API 转换为新 Unified API （如果该应用依赖于一个或多个组件或 NuGet 包），则应考虑以下注意事项。

### <a name="components"></a>组件

还需要将应用程序中包含的任何组件更新为 Unified API，否则在尝试编译时将会出现冲突。 对于包含的任何组件，请将当前版本替换为支持 Unified API 的 Xamarin 组件存储中的新版本，并执行干净的生成。 尚未由作者转换的任何组件将在组件存储区中显示仅限32位的警告。

### <a name="nuget-support"></a>NuGet 支持

虽然我们提供了对 NuGet 的更改来处理 Unified API 支持，但尚未推出新版本的 NuGet，因此我们正在评估如何获取 NuGet 来识别新 Api。

在此之前，你需要将项目中包含的任何 NuGet 包切换到支持统一 Api 的版本，并在以后进行干净的生成。

> [!IMPORTANT]
> 如果您在_同一 Xamarin iOS 项目中出现错误 "错误3不能同时包括 ' monotouch.dialog ' 和 ' xamarin. .dll '"，则会显式引用 "xamarin"，而 "monotouch.dialog" 被 "xxx，Version = 0.0.000，Culture =" 引用非特定类型，PublicKeyToken = null ' "_ 将应用程序转换为统一 api 后，通常是因为项目中的组件或 NuGet 包尚未更新到 Unified API。 需要删除现有的组件/NuGet，更新到支持统一 Api 的版本，并执行干净的生成。

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>启用 Xamarin iOS 应用的64位版本

对于已转换为 Unified API 的 Xamarin iOS 移动应用程序，开发人员仍需从应用的选项中为64位计算机启用应用程序的构建。 有关启用64位生成的详细说明，请参阅启用[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md#enable-64)文档的**Xamarin iOS 应用的64位版本**。

## <a name="finishing-up"></a>完成

无论你选择使用自动还是手动方法将 Xamarin iOS 应用程序从经典 Api 转换为统一 Api，都有几个需要进一步干预的实例。 请参阅我们有关将[代码更新到 Unified API 文档以](~/cross-platform/macios/unified/updating-tips.md)了解已知问题和解决办法的技巧。

## <a name="related-links"></a>相关链接

- [将代码更新为 Unified API 的提示](~/cross-platform/macios/unified/updating-tips.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典与 Unified API 差异](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
