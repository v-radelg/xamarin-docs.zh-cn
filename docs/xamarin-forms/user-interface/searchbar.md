---
title: Xamarin. Forms SearchBar
description: Xamarin SearchBar 是用于启动搜索的用户输入控件。 SearchBar 控件支持占位符文本、查询输入、执行和取消。 本文介绍如何使用 XAML 和代码中的 SearchBar。
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/12/2019
ms.openlocfilehash: 41bb9e082f042e7ca2933d72b4b71a4ff6c4fef4
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "69658027"
---
# <a name="xamarinforms-searchbar"></a>Xamarin. Forms SearchBar

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin [`SearchBar`](xref:Xamarin.Forms.SearchBar)是用于启动搜索的用户输入控件。 `SearchBar`控件支持占位符文本、查询输入、搜索执行和取消。 以下屏幕截图显示`SearchBar`了一个查询，其中显示了`ListView`中的结果：

[![IOS 和 Android 上 SearchBar 的屏幕截图](searchbar-images/device-searchbars-cropped.png "IOS 和 Android 上的 SearchBar")](searchbar-images/device-searchbars.png#lightbox "IOS 和 Android 上的 SearchBar")

`SearchBar`类定义以下属性：

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)`Color`定义 "取消" 按钮的颜色的。
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)一个`FontAttributes`枚举值，该值确定`SearchBar`字体是粗体、斜体还是两者都不是。
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)是一个`string` ，它确定使用`SearchBar`的字体系列。
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)可以是`NamedSize`枚举值， `double`也可以是表示平台上的特定字体大小的值。
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)一个`TextAlignment`枚举值，该值定义查询文本的水平对齐方式。
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder)`string`定义占位符文本的，如 "Search ..."。
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor)`Color`定义占位符文本的颜色的。
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)是一个`ICommand` ，它允许将用户操作（如指指单击或单击）绑定到在 viewmodel 上定义的命令。
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)指定应传递到的`SearchCommand`参数的。 `object`
* [`Text`](xref:Xamarin.Forms.SearchBar.Text)是包含中的查询文本的。 `SearchBar` `string`
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor)`Color`定义查询文本颜色的。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这`SearchBar`意味着可以对进行自定义，并使其成为数据绑定的目标。 在上指定的`SearchBar`字体属性与自定义其他[Xamarin](~/xamarin-forms/user-interface/text/index.md)上的文本一致。 有关详细信息，请参阅[Xamarin 中的字体](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="create-a-searchbar"></a>创建 SearchBar

`SearchBar`可以在 XAML 中实例化。 可以设置`Placeholder`其可选属性，以在 "查询" 输入框中定义提示文本。 的`Placeholder`默认值为空字符串，因此如果未设置占位符，则不会出现占位符。 下面的示例演示如何使用可选`SearchBar` `Placeholder`的属性集在 XAML 中实例化：

```xaml
<SearchBar Placeholder="Search items..." />
```

也`SearchBar`可以在代码中创建：

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>SearchBar 外观属性

`SearchBar`控件定义许多自定义控件外观的属性。 下面的示例演示如何在 XAML 中`SearchBar`实例化具有指定的多个属性的：

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

在代码中创建`SearchBar`对象时，还可以指定这些属性：

```csharp
SearchBar searchBar = new SearchBar
{
    Placeholder = "Search items...",
    PlaceholderColor = Color.Orange,
    TextColor = Color.Orange,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

以下屏幕截图显示了生成`SearchBar`的控件：

[![IOS 和 Android 上自定义 SearchBar 的屏幕截图](searchbar-images/device-searchbars-styled-cropped.png "IOS 和 Android 上的自定义 SearchBar")](searchbar-images/device-searchbars-styled.png#lightbox "IOS 和 Android 上的自定义 SearchBar")

## <a name="perform-a-search-with-event-handlers"></a>使用事件处理程序执行搜索

可以通过将事件处理程序附加`SearchBar`到以下事件之一来使用控件执行搜索：

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)当用户单击 "搜索" 按钮或按 "enter" 键时调用。
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged)只要更改了查询框中的文本，就会调用。

下面的示例演示如何在 XAML 中附加到`TextChanged`事件的事件处理程序， `ListView`并使用显示搜索结果：

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

事件处理程序还可以附加到在代码`SearchBar`中创建的：

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

无论是通过 XAML 还是代码创建的`SearchBar` ，代码隐藏文件中的事件处理程序都是相同的：`TextChanged`

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

前面的示例说明存在一个`DataService` `GetSearchResults`具有可返回与查询匹配的项的方法的类。 `ItemsSource` `ListView`控件的`Text` 属性`GetSearchResults`值将传递给方法，结果用于更新控件的属性。 `SearchBar` 总体效果是，搜索结果显示在`ListView`控件中。

示例应用程序提供了`DataService`一个可用于测试搜索功能的类实现。

## <a name="perform-a-search-using-a-viewmodel"></a>使用 viewmodel 执行搜索

通过将`SearchCommand`和属性绑定到`ICommand`实现， `SearchCommandParameter`可以在没有事件处理程序的情况下执行搜索。 示例项目使用模型-视图-ViewModel （MVVM）模式演示这些实现。 有关与 MVVM 的数据绑定的详细信息，请参阅[通过 mvvm 进行数据绑定](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

示例应用程序中的 viewmodel 包含以下代码：

```csharp
public class SearchViewModel : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected virtual void NotifyPropertyChanged([CallerMemberName] string propertyName = "")
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    public ICommand PerformSearch => new Command<string>((string query) =>
    {
        SearchResults = DataService.GetSearchResults(query);
    });

    private List<string> searchResults = DataService.Fruits;
    public List<string> SearchResults
    {
        get
        {
            return searchResults;
        }
        set
        {
            searchResults = value;
            NotifyPropertyChanged();
        }
    }
}
```

> [!NOTE]
> Viewmodel 假设存在`DataService`能够执行搜索的类。 `DataService`类（包括示例数据）在示例应用程序中可用。

以下 XAML 演示了如何`SearchBar` `ListView`使用显示搜索结果的控件将绑定到示例 viewmodel：

```xaml
<ContentPage ...>
    <ContentPage.BindingContext>
        <viewmodels:SearchViewModel />
    </ContentPage.BindingContext>
    <StackLayout ...>
        <SearchBar x:Name="searchBar"
                   ...
                   SearchCommand="{Binding PerformSearch}"
                   SearchCommandParameter="{Binding Text, Source={x:Reference searchBar}}"/>
        <ListView x:Name="searchResults"
                  ...
                  ItemsSource="{Binding SearchResults} />
    </StackLayout>
</ContentPage>
```

此示例将设置`BindingContext`为`SearchViewModel`类的实例。 它将`SearchCommand`属性绑定到`ICommand` `Text` `SearchBar` viewmodel中`PerformSearch`的，并将属性绑定到属性。`SearchCommandParameter` 属性绑定到 viewmodel 的属性`SearchResults` `ListView.ItemsSource` 。

有关`ICommand`接口和绑定的详细信息，请参阅[Xamarin。 Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)和[ICommand 接口](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="related-links"></a>相关链接

* [SearchBar 演示](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin. Forms 文本控件](~/xamarin-forms/user-interface/text/index.md)
* [Xamarin 中的字体](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin. 窗体数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
