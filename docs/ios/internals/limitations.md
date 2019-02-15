---
title: Xamarin.iOS 的限制
description: 本文档介绍 Xamarin.iOS，讨论泛型、 NSObjects、 P/Invoke 中的通用对象，和的详细信息的通用子类的限制。
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/09/2018
ms.openlocfilehash: 1ccbea1921b4e0c4189182696c8679d041eea60b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113022"
---
# <a name="limitations-of-xamarinios"></a>Xamarin.iOS 的限制

由于在 iPhone 上使用 Xamarin.iOS 开发的应用程序会被编译为静态代码，因此任何需要在运行时生成代码的机制都无法使用。

与桌面 Mono 相比，Xamarin.iOS 有以下限制：

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>有限的泛型的支持

与传统的 Mono/.NET 不同，iPhone 上的代码是提前静态编译的，而不是由 JIT 编译器按需编译。

Mono的[ Full AOT ](http://www.mono-project.com/docs/advanced/aot/#full-aot)技术在泛型方面有一些限制，这是因为并非所有可能的泛型实例都可以在编译时预先确定。 这对于常规的 .NET 或 Mono 运行时来说不是问题，因为代码总是在运行时使用 Just in Time 编译器进行编译。 但这对像 Xamarin.iOS 这样的静态编译器提出了挑战。

一些开发人员遇到的常见问题包括：

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a> NSObject 的泛型子类的限制

Xamarin.iOS 目前对创建 NSObject 类的泛型子类的支持有限，例如不支持泛型方法。 从7.2.1开始，可以使用NSObjects的泛型子类，如下所示：

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> 虽然NSObject 的泛型子类是可能的，但存在一些限制。 阅读[NSObject 的泛型子类](~/ios/internals/api-design/nsobject-generics.md)文档以获取更多信息



### <a name="pinvokes-in-generic-types"></a>泛型类型中的 P/Invoke 

不支持泛型类中的 P / Invokes：

```csharp
class GenericType<T> {
    [DllImport ("System")]
    public static extern int getpid ();
}
```

 <a name="Property.SetInfo_on_a_Nullable_Type_is_not_supported" />


### <a name="propertysetinfo-on-a-nullable-type-is-not-supported"></a>不支持 Nullable 类型的 Property.SetInfo

目前不支持使用反射的Property.SetInfo来设置Nullable <T>上的值。

 <a name="Value_types_as_Dictionary_Keys" />


### <a name="value-types-as-dictionary-keys"></a>作为字典键的值类型

使用值类型作为Dictionary <TKey，TValue>键是有问题的，因为默认的 Dictionary 构造函数尝试使用 EqualityComparer <TKey> .Default。 EqualityComparer <TKey> .Default 反过来尝试使用反射来实例化实现 IEqualityComparer <TKey> 接口的新类型。

这适用于引用类型（因为跳过了反射+创建新类型的步骤），但是对于值类型，一旦您尝试在设备上使用它，它就会很快崩溃。

 **解决方法**：在新类型中手动实现[ IEqualityComparer&lt;TKey&gt; ](xref:System.Collections.Generic.IEqualityComparer`1)接口，并向[Dictionary &lt;TKey，TValue&gt; ](xref:System.Collections.Generic.Dictionary`2)构造函数提供该类型的实例。


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>没有生成动态代码

由于 iPhone 的内核阻止应用程序动态生成代码，因此 iPhone上 的 Mono 不支持任何形式的动态代码生成。 这些包括：

-  System.Reflection.Emit 不可用。
-  System.Runtime.Remoting 不支持。
-  不支持动态创建类型 (没有 Type.GetType ("MyType"1"))，尽管查找现有类型，例如 (Type.GetType ("System.String") ，可以很好地工作)。 
-  在编译时，必须向运行时注册反向回调。


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

缺少 System.Reflection.**Emit** 意味着依赖于运行时代码生成的代码将不会工作。 这包括以下内容：

-  动态语言运行时。
-  任何基于动态语言运行时的语言。
-  远程处理的 TransparentProxy 或任何其他会导致运行时动态生成代码的内容。 


 **重要提示：** 不要将**Reflection.Emit**与**Reflection**混淆。 Reflection.Emit 是关于动态生成代码并将代码即时编译为本机代码。 由于 iPhone 的限制（没有 JIT 编译），因此不支持此功能。

但是整个 Reflection API，包括 Type.GetType（“someClass”）、列出方法、列出属性、获取属性和值都可以正常工作。

### <a name="using-delegates-to-call-native-functions"></a>使用委托来调用本机函数

要通过 C# 委托调用本机函数，必须使用以下属性之一修饰委托的声明：

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) （首选，因为它跨平台并且兼容.NET Standard 1.1 +）
- [MonoNativeFunctionWrapperAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoNativeFunctionWrapperAttribute)

任何一个属性都未能提供将导致运行时错误，例如：

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>反向回调

在标准 Mono 中，可以将 C# 委托实例传递给非托管代码，而不是函数指针。 运行时通常会将这些函数指针转换为允许非托管代码回调到托管代码的小 thunk。

在 Mono 中，这些桥由即时编译器实现。 当使用 iPhone 所需的提前编译器时，此时有两个重要的限制：

-  您必须使用[MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/ObjCRuntime.MonoPInvokeCallbackAttribute)标记所有回调方法
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

公开的.NET API 是完整框架的一个子集，因为并非所有内容都是在 iOS 中可用。 请参阅常见问题解答了解[当前支持的程序集列表](~/cross-platform/internals/available-assemblies.md)。



特别是，Xamarin.iOS 使用的 API 配置文件不包含 System.Configuration ，因此无法使用外部 XML 文件来配置运行时的行为。
