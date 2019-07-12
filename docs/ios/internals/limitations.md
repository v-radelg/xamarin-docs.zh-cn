---
title: Xamarin.iOS 的限制
description: 本文档介绍 Xamarin.iOS，讨论泛型、 NSObjects、 P/Invoke 中的通用对象，和的详细信息的通用子类的限制。
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/09/2018
ms.openlocfilehash: 86f41bf227b97ef24651f482ff133a065226ee14
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830391"
---
# <a name="limitations-of-xamarinios"></a>Xamarin.iOS 的限制

使用 Xamarin.iOS 应用程序编译为静态代码，因为它不能使用任何设备，需要在运行时代码生成。

与桌面 Mono 相比，Xamarin.iOS 有以下限制：

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>有限的泛型的支持

与传统的 Mono/.NET 不同，iPhone 上的代码是提前静态编译的，而不是由 JIT 编译器按需编译。

Mono 的 [Full AOT](https://www.mono-project.com/docs/advanced/aot/#full-aot)技术在泛型方面有一些限制，这是因为并非所有可能的泛型实例化都可以在编译时预先确定。 这对于常规的 .NET 或 Mono 运行时来说不是问题，因为代码总是在运行时使用 Just in Time 编译器进行编译。 但这对像 Xamarin.iOS 这样的静态编译器提出了挑战。

一些开发人员遇到的常见问题包括：

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>NSObject 的泛型子类受限

Xamarin.iOS 目前对创建 NSObject 类的泛型子类的支持有限，例如，不支持泛型方法。 从 7.2.1 开始，可以使用 NSObjects 的泛型子类，如下所示：

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> 虽然可以使用 NSObject 的泛型子类，但存在一些限制。 请参阅 [NSObject 的泛型子类](~/ios/internals/api-design/nsobject-generics.md)文档以获取更多信息


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>没有生成动态代码

因为 iOS 内核会阻止从动态生成代码的应用程序，Xamarin.iOS 不支持任何形式的动态代码生成。 这些问题包括：

-  System.Reflection.Emit 不可用。
-  System.Runtime.Remoting 不支持。
-  不支持动态创建类型（没有 Type.GetType（“MyType`1”）），但查找现有类型（例如 Type.GetType（“System.String”）仍有效）。 
-  在编译时，必须向运行时注册反向回调。


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

System.Reflection 缺乏。 **发出**取决于运行时代码生成任何代码将起作用的方式。 这包括诸如：

-  动态语言运行时。
-  任何基于动态语言运行时的语言。
-  远程处理的 TransparentProxy 或任何其他会导致运行时动态生成代码的内容。 


  > [!IMPORTANT]
  > 请不要将 **Reflection.Emit** 与 **Reflection** 混淆。 Reflection.Emit 涉及动态生成代码并将代码即时编译为本机代码。 由于对 iOS （无 JIT 编译） 的限制不是支持此。

但整个反射 API（包括 Type.GetType（“someClass”）、列出方法、列出属性、获取属性和值）均可以正常工作。

### <a name="using-delegates-to-call-native-functions"></a>使用委托来调用本机函数

若要通过 C# 委托调用本机函数，必须使用以下属性之一修饰委托的声明：

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) （首选，因为该属性跨平台并与 .NET Standard 1.1+ 兼容）
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

如果这些属性中有任何一个未能提供，将导致运行时错误，例如：

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>反向回调

在标准 Mono 中，可以将 C# 委托实例传递给非托管代码，而不是函数指针。 运行时通常会将这些函数指针转换为允许非托管代码回调到托管代码的小 thunk。

在 Mono 中，这些桥由实时编译器实现。 在使用 iPhone 所需的提前编译器时，则有两个重要限制：

-  必须使用 [MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute) 标记所有回调方法
-  方法必须是静态方法，不支持实例方法。
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>没有远程处理

远程处理堆栈不可用在 Xamarin.iOS 上。


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>运行时禁用功能

已在 Mono 的 iOS 运行时中禁用以下功能：

-  探查器
-  Reflection.Emit
-  Reflection.Emit.Save 功能
-  COM 绑定
-  JIT 引擎
-  元数据验证程序 （因为并无 JIT）


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>.NET API 限制

公开的 .NET API 是完整框架的一个子集，因为并非所有内容在 iOS 中都是可用的。 请参阅常见问题解答，获取[当前支持的程序集列表](~/cross-platform/internals/available-assemblies.md)。



特别是，Xamarin.iOS 使用的 API 配置文件不包含 System.Configuration ，因此无法使用外部 XML 文件来配置运行时的行为。
