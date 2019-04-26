---
title: C#6 项新增功能概述
description: 最新版本的C#语言 – 版本 6 – 不断发展的语言具有更少的样本，提高的清晰度和更多的一致性。 更简洁的初始化语法，能够使用 await 中 catch/finally 块和 null 条件？ 运算符时特别有用。
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: b69fe417bb521781453042269b9b52609d8e00a0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035803"
---
# <a name="c-6-new-features-overview"></a>C#6 项新增功能概述

_最新版本的C#语言 – 版本 6 – 不断发展的语言具有更少的样本，提高的清晰度和更多的一致性。更简洁的初始化语法，能够使用 await 中 catch/finally 块和 null 条件？运算符时特别有用。_

本文档介绍了新功能的C#6。 Mono 编译器完全支持，开发人员可以开始使用所有 Xamarin 目标平台的新功能。

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**新增功能C#6，也可由[Xamarin University](https://university.xamarin.com/)**

## <a name="using-c-6"></a>使用C#6

C# 6 编译器用于所有最新版本的 Visual Studio for mac。
使用命令行编译器应确认`mcs --version`返回 4.0 或更高版本。
Visual Studio for Mac 用户可以检查是否具有 4 个 （或更高版本） 的 Mono 安装指**关于 Visual Studio for Mac > Visual Studio for Mac > 显示详细信息**。

## <a name="less-boilerplate"></a>更少的样本
### <a name="using-static"></a>using static
枚举和某些类，如`System.Math`，是主要持有人的静态值和函数。 在C#6 中，您可以导入具有单个类型的所有静态成员`using static`语句。 比较中的典型三角函数函数C#5 和C#6:

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` 不会使公共`const`字段，如`Math.PI`和`Math.E`、 直接访问：

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>使用静态扩展方法

`using static`设施有点以不同方式运行的扩展方法。 尽管扩展方法是使用`static`，它们是毫无意义地而无需对其进行操作的实例。 因此，在`using static`用于定义扩展方法，其目标类型上可用的扩展方法的类型 (该方法的`this`类型)。 例如，`using static System.Linq.Enumerable`可以用于扩展的 API`IEnumerable<T>`对象而不会降低中的所有 LINQ 类型：

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

前面的示例演示的行为差异： 扩展方法`Enumerable.Where`与数组时的静态方法相关联`String.Join`可以调用而无需引用`String`类型。

### <a name="nameof-expressions"></a>nameof 表达式
有时，你想要引用名称为您提供的变量或字段。 在C#第 6`nameof(someVariableOrFieldOrType)`将返回字符串`"someVariableOrFieldOrType"`。 例如，当引发`ArgumentException`就极有可能想要命名的参数无效：

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

主要优势`nameof`表达式是它们是类型检查，并且是与提供工具支持重构兼容。 类型检查的`nameof`表达式特别中很受欢迎的情况下，`string`用于动态将类型相关联。 例如，在 iOS`string`用于指定类型的原型`UITableViewCell`中的对象`UITableView`。 `nameof` 可以确保这种关联不会由于拼写错误或封写得草率重构失败：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

尽管可以将传递到的限定的名称`nameof`，只有最后一个元素 (在最后一个`.`) 返回。 例如，您可以在 Xamarin.Forms 中添加数据绑定：

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

两次调用`SetBinding`均通过完全相同的值：`nameof(ReactiveType.StringField)`是`"StringField"`，而不`"ReactiveType.StringField"`正如您预料最初。

## <a name="null-conditional-operator"></a>Null 条件运算符
之前更新为C#引入的概念，可以为 null 的类型以及 null 合并运算符`??`以减少样本代码以处理可以为 null 值时。 C#6 继续本主题使用"null 条件运算符" `?.`。 如果该对象不是 null 条件运算符时表达式右侧的对象上使用，返回成员值`null`和`null`否则为：

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(同时`length0`并`length1`会被推断为类型`int?`)

中的上一示例所示的最后一行`?`null 条件运算符结合`??`null 合并运算符。 新C#6 null 条件运算符返回`null`上的数组，此时 null 合并运算符会介入并提供到 0 中的第二个元素`lengths`数组 （无论是适当或不是，当然，具体问题的）。

Null 条件运算符应极大地减少的样板 null 检查是必要的很多，许多应用程序。

有一些限制由于歧义而 null 条件运算符。 不能紧跟`?`具有带括号的参数列表，你可能希望如何处理委托：

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

但是，`Invoke`可用于分隔`?`从参数列表和相比仍是显著的改进`null`-检查的样板块：

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>字符串内插
`String.Format`函数具有传统上使用索引作为占位符在格式字符串中，例如， `String.Format("Expected: {0} Received: {1}.", expected, received`)。 当然，添加一个新的值始终涉及向上参数计数和重新编号占位符，以正确的顺序参数列表中插入新的自变量的一个令人讨厌小任务。

C#6 的新字符串内插功能极大地改进`String.Format`。 现在，可以直接命名中带有前缀的字符串变量`$`。 例如：

```csharp
$"Expected: {expected} Received: {received}."
```

变量，当然，检查和拼写错误或非可用变量将导致编译器错误。

占位符不需要进行简单的变量，它们可以是任何表达式。 在这些占位符，您可以使用引号*而无需*转义这些引用。 例如，请注意`"s"`在下面的示例：

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

字符串内插支持的对齐方式和格式设置语法`String.Format`。 正如您之前编写`{index, alignment:format}`，请在C#6 您编写`{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```
中的结果：

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

字符串内插是语法糖`String.Format`： 它不能用于`@""`字符串和不符合`const`，即使使用没有占位符：

```csharp
const string s = $"Foo"; //Error : const requires value
```

在常用的用例生成函数的参数和字符串内插的仍需要谨慎转义、 编码和区域性的问题。 SQL 和 URL 查询至关重要，当然，若要清理。 如同`String.Format`，字符串内插使用`CultureInfo.CurrentCulture`。 使用`CultureInfo.InvariantCulture`是稍微罗嗦：

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>初始化

C#6 提供了多种简洁的方式来指定属性、 字段和成员。

### <a name="auto-property-initialization"></a>自动属性初始化

现在可以在字段一样的简洁方法中初始化自动属性。 可以使用一个 getter 编写不可变的自动属性：

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

在构造函数中，可以设置仅定义了 getter 的自动属性的值：

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

此初始化自动属性是一项常规节省空间的功能并希望强调在其对象中的不可变性的开发人员一件好事。

### <a name="index-initializers"></a>索引初始值设定项

C#6 引入了索引初始值设定项，可用于在具有索引器的类型中设置的键和值。 通常情况下，这是用于`Dictionary`-样式数据结构：

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>Expression-bodied 函数成员

Lambda 函数有几个好处，其中之一只需节省空间。 同样，表达式主体类成员允许较小的函数比在以前版本的可能更简洁地表示为C#6。

Expression-bodied 函数成员使用 lambda 箭头语法而不是传统的数据块语法：

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

请注意，lambda 箭头语法不使用显式`return`。 用于函数，可返回`void`，还必须是语句表达式：

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

表达式主体成员都将仍然遵守规则的`async`方法，但不是属性的支持：

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Exceptions

没有任何有关它的两种方式： 异常处理是难以正确。 中的新增功能C#6 进行异常处理更灵活、 一致。

### <a name="exception-filters"></a>异常筛选器

根据定义，在异常情况下，将发生异常，它可以是到原因和有关代码很难*所有*特定类型的异常可能会出现的方式。 C#6 引入了与运行时计算的筛选器防止执行处理程序的功能。 这是通过添加`when (bool)`后正常模式`catch(ExceptionType)`声明。 在下面的示例，筛选器用于区分到相关的分析错误`date`参数而不是其他分析错误。

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>...在 catch 中等待最后...

`async`中引入的功能C#5 已突破的语言。 在C#第 5`await`中不允许使用`catch`并`finally`阻止给定的值是干扰`async/await`功能。 C#6 中删除此限制，允许异步结果，如以下代码片段中所示，一致地等待通过计划：

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>总结

C#语言的不断发展时也升级最佳实践和支持工具使开发人员提高工作效率。 本文档提供中的新语言功能的概述C#6 和简要演示了如何使用它们。

## <a name="related-links"></a>相关链接

- [中的新语言功能C#6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)

