---
title: 在 Xamarin.iOS OBJECTIVE-C 选择器
description: 本文档介绍如何与从 C# 的 OBJECTIVE-C 选择器进行交互。 它介绍了如何调用选择器和执行此操作时必须考虑在内的技术考虑因素。
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/12/2017
ms.openlocfilehash: 15db59945f482728f760006095e294bc5628c8bd
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870166"
---
# <a name="objective-c-selectors-in-xamarinios"></a>在 Xamarin.iOS OBJECTIVE-C 选择器

Objective C 语言基于*选择器*。 选择器是一条消息，可以发送到一个对象或*类*。 [Xamarin.iOS](~/ios/internals/api-design/index.md)映射实例为实例方法的选择器和类选择器对静态方法。

与普通 C 函数 (等C++成员函数)，不能直接调用选择器使用[P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/)选择器而是发送到 OBJECTIVE-C 类或使用实例的步骤 [`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
函数。

Objective C 中的消息有关的详细信息，看一看 Apple[使用对象](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2)指南。

## <a name="example"></a>示例

假设你想要调用 [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
上的选择器[ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring)。
（从 Apple 的文档） 声明为：

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

此 API 具有以下特征：

- 返回类型是`CGSize`统一 api。
- `font`参数是[UIFont](xref:UIKit.UIFont) (和类型 （间接） 派生自[NSObject](xref:Foundation.NSObject)，并映射到[System.IntPtr](xref:System.IntPtr)。
- `width`参数， `CGFloat`，映射到`nfloat`。
- `lineBreakMode`参数， [ `UILineBreakMode` ](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc)，已在作为 Xamarin.iOS 绑定 [`UILineBreakMode`](xref:UIKit.UILineBreakMode)
枚举。

将它放在一起，`objc_msgSend`声明应匹配：

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

将其声明，如下所示：

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

若要调用此方法，请使用以下代码：

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, 
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

返回的值曾经是大小小于 8 个字节的结构 (如较早`SizeF`切换到统一的 Api 之前，使用)，方式但在设备上崩溃模拟器上运行上面的代码。 若要调用的大小小于 8 位返回值的选择器，声明`objc_msgSend_stret`函数：

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

若要调用此方法，请使用以下代码：

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, 
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>调用一个选择器

调用一个选择器包含三个步骤：

1. 获取选择器目标。
2. 获取选择器名称。
3. 调用`objc_msgSend`使用适当的参数。

### <a name="selector-targets"></a>选择器目标

选择器目标是 OBJECTIVE-C 类或对象实例。 如果目标是一个实例，来自绑定的 Xamarin.iOS 类型，使用[ `ObjCRuntime.INativeObject.Handle` ](xref:ObjCRuntime.INativeObject.Handle)属性。

如果目标是一个类，使用[ `ObjCRuntime.Class` ](xref:ObjCRuntime.Class)若要获取对类实例的引用，然后使用[ `Class.Handle` ](xref:ObjCRuntime.Class.Handle)属性。

### <a name="selector-names"></a>选择器名称

Apple 的文档中列出了选择器名称。 例如， [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring?language=objc)包括[ `sizeWithFont:` ](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc)并[ `sizeWithFont:forWidth:lineBreakMode:` ](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc)选择器。 嵌入和尾随冒号是选择器名称的一部分，不能将其删除。

选择器名称后，可以创建[ `ObjCRuntime.Selector` ](xref:ObjCRuntime.Selector)为它的实例。

### <a name="calling-objcmsgsend"></a>Calling objc_msgSend

`objc_msgSend` 将一条消息 （选择器） 发送到一个对象。 此系列函数采用至少两个必需的参数： 选择器目标 （实例或处理的类）、 本身的选择器和选择器所需的任何参数。 实例，并选择器参数必须为`System.IntPtr`，并且所有其余参数必须匹配的类型选择器要求，例如`nint`有关`int`，或`System.IntPtr`所有`NSObject`-派生类型。 使用 [`NSObject.Handle`](xref:Foundation.NSObject.Handle)
属性获取`IntPtr`Objective C 类型实例。

没有多个`objc_msgSend`函数：

- 使用[ `objc_msgSend_stret` ](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)为返回结构的选择器。 在 ARM，这包括非枚举的所有返回类型或任何 C 内置类型 (`char`， `short`， `int`， `long`， `float`， `double`)。 此方法需要 x86 （模拟器） 上使用的所有结构大于 8 个字节的大小 (`CGSize`为 8 个字节，但不使用`objc_msgSend_stret`在模拟器中)。 
- 使用[ `objc_msgSend_fpret` ](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc)返回浮点的选择器仅在 x86 上点值。 此函数不需要在 ARM; 上使用请改用`objc_msgSend`。 
- 主[objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)函数用于所有其他选择器。

一旦您决定这`objc_msgSend`需要调用的函数 （模拟器和设备可能每个需要另一种方法），可以使用普通[ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute)方法声明为更高版本调用此函数。

一组预制`objc_msgSend`声明可在`ObjCRuntime.Messaging`。

## <a name="different-invocations-on-simulator-and-device"></a>在模拟器和设备上不同的调用

Objective C 上文所述，有三种类型的`objc_msgSend`方法： 一个用于常规调用，返回浮点值 (仅限 x86) 的调用，调用返回结构值。 后者包含后缀`_stret`在`ObjCRuntime.Messaging`。

如果调用将返回某些结构 （如下所述的规则） 的方法，必须调用的方法的返回值作为第一个参数作为`out`值：

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

有关何时使用规则`_stret_`在 x86 和 ARM 上的方法不同。
如果你想在模拟器和设备上运行您的绑定，添加以下代码：

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
} 
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objcmsgsendstret-method"></a>使用 objc_msgSend_stret 方法

为 ARM 生成时，使用 [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
任何值类型，不是枚举或任何枚举的基类型 (`int`， `byte`， `short`， `long`， `double`， `float`)。

为 x86 生成时，使用 [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
任何值类型，不是枚举或任何枚举的基类型 (`int`， `byte`， `short`， `long`， `double`， `float`) 以及其本机大小大于 8 个字节。

### <a name="creating-your-own-signatures"></a>创建你自己的签名

以下[要点](https://gist.github.com/rolfbjarne/981b778a99425a6e630c)可用于创建你自己的签名，如果所需。

## <a name="related-links"></a>相关链接

- [OBJECTIVE-C 选择器](https://developer.xamarin.com/samples/mac-ios/Objective-C/)示例
