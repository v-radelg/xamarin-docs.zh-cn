---
title: Xamarin.iOS 的限制
description: 本文档介绍 Xamarin.iOS，讨论泛型、 NSObjects、 P/Invoke 中的通用对象，和的详细信息的通用子类的限制。
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/09/2018
ms.openlocfilehash: bb8aec5a5054c28cf7862d14148e7f2000fa3a35
ms.sourcegitcommit: c77f84a0686d16de6ac630271fccac719fd9eec4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56307913"
---
# <a name="limitations-of-xamarinios"></a>Xamarin.iOS 的限制

使用 Xamarin.iOS 应用程序编译为静态代码，因为它不能使用任何设备，需要在运行时代码生成。

这些是与 Mono 桌面相比的 Xamarin.iOS 限制：

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>有限的泛型的支持

与传统的 Mono/.NET，而不是按需由 JIT 编译器正在编译提前静态编译在 iPhone 上的代码。

Mono[完整 AOT](http://www.mono-project.com/docs/advanced/aot/#full-aot)技术具有与泛型相关的一些限制，因为在编译时提前确定不是每个可能的泛型实例化会造成这些错误。 这不是常规的.NET 或 Mono 运行时的问题，因为始终在运行时实时编译器中使用实时编译代码。 但这会带来 Xamarin.iOS 等静态编译器所面临的难题。

一些开发人员遇到的常见问题包括：

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>泛型 NSObjects 子类将受到限制

Xamarin.iOS 目前提供有限支持用于创建 NSObject 类，如为泛型方法不支持的通用子类。 截至 7.2.1，使用 NSObjects 的通用子类是可能的类似如下：

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> 可能的 NSObjects 通用子类时，有一些限制。 读取[NSObject 的通用子类](~/ios/internals/api-design/nsobject-generics.md)文档的详细信息


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>没有生成动态代码

因为 iOS 内核会阻止从动态生成代码的应用程序，Xamarin.iOS 不支持任何形式的动态代码生成。 这些问题包括：

-  System.Reflection.Emit 不可用。
-  System.Runtime.Remoting 不支持。
-  不支持动态创建类型 (没有 Type.GetType ("MyType"1"))，尽管查找现有类型 (Type.GetType ("System.String") 例如，可以很好地工作)。 
-  在编译时，必须向运行时注册反向回调。


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

System.Reflection 缺乏。 **发出**取决于运行时代码生成任何代码将起作用的方式。 这包括诸如：

-  动态语言运行时。
-  任何基于动态语言运行时的语言。
-  远程处理的 TransparentProxy 或任何其他会导致运行时动态生成代码的内容。 


 **重要提示：** 不要混淆**Reflection.Emit**与**反射**。 Reflection.Emit 即将动态生成代码，并且具有该代码执行 Jit 和编译为本机代码。 由于对 iOS （无 JIT 编译） 的限制不是支持此。

但整个反射 API，包括 Type.GetType ("someClass") 列表的方法，列出属性，提取属性和值会顺利运行。

### <a name="using-delegates-to-call-native-functions"></a>使用委托来调用本机函数

若要调用通过 C# 委托的本机函数，必须使用以下属性之一修饰委托的声明：

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) （首选，因为它是跨平台和兼容.NET Standard 1.1 +）
- [MonoNativeFunctionWrapperAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoNativeFunctionWrapperAttribute)

无法提供这些属性之一将导致运行时错误，例如：

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>反向回调

在标准 Mono 很可能将传递给非托管代码而不是函数指针的 C# 委托实例。 运行时通常会将这些函数指针转换为一个小 thunk，用于允许回调到托管代码的非托管的代码。

在 Mono 中这些桥实现实时中由编译器。 当使用预的实时编译器所需 iPhone 现在有两个重要限制：

-  您必须标记所有回叫方法与[MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute) 
-  方法必须是静态方法，则不支持实例方法。 
 
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

公开的.NET API 是完整框架的子集，因为并非所有内容都是在 iOS 中可用。 请参阅常见问题解答了解[的当前支持的程序集列表](~/cross-platform/internals/available-assemblies.md)。



具体而言，使用 Xamarin.iOS 的 API 配置文件不包括 System.Configuration，因此不能使用外部 XML 文件来配置运行时的行为。
