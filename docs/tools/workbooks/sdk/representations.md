---
title: Xamarin 工作簿中的表示形式
description: 本文档介绍 Xamarin Workbooks 表示管道，它能够为返回值的任何代码呈现丰富的结果。
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: dde4e6b9c4903ccb0f23d8df82f39ff68030850e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292827"
---
# <a name="representations-in-xamarin-workbooks"></a>Xamarin 工作簿中的表示形式

## <a name="representations"></a>外观

在工作簿或检查器会话中，执行并生成结果的代码（例如返回值的方法或表达式的结果）将通过代理中的表示管道进行处理。 除整数以外的所有对象都将被反射，以生成交互式成员关系图，并将通过进程来提供客户端可以呈现更丰富的替代表示形式。 由于延迟和交互式反射和远程处理，任何大小和深度的对象都受到安全支持（包括循环和无限枚举）。

Xamarin Workbooks 提供了几种适用于所有代理和客户端的通用类型，这些类型允许丰富的结果呈现。 `Color`这种类型的一个示例，其中例如，在 iOS 上，代理负责将或`CGColor` `UIColor`对象转换为`Xamarin.Interactive.Representations.Color`对象。

除了常见的表示形式以外，集成 SDK 还提供了 Api，用于在客户端的代理和呈现表示形式中序列化自定义表示形式。

## <a name="external-representations"></a>外部表示形式

`Xamarin.Interactive.IAgent.RepresentationManager`提供注册的`RepresentationProvider`功能，集成必须实现该功能才能从任意对象转换为不可知的形式以呈现。 这些不可知的`ISerializableObject`形式必须实现接口。

`ISerializableObject`实现接口将添加一个序列化方法，该方法可精确地控制对象的序列化方式。 `Serialize`方法要求开发人员准确指定要序列化的属性以及最终的名称。 查看 [ `Person` `KitchenSink`示例] [示例] 中的对象，可以看到它的工作原理：

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

如果要从原始对象提供属性的超集或子集，则可以使用`Serialize`来执行此操作。 例如，我们可能会执行类似于下面的内容来提供的预`Age`计算`Person`属性：

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
> 直接生成`ISerializableObject`对象的 api 无需`RepresentationProvider`由进行处理。 如果要显示的对象**不** `ISerializableObject`是，则需要在中`RepresentationProvider`对其进行处理。

### <a name="rendering-a-representation"></a>呈现表示形式

呈现器在 JavaScript 中实现，并将可以访问通过`ISerializableObject`所表示的对象的 JavaScript 版本。 JavaScript 副本还将具有`$type`指示 .net 类型名称的字符串属性。

建议为客户端集成代码使用 TypeScript，这当然会将其编译为 vanilla JavaScript。 无论采用哪种方式，SDK 都提供[typings][typings] ，可通过 TypeScript 直接对其进行引用，或者只是在首选 vanilla JavaScript 时直接引用。

用于呈现的主要集成点是`xamarin.interactive.RendererRegistry`：

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

此处， `PersonRenderer` `Renderer`实现接口。 有关更多详细信息，请参阅[typings][typings] 。

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
