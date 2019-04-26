---
title: 更新对 NuGet 的组件引用
description: 本文档介绍如何引用替换为你组件 NuGet 程序包添加到未来的你的应用，因为 Xamarin 组件存储已停止使用。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: 70ca9a73c83bed5233b77a6f7be80a13f04f2bcb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360745"
---
# <a name="updating-component-references-to-nuget"></a>更新对 NuGet 的组件引用

> [!IMPORTANT]
> 截至 2018 年 5 月 15 日之前停用组件应用商店 (最初为此闭包[宣布](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/)在 2017 年 11 月)。
>
> Xamarin 组件不再支持在 Visual Studio 中，并应替换为 NuGet 包。 请按照以下说明手动从项目中删除组件的引用。

请参阅这些说明中的添加 NuGet 包[Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package)或[Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

一组常用 Xamarin[插件和库](https://github.com/xamarin/XamarinComponents/blob/master/README.md)可用来帮助查找组件是作为 NuGet 包不可用的替代方法。

## <a name="manually-removing-component-references"></a>手动删除组件的引用

15.6 版本的 Visual Studio 和 Visual Studio for Mac 7.4 版本不再支持组件在项目中。 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

如果将项目加载到 Visual Studio 时，会显示以下对话框，说明，你必须删除任何组件从你的项目手动：

![警报对话框说明组件已在你的项目中找到，并且必须删除](component-nuget-images/component-alert-vs.png)

若要删除你的项目的组件：

1. 打开 **.csproj**文件。 若要执行此操作，右键单击项目名称并选择**卸载项目**。 

2. 右键再次单击已卸载的项目并选择**编辑 {你的项目名称}.csproj**。

3. 到文件中找到的任何引用`XamarinComponentReference`。 其外观应类似于下面的示例：

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. 删除对引用`XamarinComponentReference`并保存该文件。 在上面的示例中，则可以安全地删除整个`ItemGroup`。

5. 后已保存该文件，右键单击项目名称并选择**重新加载项目**。

6. 你的解决方案中每个项目重复上述步骤。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

如果你将项目加载到 Visual Studio for Mac，将显示以下对话框，说明，你必须删除任何组件从你的项目手动：

![警报对话框说明组件已在你的项目中找到，并且必须删除](component-nuget-images/component-alert.png)

若要删除你的项目的组件：

1. 打开.csproj 文件。 若要执行此操作，右键单击项目名称并选择**工具 > 编辑文件**。

2. 到文件中找到的任何引用`XamarinComponentReference`。 其外观应类似于下面的示例：

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. 删除对引用`XamarinComponentReference`并保存该文件。 在上面的示例中，则可以安全地删除整个 `ItemGroup`

4. 你的解决方案中每个项目重复上述步骤。

-----

> [!WARNING]
> 以下说明仅适用于较旧版本的 Visual Studio。
> **组件**节点不再可在当前版本的 Visual Studio 2017 或 Visual Studio for mac。

以下部分介绍如何更新现有的 Xamarin 解决方案，若要更改对 NuGet 包的组件引用。

- [包含 NuGet 包的组件](#contain)
- [NuGet 的替换的组件](#replace)

大多数组件都属于上述类别之一。
如果使用的一个组件，它不具有等效的 NuGet 包，请阅读[组件不包含 NuGet 迁移路径](#require-update)下面一节。

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>包含 NuGet 包的组件

许多组件已包含 NuGet 包和迁移路径是只需删除组件的引用。

您可以确定组件是否通过双击解决方案中的组件的 NuGet 包已包含：

![展开的组件节点](component-nuget-images/solution-sml.png)

**包**选项卡将列出组件中包括的任何 NuGet 包：

![包选项卡包含 NuGet](component-nuget-images/packages-tab-sml.png)

请注意，**程序集**选项卡将为空：

![程序集选项卡为空](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>正在更新解决方案

若要更新你的解决方案，请删除**组件**解决方案中的条目：

![删除组件](component-nuget-images/delete-component-sml.png)

NuGet 包将保留在中列出**包**节点和应用程序将编译并按常规方式运行。 在将来，将通过执行对此包更新**Nuget**更新功能：

![更新 NuGet 包](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>NuGet 的替换的组件

如果组件信息页面**程序集**选项卡包含的条目，如下所示，将需要手动查找等效的 NuGet 包。

![包含程序集](component-nuget-images/assemblies-tab-sml.png)

请注意，**包**选项卡将可能为空：

![](component-nuget-images/packages-tab-empty-sml.png)

_它可能包含 NuGet 依赖项，但是可以忽略这些警告。_

若要确认更换 NuGet 程序包是否存在，请搜索[NuGet.org](https://www.nuget.org/packages)，使用该组件名称，或者由作者。

例如，可以找到常用**sqlite net pcl**通过搜索包：

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) – 产品名称。
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) – 作者的个人资料。

### <a name="updating-the-solution"></a>正在更新解决方案

一旦确认该组件是在 NuGet 中提供，请执行以下步骤：

#### <a name="delete-the-component"></a>删除的组件

右键单击该解决方案中的组件，然后选择**删除**:

![删除组件](component-nuget-images/remove-component-sml.png)

这将删除该组件的任何引用。 这将中断生成，直到添加等效的 NuGet 包，以将其替换为。

#### <a name="add-the-nuget-package"></a>添加 NuGet 包

1. 右键单击**包**节点，然后选择**添加包...**.
2. 搜索按名称或作者 NuGet 替换：

  ![](component-nuget-images/nuget-search-sml.png)

3. 按**将包添加**。

NuGet 包将添加到你的项目，以及任何依赖项。
这样应修复生成。 如果生成继续失败，调查每个错误，以查看是否存在该组件和 NuGet 包的 API 差异。

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>组件没有 NuGet 迁移路径

请不要担心，如果您没有立即找到您的应用程序中使用的组件的替换。 现有组件将继续在 Visual Studio 15.5 中，并**组件**节点将显示在解决方案中像往常一样。

将来的 Visual Studio 版本，但是，将_不_还原或更新组件。
这意味着，如果您打开新的计算机上的解决方案，该组件将不会下载并安装;和作者将不能为您提供更新。 应计划：

* 从组件中提取的程序集，并直接在您的项目中引用它们。
* 请与组件作者联系并请求有关计划将迁移到 NuGet 的信息。
* 调查其他 NuGet 包，或查找的源代码，如果该组件是开放源代码。

迁移到 NuGet，仍在使用多个组件供应商和其他人 （包括地区销售的产品） 可能会调查替代交付选项。


## <a name="related-links"></a>相关链接
- [受欢迎的 Xamarin 插件和库的列表](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [安装和使用 NuGet 包 (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [包括 NuGet 包 (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
