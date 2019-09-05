---
title: 高级集成主题
description: 本文档介绍与 Xamarin Workbooks 集成相关的高级主题。 它讨论了 xamarin 工作簿中的 Xamarin. 集成 NuGet 包和 API 公开。
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: f8105c8285e696f8754799c33c30e31ce5356870
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283882"
---
# <a name="advanced-integration-topics"></a>高级集成主题

集成程序集应引用[ `Xamarin.Workbooks.Integrations` NuGet][nuget]。 有关 NuGet 包入门的详细信息，请查看我们的[快速入门文档](~/tools/workbooks/sdk/index.md)。

此外，还支持客户端集成，并将 JavaScript 或 CSS 文件放在同一目录中，并将其与代理集成程序集同名。 例如，如果代理集成程序集（引用 NuGet）命名`SampleExternalIntegration.dll`为，则`SampleExternalIntegration.js`和`SampleExternalIntegration.css`将集成到客户端（如果存在）。 客户端集成是可选的。

外部集成本身可打包为 NuGet、在托管代理的应用程序中提供和引用，或者只是放置在希望使用它的`.workbook`文件的位置。

在引用包时，将自动加载 NuGet 包中的外部集成（代理和客户端），根据快速入门文档，在与工作簿一起提供的集成程序集需要将其引用为：

```csharp
#r "SampleExternalIntegration.dll"
```

以这种方式引用集成时，客户端将不会立即&mdash;加载它，你将需要从集成调用一些代码以使其加载。 我们将在将来处理此 bug。

`Xamarin.Interactive` PCL 提供了几个重要的集成 api。 每个集成必须至少提供一个集成入口点：

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

此时，一旦引用集成程序集，客户端将隐式加载 JavaScript 和 CSS 集成文件。

## <a name="apis"></a>API

与工作簿或实时检查会话所引用的任何程序集一样，该会话可以访问其任何公共 Api。 因此，为用户提供一个安全且明智的 API 图面是非常重要的。

集成程序集实际上是应用程序或目标 SDK 与会话之间的桥梁。 它可以提供特别适用于工作簿或实时检查会话上下文的新 Api，或者不提供公共 Api，只需执行 "幕后" 任务（如生成对象[表示形式](~/tools/workbooks/sdk/representations.md)）。

> [!NOTE]
> 必须是公共的且不应通过 IntelliSense 显示的 api 可以用常规`[EditorBrowsable (EditorBrowsableState.Never)]`属性进行标记。

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
