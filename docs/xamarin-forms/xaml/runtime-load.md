---
title: 加载 XAML 在 Xamarin.Forms 中的运行时
description: 可以加载和使用 LoadFromXaml 扩展方法在运行时分析 XAML。
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: ce8ba32a1a6a1f69033615558c7ebf15d41e70fe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178041"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>加载 XAML 在 Xamarin.Forms 中的运行时

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)

[ `Xamarin.Forms.Xaml` ](xref:Xamarin.Forms.Xaml)命名空间包含两个[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)扩展方法，可用于加载，并在运行时分析 XAML。

## <a name="background"></a>背景

当构造 Xamarin.Forms XAML 类时， [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)间接调用方法。 这是因为 XAML 的代码隐藏文件类调用`InitializeComponent`来自其构造函数的方法：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

当 Visual Studio 将生成包含 XAML 文件的项目时，它会分析要生成的 XAML 文件C#代码文件 (例如， **MainPage.xaml.g.cs**)，其中包含的定义`InitializeComponent`方法：

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent`方法调用[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法从.NET Standard 库中提取的 XAML 文件 （或其编译的二进制文件）。 经提取，它初始化的所有 XAML 文件中定义对象、 将它们组合在一起在父-子关系中连接、 将附加到 XAML 文件中设置的事件的代码中定义的事件处理程序和设置的内容作为对象的结果树页。

## <a name="loading-xaml-at-runtime"></a>在运行时加载 XAML

[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法都是`public`，因此可以调用从 Xamarin.Forms 应用程序加载，并且在运行时分析 XAML。 这将允许从 web 服务，从 XAML 中，创建所需的视图和应用程序中显示下载 XAML 的应用程序等方案。

> [!WARNING]
> 在运行时加载 XAML 具有显著的性能开销，并通常应避免使用。

下面的代码示例显示了简单的使用情况：

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

在此示例中， [ `Button` ](xref:Xamarin.Forms.Button)创建实例后，使用它的[ `Text` ](xref:Xamarin.Forms.Button.Text)属性值被设置从 XAML 中定义`string`。 `Button`随后将添加到[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)页 XAML 中定义的。

> [!NOTE]
> [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)扩展方法允许指定泛型类型参数。 但是，它很少需要指定类型参数，因为它将对其操作从实例的类型推断。

[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法可用于放任何 XAML，大量使用下面的示例都[ `ContentPage` ](xref:Xamarin.Forms.ContentPage) ，然后导航到它：

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>访问元素

在与运行时加载 XAML [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*)方法不允许指定运行时对象名称的 XAML 元素的强类型访问 (使用`x:Name`)。 但是，这些 XAML 元素可以使用检索[ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法，然后再根据需要访问：

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

在此示例中，为 XAML [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)进行扩充。 包含此 XAML [ `Label` ](xref:Xamarin.Forms.Label)名为`monkeyName`，使用检索[ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*)方法，它具有之前[ `Text` ](xref:Xamarin.Forms.Label.Text)设置属性。

## <a name="related-links"></a>相关链接

- [LoadRuntimeXAML （示例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)
