---
title: C#6新功能概述
description: C#语言版本6继续演化语言以降低样本代码，提高清晰度和一致性。 更干净的初始化语法，在 catch/finally 块中使用 await 的能力，以及 null 条件？ 运算符特别有用。
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 1db7ee95ec261739463fa2584f4acf493ac71217
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014823"
---
# <a name="c-6-new-features-overview"></a>C#6新功能概述

_C#语言版本6继续演化语言以降低样本代码，提高清晰度和一致性。更干净的初始化语法，在 catch/finally 块中使用 await 的能力，以及 null 条件？运算符特别有用。_

> [!NOTE]
> 有关最新版本的C#语言（版本7）的信息，请参阅文章[7.0 中C#的新增功能](/dotnet/csharp/whats-new/csharp-7)

本文档介绍C# 6 的新功能。 Mono 编译器完全支持此功能，开发人员可以在所有 Xamarin 目标平台上开始使用新功能。

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**6视频中C#的新增功能**

## <a name="using-c-6"></a>使用C# 6

C# 6 编译器用于 Visual Studio for Mac 的所有最新版本。
使用命令行编译器的那些应用程序应确认 `mcs --version` 返回4.0 或更高版本。
Visual Studio for Mac 用户可以检查是否已安装 Mono 4 （或更高版本），方法是参考**关于 Visual Studio for Mac > Visual Studio for Mac > 显示详细信息**。

## <a name="less-boilerplate"></a>不太样板
### <a name="using-static"></a>using static
枚举和某些类（如 `System.Math`）主要用于静态值和函数。 在C# 6 中，您可以使用单个`using static`语句导入某个类型的所有静态成员。 比较5和C# C# 6 中的典型三角函数：

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

`using static` 不会直接访问 `Math.PI` 和 `Math.E`等公共 `const` 字段：

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>使用 static with Extension 方法

`using static` 设备的运行方式与扩展方法稍有不同。 尽管扩展方法是使用 `static`编写的，但在没有要操作的实例的情况下，这些方法没有意义。 因此，当 `using static` 与定义扩展方法的类型一起使用时，扩展方法将在其目标类型（方法的 `this` 类型）中变为可用。 例如，`using static System.Linq.Enumerable` 可用于扩展 `IEnumerable<T>` 对象的 API，而无需引入所有 LINQ 类型：

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

前面的示例演示了行为差异：扩展方法 `Enumerable.Where` 与数组相关联，而 `String.Join` 可以调用静态方法而无需引用 `String` 类型。

### <a name="nameof-expressions"></a>nameof 表达式
有时，您需要引用您为变量或字段提供的名称。 在C# 6 中，`nameof(someVariableOrFieldOrType)`将返回`"someVariableOrFieldOrType"`的字符串。 例如，当引发 `ArgumentException` 您很可能想要将哪个参数命名为无效：

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

`nameof` 表达式的主要优点是它们是类型检查的，并与工具支持的重构兼容。 在使用 `string` 动态关联类型的情况下，`nameof` 表达式的类型检查特别有用。 例如，在 iOS 中，`string` 用于指定 `UITableView`中用于对 `UITableViewCell` 对象进行原型的类型。 `nameof` 可以确保此关联不会因拼写错误或草率重构而失败：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

虽然可将限定名称传递到 `nameof`，但仅返回最后一个元素（在最后一个 `.`之后）。 例如，可以在 Xamarin 中添加数据绑定。 Forms：

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

对 `SetBinding` 的两个调用将传递相同的值： `nameof(ReactiveType.StringField)` `"StringField"`，而不是 `"ReactiveType.StringField"` 最初预期的值。

## <a name="null-conditional-operator"></a>Null 条件运算符
早期版本的C#更新介绍了可为 null 的类型和 null 合并运算符的概念，`??`在处理可以为 null 值的值时减少样本代码量。 C#6将继续此主题，其中包含 "null 条件运算符"`?.`。 当在表达式的右侧的对象上使用时，如果对象不 `null`，则为 null 条件运算符将返回成员值，否则 `null`：

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

（`length0` 和 `length1` 都推断为类型 `int?`）

上一示例中的最后一行显示了 `?` 的 null 条件运算符与 `??` null 合并运算符的组合。 新C#的 6 null 条件运算符返回数组中第2个元素上的`null`，此时，空合并运算符将在中启动，并向`lengths`的数组提供0（当然，这是特定于问题的）。

对于许多应用程序而言，null 条件运算符应在很大程度上减少不必要的样本的空检查量。

由于歧义，对 null 条件运算符有一些限制。 你不能立即使用带括号的参数列表的 `?`，因为你可能希望使用委托：

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

不过，`Invoke` 可用于将 `?` 与自变量列表分开，并且仍是对 `null`检查样本块的标记改进：

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>字符串内插
`String.Format` 函数过去使用索引作为格式字符串中的占位符，如 `String.Format("Expected: {0} Received: {1}.", expected, received`）。 当然，添加一个新值始终涉及一个令人讨厌的小任务：计算参数、对占位符重新编号，以及在参数列表中的正确序列中插入新的参数。

C#6的新的字符串内插功能在 `String.Format`上大大提高。 现在，你可以直接在带有 `$`前缀的字符串中命名变量。 例如：

```csharp
$"Expected: {expected} Received: {received}."
```

当然，变量已选中，并且拼写错误或不可用的变量将导致编译器错误。

占位符不需要是简单变量，它们可以是任何表达式。 在这些占位符中，可以使用引号，*而无需*转义这些引号。 例如，请注意以下各项中的 `"s"`：

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

字符串内插支持 `String.Format`的对齐和格式设置语法。 正如你之前编写的 `{index, alignment:format}`， C#在6中编写`{placeholder, alignment:format}`：

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

结果为：

```
The value is       1.00.
The value is       2.00.
The value is       3.00.
The value is       4.00.
The value is      12.00.
The value is 123,456.00.
Minimum is 1.00.
```

字符串内插为 `String.Format`语法：不能与 `@""` 字符串一起使用，也不能与 `const`兼容，即使未使用占位符也是如此：

```csharp
const string s = $"Foo"; //Error : const requires value
```

在使用字符串内插生成函数参数的常见用例中，仍需要注意转义、编码和区域性问题。 当然，SQL 和 URL 查询对净化至关重要。 与 `String.Format`一样，字符串内插使用 `CultureInfo.CurrentCulture`。 使用 `CultureInfo.InvariantCulture` 稍微罗嗦一些：

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>初始化

C#6提供了许多简洁的方法来指定属性、字段和成员。

### <a name="auto-property-initialization"></a>自动属性初始化

现在可以用与字段相同的简洁方式初始化自动属性。 不可变的自动属性只能用 getter 编写：

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

在构造函数中，可以设置仅 getter 自动属性的值：

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

自动属性的这一初始化是常规的空间保存功能，并且是希望在其对象中强调不 boon 的开发人员。

### <a name="index-initializers"></a>索引初始值设定项

C#6引入了索引初始值设定项，这允许您设置具有索引器的类型中的键和值。 通常，这适用于 `Dictionary`样式的数据结构：

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

### <a name="expression-bodied-function-members"></a>Expression-expression-bodied 函数成员

Lambda 函数有多个优点，其中一个只是节省空间。 同样，在早期版本的C# 6 中，表达式 expression-bodied 类成员允许用比以往更简单的方式来表示更小的函数。

表达式 expression-bodied 函数成员使用 lambda 箭头语法，而不是传统的块语法：

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

请注意，lambda 箭头语法不使用显式 `return`。 对于返回 `void`的函数，该表达式还必须是语句：

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

表达式 expression-bodied 成员仍受支持方法的规则 `async`，但不支持属性：

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>异常

它没有两种方法：异常处理很难获得。 6中的C#新功能使得异常处理更为灵活和一致。

### <a name="exception-filters"></a>异常筛选器

按照定义，异常会在异常情况下发生，并且可能非常难以理解特定类型的异常的*所有*方法。 C#6引入了使用运行时计算筛选器保护执行处理程序的功能。 这是通过在正常 `catch(ExceptionType)` 声明后添加 `when (bool)` 模式来完成的。 在下面的中，筛选器将与 `date` 参数相关的分析错误与其他分析错误区分开来。

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

### <a name="await-in-catchfinally"></a>正在 catch 中等待最后 。

5中C#引入的 `async` 功能是该语言的游戏转换器。 在C# 5 中，不允许在`catch`和`finally`块中使用`await`，因此，对`async/await`功能的值具有干扰。 C#6消除了这种限制，允许通过程序一致地等待异步结果，如以下代码片段所示：

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

此C#语言不断发展，使开发人员能够更高效地工作，同时还能提升良好的做法和支持工具。 本文档概述了6中C#的新语言功能，并简要说明了如何使用这些功能。

## <a name="related-links"></a>相关链接

- [6中的C#新语言功能](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
