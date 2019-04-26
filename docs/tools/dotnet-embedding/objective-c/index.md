---
title: Objective C 的支持
description: 本文档提供适用于 OBJECTIVE-C 的中嵌入.NET 支持的说明。 它讨论自动引用计数、 NSString、 协议、 NSObject 协议、 异常和的详细信息。
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 48caa70cf2bd408f8afc673b400f7d5a4369e108
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230843"
---
# <a name="objective-c-support"></a>Objective C 的支持

## <a name="specific-features"></a>特定功能

Objective C 的新一代拥有几个值得注意的特殊功能。

### <a name="automatic-reference-counting"></a>自动引用计数

使用的自动引用计数 (ARC) 是**需**调用生成的绑定。 使用基于.NET 嵌入的库的项目必须使用编译`-fobjc-arc`。

### <a name="nsstring-support"></a>NSString 支持

公开的 Api`System.String`类型转换为`NSString`。 这使内存管理相比时应对更容易`char*`。

### <a name="protocols-support"></a>协议支持

托管的接口转换为 Objective C 协议的所有成员都是`@required`。

### <a name="nsobject-protocol-support"></a>NSObject 协议支持

默认情况下，假定的默认哈希和相等性的.NET 和 OBJECTIVE-C 运行时是可互换，聆听他们分享类似语义。

当托管的类型的重写`Equals(Object)`或`GetHashCode`，这通常意味着 (.NET) 的默认行为是不足够; 这意味着默认 OBJECTIVE-C 的行为可能足够任一。

在这种情况下，生成器将覆盖[ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc)方法并[ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc)中定义的属性[`NSObject`协议](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)。 这允许要 Objective C 代码中以透明方式使用的自定义托管的实现。

### <a name="exceptions-support"></a>异常支持

传递`--nativeexception`作为自变量到`objcgen`会将托管的异常转换为 Objective C 异常，可以捕获和处理。 

### <a name="comparison"></a>比较

将托管类型实现`IComparable`(或其通用版本`IComparable<T>`) 将生成返回的 Objective C 友好方法`NSComparisonResult`并接受`nil`参数。 这使得生成的 API 更 Objective C 开发人员所熟知。 例如：

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>类别

托管的扩展方法转换为类别。 例如，以下扩展方法上的`Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

将创建与此类似的 Objective C 类别：

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

如果单个托管的类型扩展了几种类型，生成多个 OBJECTIVE-C 的类别。

### <a name="subscripting"></a>下标

托管索引的属性将转换为对象的子脚本。 例如：

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

将创建类似于 Objective C:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

它可通过 Objective C 下标语法：

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

根据你的索引器的类型，在适当的位置，将生成索引或键的子脚本。

这[一文](http://nshipster.com/object-subscripting/)是到子脚本很棒的介绍。

## <a name="main-differences-with-net"></a>使用.NET 的主要区别

### <a name="constructors-vs-initializers"></a>构造函数与初始值设定项

Objective C 中你可以调用任意初始值设定项的任何继承链中的父类的原型除非它被标记为不可用 (`NS_UNAVAILABLE`)。

在C#必须显式声明在类中，这意味着不会继承构造函数构造函数成员。

若要公开的正确表示形式C#Objective C API`NS_UNAVAILABLE`添加到不是从父类将被子类中存在任何初始值设定项。

C#API:

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

Objective C 显示 API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

在这里，`initWithId:`已标记为不可用。

### <a name="operator"></a>运算符

Objective C 不支持运算符重载为C#值有效，因此运算符转换为类选择器：

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

更改为

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

但是，某些.NET 语言不支持运算符重载，因此通常还包括["友好"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)名为除了运算符重载的方法。

如果运算符版本和"友好"的版本找到，将生成只有友好版本，因为它们不会生成相同的 OBJECTIVE-C 的名称。

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

将成为：

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>相等运算符

在常规运算符`==`中C#会被视为一个常规运算符所指出更高版本。

但是，如果"友好"的等于运算符找到，则这两个运算符`==`和运算符`!=`将跳过在生成中。

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

从[ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc)文档：

> `NSDate` 对象封装的时间，独立于任何特定 calendrical 系统或时区的单一点。 日期对象是不可变的这表示相对于绝对引用日期的固定时间间隔 (00： 在 2001 年 1 月 1 00:00 UTC)。

由于`NSDate`引用日期，它之间的所有转换和`DateTime`必须在 UTC 中完成。

#### <a name="datetime-to-nsdate"></a>从 DateTime 到 NSDate

从转换时`DateTime`到`NSDate`，则`Kind`属性`DateTime`考虑在内：

|类型|结果|
|---|---|
|`Utc`|使用提供执行转换`DateTime`对象原样。|
|`Local`|调用的结果`ToUniversalTime()`中所提供`DateTime`对象用于进行转换。|
|`Unspecified`|提供`DateTime`对象被假定为 UTC，因此相同的行为时`Kind`是`Utc`。|

转换使用以下公式：

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

在此公式： 

- `NSDateReferenceDateTicks` 根据计算`NSDate`2001 年 1 月 1 引用 00:00:00 UTC 日期： 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) 在定义 [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

若要创建`NSDate`对象，`TimeInterval`用于`NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc)选择器。

#### <a name="nsdate-to-datetime"></a>NSDate 为日期时间

从转换`NSDate`到`DateTime`使用以下公式：

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

在此公式： 

- `NSDateReferenceDateTicks` 根据计算`NSDate`2001 年 1 月 1 引用 00:00:00 UTC 日期： 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) 在定义 [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

后计算`DateTimeTicks`，则`DateTime`[构造函数](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_)调用，设置其`kind`到`DateTimeKind.Utc`。

> [!NOTE]
> `NSDate` 可以是`nil`，但`DateTime`是在.NET 中，它通过定义不能是一种结构`null`。 如果您为提供`nil` `NSDate`，会将它转换为默认值`DateTime`值，该值映射到`DateTime.MinValue`。

`NSDate` 支持更高版本的最大值和低于最小值`DateTime`。 从转换时`NSDate`到`DateTime`，这些更高版本和较低的值更改为`DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue)或[MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue)分别。
