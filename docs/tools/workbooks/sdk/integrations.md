---
title: 高级集成主题
description: 本文档介绍适用于 Xamarin Workbooks 集成相关的高级的主题。 它讨论 Xamarin.Workbook.Integrations NuGet 包和 Xamarin 工作簿中的 API 公开。
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 56ee709b78b8587c2717dc9d25a6357041812d23
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382240"
---
# <a name="advanced-integration-topics"></a>高级集成主题

集成程序集应引用[ `Xamarin.Workbooks.Integrations` NuGet][nuget]。 请查看我们[快速入门文档](~/tools/workbooks/sdk/index.md)有关如何开始使用 NuGet 包的详细信息。

也支持的客户端集成和由代理集成程序集与同名的 JavaScript 或 CSS 文件放置在同一目录中启动。 例如，如果名为代理集成程序集 （即在引用 NuGet） `SampleExternalIntegration.dll`，然后`SampleExternalIntegration.js`和`SampleExternalIntegration.css`将集成到客户端中，如果它们存在。 客户端集成是可选的。

外部集成本身可以打包为 NuGet、 提供和托管代理，或只需并排放置的应用程序内直接引用`.workbook`想要使用它的文件。

包引用时，根据快速入门文档，而该工作簿随附的集成程序集将需要对其进行引用，因此，将自动加载的 NuGet 包中的外部集成 （代理和客户端）：

```csharp
#r "SampleExternalIntegration.dll"
```

引用时集成这样一来，它将不加载的客户端立即&mdash;将需要从集成，使其加载调用一些代码。 我们将在此探讨此 bug 在将来。

`Xamarin.Interactive` PCL 提供了几个重要的集成 Api。 每个集成必须至少提供一个集成入口点：

```csharp
using Xamarin.Interactive;

[assembly: AgentIntegration (typeof (AgentIntegration))]

class AgentIntegration : IAgentIntegration
{
    const string TAG = nameof (AgentIntegration);

    public void IntegrateWith (IAgent agent)
    {
        // hook into IAgent APIs
    }
}
```

此时，一旦引用集成程序集，则客户端将隐式加载集成的 JavaScript 和 CSS 文件。

## <a name="apis"></a>API

与任何程序集所引用的工作簿或实时检查会话，因为任何其公共 Api 都可以访问该会话。 因此，它是重要安全又合理的 API 外围应用，用户可以浏览。

集成程序集实际上是应用程序或所需的 SDK 和会话之间的桥梁。 它可以提供有意义的工作簿或实时的上下文中专门检查会话中，或提供任何公共 Api，只需执行"后台"任务，例如生成对象的新 Api[表示形式](~/tools/workbooks/sdk/representations.md)。

> [!NOTE]
> 可以使用常用标记 Api 这必须是公共的但不是应通过 IntelliSense 显示`[EditorBrowsable (EditorBrowsableState.Never)]`属性。

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
