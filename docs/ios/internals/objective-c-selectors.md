---
title: Xamarin 中的目标-C 选择器
description: 本文档介绍如何从C#与的目标-C 选择器进行交互。 它介绍了如何调用在执行此操作时必须考虑的选择器和技术注意事项。
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/12/2017
ms.openlocfilehash: 17b845345175d80237bcfdb171461f2c763c364e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291854"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Xamarin 中的目标-C 选择器

目标 C 语言基于*选择器*。 选择器是可以发送到对象或*类*的消息。 [Xamarin](~/ios/internals/api-design/index.md)将实例选择器映射到实例方法，将类选择器映射到静态方法。

与普通 C 函数（与成员C++函数类似）不同，不能直接使用[P/invoke](https://www.mono-project.com/docs/advanced/pinvoke/)调用选择器，而是使用[`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
才能.

有关目标-C 中的消息的详细信息，请参阅 Apple 使用[对象](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2)指南。

## <a name="example"></a>示例

假设你想要调用[`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
选择器[`NSString`](https://developer.apple.com/documentation/foundation/nsstring)。
声明（来自 Apple 的文档）是：

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

此 API 具有以下特征：

- 返回类型`CGSize`适用于 Unified API。
- 参数是[UIFont](xref:UIKit.UIFont) （和从 NSObject 派生的类型），并[映射到](xref:System.IntPtr) [。](xref:Foundation.NSObject) `font`
- 参数将映射到`nfloat`。 `CGFloat` `width`
- `lineBreakMode`参数[a`UILineBreakMode`](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc)已在 Xamarin 中绑定为[`UILineBreakMode`](xref:UIKit.UILineBreakMode)
枚举.

将其放在`objc_msgSend`一起，声明应匹配：

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

按如下所示声明：

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

若要调用此方法，请使用如下所示的代码：

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

如果返回的值是小于8个字节的结构（例如，切换到统一 api 之前`SizeF`使用过的结构），则上述代码将在模拟器上运行，但在设备上崩溃。 若要调用返回小于8位的值的选择器，请声明`objc_msgSend_stret`函数：

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

若要调用此方法，请使用如下所示的代码：

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

## <a name="invoking-a-selector"></a>调用选择器

调用选择器有三个步骤：

1. 获取选择器目标。
2. 获取选择器名称。
3. 用`objc_msgSend`适当的参数调用。

### <a name="selector-targets"></a>选择器目标

选择器目标可以是对象实例或目标-C 类。 如果目标是一个实例并且来自绑定的 Xamarin iOS 类型，请使用[`ObjCRuntime.INativeObject.Handle`](xref:ObjCRuntime.INativeObject.Handle)属性。

如果目标是类，请使用[`ObjCRuntime.Class`](xref:ObjCRuntime.Class)获取对类实例的引用，然后[`Class.Handle`](xref:ObjCRuntime.Class.Handle)使用属性。

### <a name="selector-names"></a>选择器名称

选择器名称列在 Apple 文档中。 例如， [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc)包括[`sizeWithFont:`](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc)和[`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc)选择器。 嵌入和尾随冒号是选择器名称的一部分，因此不能省略。

选择器名称后，可以为它创建一个[`ObjCRuntime.Selector`](xref:ObjCRuntime.Selector)实例。

### <a name="calling-objc_msgsend"></a>调用 objc_msgSend

`objc_msgSend`将消息（选择器）发送到对象。 此系列函数至少需要两个必需参数：选择器目标（实例或类句柄）、选择器本身以及选择器所需的任何参数。 实例和选择器参数必须是`System.IntPtr`，所有剩余的参数必须与该选择器所需的类型（ `nint`例如`int`）或`System.IntPtr`所有`NSObject`派生的类型相匹配。 使用[`NSObject.Handle`](xref:Foundation.NSObject.Handle)
用于获取`IntPtr`目标 C 类型实例的的属性。

存在多`objc_msgSend`个函数：

- [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)用于返回结构的选择器。 在 ARM 上，这包括不是枚举的所有返回类型或任何 C 内置类型（`char`、 `int` `long` `short`、、、 `float`、 `double`）。 在 x86 （模拟器）上，此方法需要用于大小超过8个字节的所有结构（`CGSize`为8个字节，不会在模拟器中使用`objc_msgSend_stret` ）。 
- 仅[`objc_msgSend_fpret`](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc)用于在 x86 上返回浮点值的选择器。 此函数无需在 ARM 上使用;请`objc_msgSend`改用。 
- Main [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)函数用于所有其他选择器。

确定需要调用的`objc_msgSend`函数（每个模拟器和设备可能需要不同的方法）后，可以使用普通[`[DllImport]`](xref:System.Runtime.InteropServices.DllImportAttribute)方法声明函数以便以后调用。

可以在中`ObjCRuntime.Messaging`找到一组`objc_msgSend`预生成的声明。

## <a name="different-invocations-on-simulator-and-device"></a>模拟器和设备上的不同调用

如上所述，客观-C 具有三种`objc_msgSend`方法：一种用于常规调用，一个用于返回浮点值的调用（仅限 x86），另一个用于返回结构值的调用。 后者在中`ObjCRuntime.Messaging`包含后缀`_stret` 。

如果要调用将返回特定结构的方法（如下所述的规则），则必须调用方法，并将返回值作为第一个参数作为`out`值：

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

在 x86 和 ARM 上，使用`_stret_`方法的规则不同。
如果希望绑定同时在模拟器和设备上运行，请添加如下所示的代码：

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

### <a name="using-the-objc_msgsend_stret-method"></a>使用 objc_msgSend_stret 方法

为 ARM 生成时，请使用[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
对于不是枚举的任何值类型或枚举的任何基类型（`int`、 `short` `long` `byte`、 `double` `float`、、、）。

构建 x86 时，请使用[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
对于不是枚举的任何值类型或枚举的任何基类型（`int`、 `short` `long` `byte`、 `double`、、、 `float`），其本机大小大于8字节。

### <a name="creating-your-own-signatures"></a>创建自己的签名

如果需要，可以使用以下[gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c)创建自己的签名。

## <a name="related-links"></a>相关链接

- [目标-C 选择器](https://developer.xamarin.com/samples/mac-ios/Objective-C/)示例
