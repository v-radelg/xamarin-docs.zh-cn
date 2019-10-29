---
title: Xamarin 的限制
description: 本文档介绍了 Xamarin 的限制，并讨论了泛型、NSObjects 的泛型子类、泛型对象中的 P/Invoke 等。
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/09/2018
ms.openlocfilehash: 91513936a0223af0e4220154d0fe65ee0a599a4f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022312"
---
# <a name="limitations-of-xamarinios"></a>Xamarin 的限制

由于使用 Xamarin 的应用程序编译为静态代码，因此不能使用在运行时需要代码生成的任何功能。

与桌面 Mono 相比，这些是 Xamarin iOS 的限制：

 <a name="Limited_Generics_Support" />

## <a name="limited-generics-support"></a>有限的泛型支持

与传统的 Mono/.NET 不同，iPhone 上的代码会提前进行静态编译，而不是由 JIT 编译器按需编译。

Mono 的[完整 AOT](https://www.mono-project.com/docs/advanced/aot/#full-aot)技术对于泛型具有几个限制，这是因为并非每个可能的泛型实例化在编译时都是预先确定的。 对于常规 .NET 或 Mono 运行时，这并不是问题，因为代码始终在运行时使用实时编译器进行编译。 但这对诸如 Xamarin 这类静态编译器是一项挑战。

开发人员遇到的一些常见问题包括：

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />

### <a name="generic-subclasses-of-nsobjects-are-limited"></a>NSObjects 的泛型子类受到限制

Xamarin 目前支持创建 NSObject 类的泛型子类，如不支持泛型方法。 在7.2.1 中，可以使用 NSObjects 的泛型子类，如下所示：

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> 虽然 NSObjects 的泛型子类是可行的，但有一些限制。 有关详细信息，请参阅 NSObject 文档的[一般子类](~/ios/internals/api-design/nsobject-generics.md)

 <a name="No_Dynamic_Code_Generation" />

## <a name="no-dynamic-code-generation"></a>无动态代码生成

由于 iOS 内核阻止应用程序动态生成代码，因此，Xamarin 不支持任何形式的动态代码生成。 这些问题包括：

- 无法获取 System.object。
- 不支持 System.object。
- 不支持动态创建类型（不支持类型类型（"MyType" 1 "）），尽管查找现有类型（类型为 GetType （" System.string "））。
- 在编译时，必须向运行时注册反向回调。

 <a name="System.Reflection.Emit" />

### <a name="systemreflectionemit"></a>System.Reflection.Emit

缺少 System.object。 **发出**表示没有依赖于运行时代码生成的代码。 这包括：

- 动态语言运行时。
- 在动态语言运行时之上构建的任何语言。
- 远程处理的 TransparentProxy 或任何其他操作会导致运行时动态生成代码。

  > [!IMPORTANT]
  > 不要混淆**反射。** 通过**反射**发出。 反射是指动态生成代码，并使该代码 JITed 并编译为本机代码。 由于 iOS 的限制（无 JIT 编译），这不受支持。

但整个反射 API （包括类型 GetType （"someClass"）、列出方法、列出属性、提取属性和值）都是正常的。

### <a name="using-delegates-to-call-native-functions"></a>使用委托调用本机函数

若要通过C#委托调用本机函数，必须使用以下属性之一修饰委托的声明：

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) （首选，因为它是跨平台的并且与 .NET Standard 1.1 + 兼容）
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

未能提供其中一个属性将导致运行时错误，例如：

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```

 <a name="Reverse_Callbacks" />

### <a name="reverse-callbacks"></a>反向回调

在标准 Mono 中，可以将委托C#实例以替代函数指针的方法传递到非托管代码。 运行时通常会将这些函数指针转换为小型 thunk，以允许非托管代码回调到托管代码。

在 Mono 中，这些桥由实时编译器实现。 使用 iPhone 所需的预先编译的编译器时，目前有两个重要限制：

- 必须用[MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute)标记所有的回调方法
- 这些方法必须是静态方法，但不支持实例方法。

<a name="No_Remoting" />

## <a name="no-remoting"></a>无远程处理

远程处理堆栈在 Xamarin 上不可用。

 <a name="Runtime_Disabled_Features" />

## <a name="runtime-disabled-features"></a>运行时禁用的功能

Mono 的 iOS 运行时中禁用了以下功能：

- 探查器
- 反射。发出
- 反射。保存功能
- COM 绑定
- JIT 引擎
- 元数据验证程序（因为没有 JIT）

 <a name=".NET_API_Limitations" />

## <a name="net-api-limitations"></a>.NET API 限制

公开的 .NET API 是完整框架的子集，因为 iOS 中不提供所有功能。 有关[当前支持的程序集的列表](~/cross-platform/internals/available-assemblies.md)，请参阅常见问题解答。

具体而言，Xamarin 使用的 API 配置文件不包括 System.web，因此不能使用外部 XML 文件来配置运行时的行为。
