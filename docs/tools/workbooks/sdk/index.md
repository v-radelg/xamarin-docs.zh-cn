---
title: 开始使用 Xamarin 工作簿 SDK
description: 本文档介绍如何开始使用 Xamarin 工作簿 SDK，可用于开发的 Xamarin 工作簿的集成。
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 5800e98acbff147735ae4a6125979a4b47be2367
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382672"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>开始使用 Xamarin 工作簿 SDK

本文档提供了开发 Xamarin Workbooks 针对集成入门的快速指南。 其中的大部分将使用稳定 Xamarin 工作簿，但**加载通过 NuGet 包的集成仅支持在工作簿 1.3 中**，在撰写本文的 alpha 通道中。

## <a name="general-overview"></a>常规概述

Xamarin Workbooks 的集成是使用的小型库[ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK 与 Xamarin 工作簿和检查器集成代理程序以提供增强的体验。

有 3 个主要步骤开发集成入门，我们将在此处介绍它们。

## <a name="creating-the-integration-project"></a>创建集成项目

集成库最好是作为多平台库开发的。 因为你想要提供有关所有可用的代理、 过去和未来的最佳集成，你将想要选择一组广泛支持的库。 我们建议使用最广泛的支持"可移植库"模板：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![可移植库模板 Visual Studio for Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![可移植库模板的 Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

在 Visual Studio 中，你将想要确保可移植库中选择以下目标平台：

[![可移植库平台的 Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

一旦创建类库项目，添加对的引用我们`Xamarin.Workbooks.Integration`NuGet 库通过 NuGet 程序包管理器。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![NuGet Visual Studio for Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

你将想要删除作为项目的一部分创建的空类 — 您不需要使用它对此。 完成这些步骤后，你准备好开始构建你的集成。

## <a name="building-an-integration"></a>生成集成

我们将构建简单的集成。 我们非常喜欢颜色绿色，因此我们将为每个对象的表示形式，添加将绿色。 首先，创建一个名为的新类`SampleIntegration`，并使其实现我们[ `IAgentIntegration` ] [ integration-type]接口：

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

我们想要执行操作是添加[表示形式](~/tools/workbooks/sdk/representations.md)为每个对象的颜色为绿色。 我们将使用的表示形式提供此操作。 提供程序继承自[ `RepresentationProvider` ] [ reppr]类 — 对于我们的愿景，我们只需重写[ `ProvideRepresentations` ] [ prrep]:

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

将返回[ `Color` ] [ color]、 预生成我们的 SDK 中的表示形式类型。
您会注意到，此处的返回类型是`IEnumerable<object>`&mdash;一个表示形式提供程序可能会返回多个表示形式的对象 ！ 因此，必须以不进行任何假设哪些对象传递给您为每个对象，调用表示形式的所有提供程序。

最后一步是实际向代理注册我们的提供程序，并告知在哪里可以找到我们集成类型的工作簿。 若要注册提供程序，将添加到此代码`IntegrateWith`中的方法`SampleIntegration`我们之前创建的类：

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

设置集成类型是通过程序集级属性。 在 AssemblyInfo.cs 或方便你集成类型与相同的类中，可以将此：

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

在开发期间，可能会更方便地使用[`AddProvider`重载][ addprovider]上`RepresentationManager`，使用户能够注册一个简单的回调，以提供工作簿内的表示形式然后将移动到该代码在`RepresentationProvider`完后的实现。 一个示例，用于呈现[ `OxyPlot` ] [ oxyplot] `PlotModel`可能如下所示：

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> 这些 Api 可以快速启动并运行，但我们不会建议只使用了它们传送整个集成&mdash;它们提供很少控制在客户端如何处理您的类型。

已注册的表示形式，与你的集成已准备好交付 ！

## <a name="shipping-your-integration"></a>传送你的集成

若要提供你的集成，需要将其添加到 NuGet 包。
可以将其送与现有库的 NuGet，或如果要创建新的包，您可以使用此模板.nuspec 文件作为起始点。
你将需要填写你的集成与相关部分。 最重要的部分是，所有集成的文件必须为`xamarin.interactive`目录的包根目录下。 这使我们能够轻松地找到你的集成，而不考虑是否使用现有包或创建一个新相关的所有文件。

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

创建.nuspec 文件后，能够打包 NuGet 如下所示：

```csharp
nuget pack MyIntegration.nuspec
```

然后将其向发布[NuGet][nugetorg]。 后存在，您将能够从任何工作簿中引用它并观察它的操作。 在下面的屏幕截图，我们已经打包我们在本文档中生成和安装 NuGet 包的工作簿中的示例集成：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![使用集成的工作簿](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![使用集成的工作簿](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

请注意，你未看到任何`#r`指令或任何内容来初始化集成-工作簿已处理所有这些为您在后台 ！

## <a name="next-steps"></a>后续步骤

请查看我们有关移动片段组成该 SDK 的详细信息的其他文档和我们[示例集成](~/tools/workbooks/samples/index.md)有关您可以从你的集成，例如，提供自定义运行中的 JavaScript 执行的其他事项工作簿的客户端。

[integration-type]: https://developer.xamarin.com/api/type/Xamarin.Interactive.IAgentIntegration/
[repman-api]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[xir]: https://developer.xamarin.com/api/namespace/Xamarin.Interactive.Representations/
[reppr]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[prrep]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.RepresentationProvider.ProvideRepresentations/p/System.Object/
[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[addprovider]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.IRepresentationManager.AddProvider/
[oxyplot]: http://www.oxyplot.org/
