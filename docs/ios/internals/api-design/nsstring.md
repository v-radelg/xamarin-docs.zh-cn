---
title: Xamarin 和 Xamarin 中的 NSString
description: 本文档介绍当不发生此情况时，Xamarin 如何C#以透明方式将 NSString 对象转换为字符串对象。
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022351"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>Xamarin 和 Xamarin 中的 NSString

Xamarin 和 Xamarin 的设计都调用使用 API 来公开本机 .NET 字符串类型（`string`），以便为中C#的字符串操作和其他 .net 编程语言公开字符串，并将字符串公开为 API 公开的数据类型，而不是 数据类型 `NSString`。

这意味着开发人员无需保留用于在特殊类型（`Foundation.NSString`）中调用 Xamarin & Xamarin API （统一）的字符串，而是可以继续使用 Mono 的 `System.String` 执行所有操作，并在每次Xamarin 或 Xamarin 需要字符串，API 绑定负责封送处理信息。

例如，类型为 `NSString`的 `UILabel` 上的 "目标 C" 文本 "属性声明如下所示：

```objc
@property(nonatomic, copy) NSString *text
```

这在 Xamarin 中公开为：

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在后台，此属性的实现将C#字符串封送到`NSString`中，并以与目标 C 相同的方式调用`objc_msgSend`方法。

有少量的第三方目标 C Api 不使用 `NSString`，而是使用 C 字符串（"*char*"）。 在这些情况下，你仍可以使用C#字符串数据类型，但你必须使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)属性来通知绑定生成器，不应将此字符串作为`NSString`封送，而应改为 C 字符串。

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>规则的例外情况

在 Xamarin 和 Xamarin 中，我们已对此规则进行了例外。 如果 `NSString` 方法可以执行指针比较而不是内容比较，则当我们公开 `string`时，以及当我们进行除和公开 `NSString`时进行的决策。

当目标 C Api 使用公共 `NSString` 常数作为表示某个操作的标记，而不是比较字符串的实际内容时，就会发生这种情况。

在这些情况下，将公开 `NSString` Api，并且有少数 Api 具有此项。 你还会注意到，在某些类中公开了 NSString 属性。 对于通知之类的项目，这些 `NSString` 属性都是公开的。 这些属性通常如下所示：

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

当您想要注册由运行时广播的特定事件时，通知是用于 `NSNotification` 类的键。

密钥通常类似于：

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

在 API 中公开 `NSString`的另一个位置是作为令牌，用作在 iOS 或 OS X 中将 `NSDictionary` 对象作为参数的某些 Api 的参数。 字典通常包含 `NSString` 键。 根据约定，Xamarin 通过添加 "键" 名称命名静态 `NSString` 属性。
