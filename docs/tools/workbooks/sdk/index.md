---
title: Xamarin Workbooks SDK 入门
description: 本文档介绍如何开始使用 Xamarin Workbooks SDK, 该 SDK 可用于开发 Xamarin Workbooks 的集成。
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: dd75270b3b14b0b770808bbc3ffc88240f868eae
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511008"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Xamarin Workbooks SDK 入门

本文档简要介绍了如何开发 Xamarin Workbooks 的集成。 很多这一操作都适用于稳定的 Xamarin Workbooks, 但仅支持在编写时在 alpha 通道中的**工作簿1.3 中加载通过 NuGet 包的集成**。

## <a name="general-overview"></a>一般概述

Xamarin Workbooks 集成是小型库, 使用[ `Xamarin.Workbooks.Integrations` NuGet][nuget] SDK 与 Xamarin Workbooks 和检查器代理集成, 提供增强的体验。

开发集成入门有3个主要步骤-我们将在此概述这些步骤。

## <a name="creating-the-integration-project"></a>创建集成项目

集成库最佳开发为多平台库。 由于你想要在过去和以后的所有可用代理上提供最佳集成, 因此你需要选择一组广泛支持的库。 建议使用 "可移植库" 模板获得最广泛的支持:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![可移植库模板 Visual Studio for Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![可移植库模板 Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

在 Visual Studio 中, 需要确保为可移植库选择以下目标平台:

[![可移植库平台 Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

创建库项目后, 通过 nuget 包管理器添加对`Xamarin.Workbooks.Integration` nuget 库的引用。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![NuGet Visual Studio for Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

您需要删除为您创建的空类作为项目的一部分, 而不需要您这样做。 完成这些步骤后, 便可以开始构建集成了。

## <a name="building-an-integration"></a>构建集成

我们将构建一个简单的集成。 我们非常喜欢绿色, 因此, 我们会将绿色添加为每个对象的表示形式。 首先, 创建一个名`SampleIntegration`为的新类, 并使它实现我们`IAgentIntegration`的接口:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

我们要做的就是为每个绿色颜色的对象添加[表示形式](~/tools/workbooks/sdk/representations.md)。 我们将使用表示提供程序来实现此目的。 提供程序从`RepresentationProvider`类继承-对于我们而言, 只需重写`ProvideRepresentations`:

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

我们将在我们`Color`的 SDK 中返回一个预建表示法类型。
你会注意到, 此处的返回类型是`IEnumerable<object>` &mdash;一个表示提供程序可能会为对象返回很多表示形式! 为每个对象调用所有表示器提供程序, 因此, 不要对要传递给您的对象进行任何假设, 这一点很重要。

最后一步是将我们的提供商实际注册到代理, 并告诉工作簿找到集成类型的位置。 若要注册提供程序, 请将以下代码`IntegrateWith`添加到前面`SampleIntegration`创建的类中的方法:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

设置集成类型是通过程序集范围的属性来完成的。 可以将其放在 AssemblyInfo.cs 中, 也可以在与集成类型相同的类中, 以便于方便:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

在开发过程中, 你可能会发现更方便`AddProvider`的方法`RepresentationManager`是使用上的重载, 以允许你注册一个简单的回调以在工作簿内提供表示形式`RepresentationProvider` , 然后将该代码迁移到你的实现一次您已经完成了。 [`OxyPlot`][oxyplot] 呈现`PlotModel`的示例可能如下所示:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> 借助这些 api, 你可以快速启动和运行, 但我们不建议仅使用这些集成, 只需使用&mdash;这些方法, 只需对客户端处理你的类型的方式提供极小的控制。

注册表示形式后, 你的集成便已准备就绪, 可供发运!

## <a name="shipping-your-integration"></a>交付集成

若要交付集成, 你需要将其添加到 NuGet 包。
可以将其与现有库的 NuGet 一起提供, 或者, 如果要创建新的包, 则可以使用此 nuspec 文件作为起点。
需要填写与集成相关的部分。 最重要的部分是, 集成的所有文件必须位于包根的`xamarin.interactive`目录中。 这使我们可以轻松地找到集成的所有相关文件, 无论你使用的是现有包还是创建新包。

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

创建 nuspec 文件后, 可以打包 NuGet, 如下所示:

```csharp
nuget pack MyIntegration.nuspec
```

然后将其发布到[NuGet][nugetorg]。 完成后, 可以从任何工作簿中引用该工作簿并查看其运行情况。 在下面的屏幕截图中, 我们已打包了本文档中生成的示例集成, 并在工作簿中安装了 NuGet 包:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![具有集成的工作簿](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![具有集成的工作簿](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

请注意, 您看不`#r`到任何指令或任何内容来初始化集成, 工作簿已在幕后完成所有工作。

## <a name="next-steps"></a>后续步骤

请查看我们的其他文档, 以了解有关构成 SDK 的移动部分的详细信息, 以及我们的[示例集成](~/tools/workbooks/samples/index.md), 了解你可以通过集成执行的其他操作, 例如, 提供在工作簿客户端中运行的自定义 JavaScript。

[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[oxyplot]: http://www.oxyplot.org/
