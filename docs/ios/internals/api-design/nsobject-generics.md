---
title: 在 Xamarin.iOS NSObject 的通用子类
description: 本文档介绍如何创建创建 NSObject 的通用子类。 它将检查什么可以并不能完成，讨论了静态注册机构，并且介绍了性能。
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 512280e9c298cfbcea6f693b0691236fd1cf5a5f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036465"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>在 Xamarin.iOS NSObject 的通用子类

## <a name="using-generics-with-nsobjects"></a>将泛型与 NSObjects

从开始使用 Xamarin.iOS 7.2.1，可以使用泛型中的子类`NSObject`(例如[UIView](xref:UIKit.UIView)。

现在可以创建泛型类，如下：

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

由于对象派生子类`NSObject`注册与 Objective C 运行时有有关可能的通用子类具有一些限制`NSObject`类型。
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>NSObject 的通用子类的注意事项

本文档详细介绍中的通用子类的有限支持的限制`NSObjects`使用 Xamarin.iOS 7.2.1 引入。
    
### <a name="generic-type-arguments-in-member-signatures"></a>在成员签名中的泛型类型参数

在公开给 Objective C 的成员签名中的所有泛型类型参数必须具有`NSObject`约束。

**好**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**:泛型类型参数是`NSObject`，因此的选择器签名`myMethod:`可以安全地公开到 Objective C (它将始终为`NSObject`或它的子类)。

**错误**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**原因**： 不能为 Objective C 代码可以调用，因为签名会有所不同的泛型类型的确切类型的导出成员创建的 Objective C 签名`T`。

**好**:

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

**原因**： 可以只要它们不会导出的成员签名的一部分不受约束的泛型类型参数。

**好**:

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

**原因**: `T` Objective C 中的参数导出`MyMethod`约束为不可`NSObject`，不受约束的类型`U`不是签名的一部分。
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>从 OBJECTIVE-C 的泛型类型的实例化

不允许从 OBJECTIVE-C 的泛型类型的实例化。 这通常发生在 xib 中使用托管的类型时。

请考虑此类定义中，它提供了一个构造函数采用`IntPtr`(Xamarin.iOS 方式构造的C#从本机 OBJECTIVE-C 的实例对象):
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

上述构造时没有什么问题，在运行时，这将引发异常时如果 Objective C 尝试创建它的一个实例。

这是因为 Objective C 的泛型类型，没有概念，它不能指定要创建的确切的泛型类型，会发生情况。

通过创建一个专门的泛型类型的子类，此问题可得到解决。   例如：
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

现在没有歧义，类`GenericUIView`可以 xib 中使用。

## <a name="no-support-for-generic-methods"></a>不支持泛型方法

### <a name="generic-methods-are-not-allowed"></a>不允许使用泛型方法。

下面的代码将编译：

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**原因**:这不允许的因为 Xamarin.iOS 不知道要使用的类型参数的具体类型`T`从 Objective C 调用该方法时。

一种替代方法是创建一个专用的方法，并改为导出的：

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

### <a name="no-exported-static-members-allowed"></a>允许任何导出静态成员

如果承载的通用子类中，可以不公开到 OBJECTIVE-C 的静态成员`NSObject`。

不支持的方案的示例：

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

**原因：** 就像泛型方法，Xamarin.iOS 运行时需要了解什么键入要用于泛型类型参数 t。

对实例使用的实例本身的成员 (因为永远不会将实例泛型<T>，它将始终为泛型<SomeSpecificClass>)，但对于静态成员不存在此信息。

请注意，这适用即使有问题的成员不以任何方式使用类型参数 T。

在这种情况下替代方法是创建一个专用的子类：

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIVIewProperty {
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

### <a name="requires-new-static-registrar"></a>需要新的静态注册机构

泛型支持需要新[注册系统](~/ios/internals/registrar.md)。

如果尝试使用旧遇到 （中除了不会生成正确的代码，从而导致未定义的行为） 的泛型类型时，旧的注册系统会显示警告。
    
## <a name="performance"></a>性能

静态注册机构不能在生成时解决在泛型类型中导出的成员，像通常一样，它必须在运行时查找。 这意味着，调用此方法从 OBJECTIVE-C 是有些慢调用从非泛型类的成员。

