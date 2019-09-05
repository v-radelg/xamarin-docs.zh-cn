---
title: Xamarin 中 NSObject 的通用子类
description: 本文档介绍如何创建 NSObject 的常规子类。 它将检查可以和不能完成的操作，讨论静态注册器并了解性能。
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 531d0b2b6141cc0e1f4014f1d3422af3c6f8643a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291983"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Xamarin 中 NSObject 的通用子类

## <a name="using-generics-with-nsobjects"></a>将泛型与 NSObjects 结合使用

可以在的`NSObject`子类中使用泛型，例如[UIView](xref:UIKit.UIView)：

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

由于子类`NSObject`在目标 C 运行时中注册的对象存在一些限制，因此对于类型的`NSObject`泛型子类可能存在一些限制。

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>NSObject 的泛型子类注意事项

本文档详细介绍对的`NSObjects`泛型子类的有限支持的限制。

### <a name="generic-type-arguments-in-member-signatures"></a>成员签名中的泛型类型参数

公开给目标-C 的成员签名中的所有泛型类型参数都必须`NSObject`具有约束。

**不错**：

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**：泛型类型参数是`NSObject`，因此的选择器`myMethod:`签名可以安全地公开给目标 C （它将始终是`NSObject`或它的子类）。

**错误**：

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**：无法为目标-c 代码可调用的已导出成员创建目标为 c 的签名，因为根据泛型类型`T`的具体类型，签名将有所不同。

**不错**：

```csharp
class Generic<T> : NSObject
{
    T storage;

    [Export ("myMethod:")]
    public void MyMethod (NSObject value)
    {
    }
}
```

**原因**：只要它们不是导出成员签名的一部分，就可以具有不受约束的泛型类型参数。

**不错**：

```csharp
class Generic<T, U> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
        Console.WriteLine (typeof (U));
    }
}
```

**原因**： `T`目标-C 导出`MyMethod`的参数被约束为`NSObject`，不受约束的类型`U`不是签名的一部分。

### <a name="instantiations-of-generic-types-from-objective-c"></a>来自目标的泛型类型的实例化-C

不允许从目标-C 中实例化泛型类型。 当在 xib 或情节提要中使用托管类型时，通常会发生这种情况。

请考虑此类定义，此类定义公开了一个`IntPtr`构造函数，该构造函数采用（从C#本机目标-C 实例构造对象的 Xamarin 方式）：

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

虽然以上构造是正确的，但在运行时，如果客观-C 尝试创建它的实例，这将引发异常。

出现这种情况的原因是，目标-C 没有泛型类型的概念，并且无法指定要创建的确切泛型类型。

可以通过创建泛型类型的专用子类来解决此问题。 例如：

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

现在不再存在歧义，该类`GenericUIView`可用于 xib 或情节提要。

## <a name="no-support-for-generic-methods"></a>不支持泛型方法

### <a name="generic-methods-are-not-allowed"></a>不允许使用泛型方法。

以下代码将不会编译：

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**原因**：这是不允许的，因为在从目标-C 调用方法时，Xamarin 不知道`T`要用于类型参数的类型。

另一种方法是创建专用方法，并改为导出：

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyUIViewMethod (UIView argument)
    {
        MyMethod<UIView> (argument);
    }
    public void MyMethod<T> (T argument)
    {
    }
}
```

### <a name="no-exported-static-members-allowed"></a>不允许使用导出的静态成员

如果静态成员承载于的泛型子类`NSObject`中，则不能将其公开给目标-C。

不受支持的方案示例：

```csharp
class Generic<T> : NSObject where T : NSObject
{
    [Export ("myMethod:")]
    public static void MyMethod ()
    {
    }

    [Export ("myProperty")]
    public static T MyProperty { get; set; }
}
```

**在于**与泛型方法一样，Xamarin 运行时需要能够知道要用于泛型类型参数`T`的类型。

对于实例成员，将使用实例本身（因为决不会有实例`Generic<T>`，它将始终为`Generic<SomeSpecificClass>`），但对于静态成员，此信息不存在。

请注意，即使相关成员不以任何方式使用 type 参数`T` ，此应用也适用。

在这种情况下，另一种方法是创建专用子类：

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIViewProperty {
        get { return MyProperty; }
        set { MyProperty = value; }
    }
}

class Generic<T> : NSObject where T : NSObject
{
    public static void MyMethod () {}
    public static T MyProperty { get; set; }
}
```

## <a name="performance"></a>性能

静态注册器在生成时无法解析泛型类型中的导出成员，这通常是必须在运行时查找。 这意味着从客观-C 调用此类方法略微慢于从非泛型类中调用成员。

