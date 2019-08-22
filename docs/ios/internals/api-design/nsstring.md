---
title: Xamarin 和 Xamarin 中的 NSString
description: 本文档介绍当不发生此情况时, Xamarin 如何C#以透明方式将 NSString 对象转换为字符串对象。
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 589b584e0526ffdc56dd5ec26aa25a0b61e2141a
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889893"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>Xamarin 和 Xamarin 中的 NSString

Xamarin 和 xamarin 的设计都调用使用 API 来公开本机 .net 字符串类型`string`, 为中C#的字符串操作和其他 .net 编程语言公开, 并将字符串公开为 API 公开的数据类型, 而不是 `NSString`数据类型 。

这意味着开发人员无需保留用于在特殊类型 (`Foundation.NSString`) 中调用 xamarin & xamarin API (统一) 的字符串, 而是可以将`System.String` Mono 用于所有操作, 并在每次Xamarin 或 Xamarin 中的 API 需要字符串, 我们的 API 绑定负责封送处理信息。

例如, 类型`NSString`为的目标为 C 的 "text" 属性`UILabel`的声明如下所示:

```objc
@property(nonatomic, copy) NSString *text
```

这在 Xamarin 中公开为:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在后台, 此属性的实现将C#字符串封送到`NSString` , 并以与目标 C 相同的方式调用`objc_msgSend`方法。

有少量的第三方目标 C api 不使用`NSString`, 而是使用 C 字符串 ("*char*")。 在这些情况下, 你仍可以使用C#字符串数据类型, 但你必须使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)属性来通知绑定生成器`NSString`, 此字符串不应作为而作为 C 字符串进行封送处理。

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>规则的例外情况

在 Xamarin 和 Xamarin 中, 我们已对此规则进行了例外。  `string` `NSString`如果该 `NSString`方法可能执行指针比较而不是内容比较, 则当我们公开时, 以及当我们进行除和公开时, 会作出决定 。

当目标 C api 使用公共 `NSString` 常量作为表示某种操作的令牌, 而不是比较字符串的实际内容时, 就会发生这种情况。

在这些情况下`NSString`,  将公开 api, 其中包含少数 api。 你还会注意到, 在某些类中公开了 NSString 属性。 这些`NSString`属性对通知等项目公开。 这些属性通常如下所示:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

当您想要注册由运行时`NSNotification`广播的特定事件时, 通知是用于类的键。

密钥通常类似于:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

在 API 中`NSString`公开的另一个位置是作为标记, 用作在 iOS 或 OS X `NSDictionary`中将对象作为参数的某些 api 的参数。 字典通常包含`NSString`键。 根据约定, Xamarin 通过添加 "键" 名称`NSString`命名这些静态属性。
