---
title: Xamarin 工作簿中的表示形式
description: 本文档介绍了 Xamarin Workbooks 表示管道中，可以返回一个值，任何代码的丰富结果呈现。
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: d9aafbe13e06875b6577a4d2308e419932fd1589
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382108"
---
# <a name="representations-in-xamarin-workbooks"></a>Xamarin 工作簿中的表示形式

## <a name="representations"></a>表示形式

在工作簿或检查器会话中，通过在代理表示管道处理代码的执行并生成结果 （例如方法返回一个值或表达式的结果）。 所有对象，如整数、 基元除外将反射以生成交互式成员关系图并将完成一个过程来提供客户端可以呈现具有更丰富的替代表示。 （包括周期和无限的可枚举对象），任何大小和深度的对象安全地由于延迟和交互式反射和远程处理支持。

Xamarin 工作簿提供几种常见类型到所有代理和客户端，便于丰富呈现的结果。 [`Color`][xir-color] 是这种类型的一个示例，例如在 iOS 上，代理负责将转换`CGColor`或`UIColor`将对象插入`Xamarin.Interactive.Representations.Color`对象。

除了常见表示形式，集成 SDK 提供用于序列化自定义代理中的表示形式和呈现客户端中的表示形式的 Api。

## <a name="external-representations"></a>外部表示形式

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] 提供注册的能力 [`RepresentationProvider`][repp]，集成必须实现从任意对象转换为以不可知的形式呈现该。 这些不可知的窗体必须实现 [`ISerializableObject`][serobj] 接口。

实现`ISerializableObject`接口将精确地控制如何序列化对象的序列化方法。 `Serialize`方法需要开发人员将完全指定哪些属性是要序列化，并且最终名称将是。 看一下`Person`对象中我们 [`KitchenSink`示例] [示例]，则我们可以看到这的工作原理：

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

如果我们想要提供的超集或子集从原始对象的属性，为此，我们使用`Serialize`。 例如，我们可能会执行如下操作提供预计算`Age`属性上的`Person`:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> 生成的 Api`ISerializableObject`对象直接无需由`RepresentationProvider`。 你想要显示的对象是否**不** `ISerializableObject`，想要处理将在其包装在`RepresentationProvider`。

### <a name="rendering-a-representation"></a>呈现表示形式

呈现器在 JavaScript 中实现，并将有权通过所表示的对象的 JavaScript 版本`ISerializableObject`。 JavaScript 副本也会`$type`字符串指示的.NET 类型名称的属性。

我们建议为客户端集成代码，当然编译为传统的 JavaScript 中使用 TypeScript。 无论哪种方式，该 SDK 提供 [typings][typings] 它们可以直接引用 TypeScript 或简称为手动如果编写香草 JavaScript 首选。

呈现的主要集成点是`xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

在这里，`PersonRenderer`实现`Renderer`接口。 请参阅 [typings][typings] 有关详细信息。

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
