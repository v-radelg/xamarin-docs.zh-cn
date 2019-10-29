---
title: Xamarin 中 NSObject 的通用子类
description: 本文档介绍如何创建 NSObject 的常规子类。 它将检查可以和不能完成的操作，讨论静态注册器并了解性能。
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 279fcac1611038613bf442e1b766fda45dd5a429
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022367"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Xamarin 中 NSObject 的通用子类

## <a name="using-generics-with-nsobjects"></a>将泛型与 NSObjects 结合使用

可以在 `NSObject`的子类中使用泛型，例如[UIView](xref:UIKit.UIView)：

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

由于子类 `NSObject` 的对象向目标-C 运行时注册，因此存在一些与 `NSObject` 类型的泛型子类有关的可能的限制。

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>NSObject 的泛型子类注意事项

本文档详细介绍对 `NSObjects`的泛型子类的有限支持的限制。

### <a name="generic-type-arguments-in-member-signatures"></a>成员签名中的泛型类型参数

公开给目标-C 的成员签名中的所有泛型类型参数都必须具有 `NSObject` 约束。

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

**原因**：泛型类型参数是一个 `NSObject`，因此可以将 `myMethod:` 的选择器签名安全地公开给目标 C （它将始终 `NSObject` 或其子类）。

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

**原因**：无法为目标-c 代码可调用的已导出成员创建目标为 c 的签名，因为根据泛型类型 `T`的具体类型，签名将有所不同。

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

**原因**：目标-C 导出 `MyMethod` 中的 `T` 参数被限制为 `NSObject`，不受约束的类型 `U` 不是签名的一部分。

### <a name="instantiations-of-generic-types-from-objective-c"></a>来自目标的泛型类型的实例化-C

不允许从目标-C 中实例化泛型类型。 当在 xib 或情节提要中使用托管类型时，通常会发生这种情况。

请考虑此类定义，此类定义公开一个构造函数，该构造函数采用 `IntPtr` （从本机C#目标-C 实例构造对象的 Xamarin 方法）：

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

虽然以上构造是正确的，但在运行时，如果客观-C 尝试创建它的实例，这将引发异常。

出现这种情况的原因是，目标-C 没有泛型类型的概念，并且无法指定要创建的确切泛型类型。

可以通过创建泛型类型的专用子类来解决此问题。 例如:

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

现在尚无歧义，可在 xib 或情节提要中使用类 `GenericUIView`。

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

**原因**：这是不允许的，因为在从目标-C 调用方法时，Xamarin 不知道要使用哪种类型参数 `T` 类型。

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

如果静态成员托管在 `NSObject`的泛型子类中，则不能将其公开给目标-C。

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

**在于**与泛型方法一样，Xamarin 运行时需要能够知道要用于泛型类型参数 `T`的类型。

对于实例成员，将使用实例本身（因为决不会有 `Generic<SomeSpecificClass>``Generic<T>`的实例），但对于静态成员，此信息并不存在。

请注意，即使相关成员不以任何方式使用类型参数 `T`，这也适用。

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
