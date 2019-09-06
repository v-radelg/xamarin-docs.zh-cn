---
title: 目标-C 支持
description: 本文档提供对 .NET 嵌入中的目标 C 的支持的说明。 它讨论自动引用计数、NSString、协议、NSObject 协议、异常等。
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: 8798e0acf7b1184c64c7012b2f724e2fa7d2c816
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292769"
---
# <a name="objective-c-support"></a>目标-C 支持

## <a name="specific-features"></a>特定功能

为实现目标-C 有一些值得注意的特殊功能。

### <a name="automatic-reference-counting"></a>自动引用计数

**需要**使用自动引用计数（ARC）才能调用生成的绑定。 使用基于 .NET 嵌入的库的项目必须使用`-fobjc-arc`进行编译。

### <a name="nsstring-support"></a>NSString 支持

公开`System.String`类型的 api 将`NSString`转换为。 这使得内存管理比处理`char*`时更容易。

### <a name="protocols-support"></a>协议支持

托管接口会转换为目标-C 协议，其中所有成员`@required`均为。

### <a name="nsobject-protocol-support"></a>NSObject 协议支持

默认情况下，.NET 和目标 C 运行时的默认哈希与相等性被认为是可互换的，因为它们共享类似的语义。

当托管类型重写`Equals(Object)`或`GetHashCode`时，这通常意味着默认（.net）行为不足; 这意味着默认的目标 C 行为可能不够。

在这种情况下，生成器将[`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc)重写[`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) [ `NSObject`协议](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)中定义的方法和属性。 这样，便可以透明地从目标 C 代码使用自定义托管实现。

### <a name="exceptions-support"></a>异常支持

`--nativeexception` 作为`objcgen`参数传递会将托管异常转换为可以捕获和处理的目标-C 异常。 

### <a name="comparison"></a>比较

实现`IComparable`的托管类型（或其泛型版本`IComparable<T>`）将生成目标为`NSComparisonResult` C 的友好方法，这些方法可`nil`返回并接受自变量。 这样，生成的 API 就更适合目标-C 开发人员。 例如：

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categories

托管扩展方法转换为类别。 例如，下面的扩展方法`Collection`：

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

将创建如下所示的目标-C 类别：

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

当单个托管类型扩展多种类型时，将生成多个目标 C 类别。

### <a name="subscripting"></a>下标

托管索引属性转换为对象下标。 例如:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

会创建类似于以下内容的目标-C：

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

可通过下标语法使用该语法：

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

根据索引器的类型，将根据需要生成索引的或键控的下标。

[本文](http://nshipster.com/object-subscripting/)是下标的一个重要简介。

## <a name="main-differences-with-net"></a>与 .NET 的主要差异

### <a name="constructors-vs-initializers"></a>构造函数和初始值设定项

在目标-C 中，可以调用继承链中任何父类的任何初始值设定项原型，除非它被标记为不可用（`NS_UNAVAILABLE`）。

在C#中，必须显式声明类中的构造函数成员，这意味着不会继承构造函数。

为了向目标-C 公开C# API 的正确表示形式， `NS_UNAVAILABLE`会将添加到父类中不存在的任何初始值设定项。

C#API

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

目标-C 呈现的 API：

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

`initWithId:`此处已将标记为不可用。

### <a name="operator"></a>运算符

客观-C 不支持运算符重载C# ，因此运算符转换为类选择器：

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

实例部署到 Windows Azure 虚拟机 (VM) 中的

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

但是，某些 .NET 语言不支持运算符重载，因此除了运算符重载外，还经常包含["友好"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)命名方法。

如果找到了运算符版本和 "友好" 版本，则仅生成友好版本，因为它们将生成相同的目标 C 名称。

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

会

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>相等运算符

在中， `==` " C#常规" 运算符作为常规运算符处理，如上所述。

但是，如果找到 "友好" 等于运算符，则在生成中`==`将跳`!=`过运算符和运算符。

### <a name="datetime-vs-nsdate"></a>DateTime 与 NSDate

[`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc)文档：

> `NSDate`对象封装单个时间点，与任何特定 calendrical 系统或时区无关。 Date 对象是不可变的，表示相对于绝对引用日期的固定时间间隔（1月 1 2001 日，00:00:00 UTC）。

由于引用日期，其之间的所有转换都`DateTime`必须在 UTC 内完成。 `NSDate`

#### <a name="datetime-to-nsdate"></a>DateTime 到 NSDate

从`DateTime`转换到`NSDate`时，的`Kind`属性`DateTime`将采用：

|类型|结果|
|---|---|
|`Utc`|按原样使用提供`DateTime`的对象执行转换。|
|`Local`|在提供`ToUniversalTime()` `DateTime`的对象中调用的结果用于转换。|
|`Unspecified`|提供`DateTime`的对象被假定为 UTC，因此在为`Utc`时`Kind`相同。|

转换使用以下公式：

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

在此公式中： 

- `NSDateReferenceDateTicks`根据00:00:00 年1月`NSDate` 1 2001 日的参考日期： 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond)定义于[`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

若要创建`NSDate`对象`TimeInterval` ，请将与`NSDate` [dateWithTimeIntervalSinceReferenceDate：](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc)选择器一起使用。

#### <a name="nsdate-to-datetime"></a>NSDate 到 DateTime

从`NSDate` 到`DateTime`的转换使用以下公式：

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

在此公式中： 

- `NSDateReferenceDateTicks`根据00:00:00 年1月`NSDate` 1 2001 日的参考日期： 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond)定义于[`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

计算`DateTimeTicks` `DateTimeKind.Utc`后`kind` ，将调用[构造函数](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_)，并将其设置为。 `DateTime`

> [!NOTE]
> `NSDate`可以为`nil`， `DateTime`但是 .net 中的结构，而定义的结构不能`null`为。 如果`nil`给出`NSDate`，则会将其转换为默认`DateTime`值，这将映射到`DateTime.MinValue`。

`NSDate`支持更高的最大值和最小`DateTime`值。 从`NSDate`转换到`DateTime`时，这些较高和较[低的](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) `DateTime`值将分别更改为[MinValue 或](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue)。
