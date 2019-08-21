---
title: Xamarin。窗体 MenuItem
description: MenuItem 类用于为菜单项 (如 ListView 项上下文菜单和 Shell 应用程序飞出菜单) 创建菜单项。
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: 396853e2e61904689069b7abd157c73d1da59fed
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658096"
---
# <a name="xamarinforms-menuitem"></a>Xamarin。窗体 MenuItem

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)

Xamarin [`MenuItem`](xref:Xamarin.Forms.MenuItem)类定义菜单的菜单项, `ListView`如项上下文菜单和 Shell 应用程序飞出菜单。

以下屏幕截图显示`MenuItem` iOS 和 Android `ListView`上上下文菜单中的对象:

[(menuitem-images/menuitem-demo-cropped.png "Ios 和 android 上的") !["ios 和 android 上的菜单项"]菜单项](menuitem-images/menuitem-demo-full.png#lightbox "IOS 和 Android 上的菜单项full image")

`MenuItem`类定义以下属性:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)是一个`ICommand` , 它允许将用户操作 (如指指单击或单击) 绑定到在 viewmodel 上定义的命令。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)指定应传递到的`Command`参数的。 `object`
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)定义显示图标的值。`ImageSource`
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)一个`bool`值, 该值指示`MenuItem`是否从列表中移除其关联的 UI 元素。
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)一个`bool`值, 该值确定此对象是否响应用户输入。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)指定显示文本的值。`string`

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的, 因此, `MenuItem`实例可以是数据绑定的目标。

## <a name="create-a-menuitem"></a>创建 MenuItem

`MenuItem`对象可用于`ListView`对象项的上下文菜单中。 最常见的`MenuItem`模式是`ViewCell`在实例中创建对象, `DataTemplate`该对象用作的对象`ListView`。 `ItemTemplate` 填充对象时`DataTemplate`, 它将使用创建每个项, 并在为`MenuItem`项激活上下文菜单时公开选项。 `ListView`

下面的示例显示`MenuItem`了`ListView`对象在对象上下文中的实例化:

```xaml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.ContextActions>
                    <MenuItem Text="Context Menu Option" />
                </ViewCell.ContextActions>
                <Label Text="{Binding .}" />
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

也`MenuItem`可以在代码中创建:

```csharp
// A function returns a ViewCell instance that
// is used as the template for each list item
DataTemplate dataTemplate = new DataTemplate(() =>
{
    // A Label displays the list item text
    Label label = new Label();
    label.SetBinding(Label.TextProperty, ".");

    // A ViewCell serves as the DataTemplate
    ViewCell viewCell = new ViewCell
    {
        View = label
    };

    // Add a MenuItem instance to the ContextActions
    MenuItem menuItem = new MenuItem
    {
        Text = "Context Menu Option"
    };
    viewCell.ContextActions.Add(menuItem);

    // The function returns the custom ViewCell
    // to the DataTemplate constructor
    return viewCell;
});

// Finally, the dataTemplate is provided to
// the ListView object
ListView listView = new ListView
{
    ...
    ItemTemplate = dataTemplate
};
```

## <a name="define-menuitem-behavior-with-events"></a>定义包含事件的 MenuItem 行为

`MenuItem` 类会公开 `Clicked` 事件。 可以将事件处理程序附加到此事件, 以响应在 XAML 中的`MenuItem`点击或单击实例:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

事件处理程序也可以在代码中附加:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

前面的示例引用`OnItemClicked`了事件处理程序。 下面的代码演示实现示例:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    // The sender is the menuItem
    MenuItem menuItem = sender as MenuItem;

    // Access the list item through the BindingContext
    var contextItem = menuItem.BindingContext;

    // Do something with the contextItem here
}
```

## <a name="define-menuitem-behavior-with-mvvm"></a>通过 MVVM 定义 MenuItem 行为

类通过[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象和`ICommand`接口支持模型-视图-ViewModel (MVVM) 模式。 `MenuItem` 以下 XAML 显示`MenuItem`了绑定到 viewmodel 上定义的命令的实例:

```xaml
<ContentPage.BindingContext>
    <viewmodels:ListPageViewModel />
</ContentPage.BindingContext>

<StackLayout>
    <Label Text="{Binding Message}" ... />
    <ListView ItemsSource="{Binding Items}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ViewCell.ContextActions>
                        <MenuItem Text="Edit"
                                    IconImageSource="icon.png"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.EditCommand}"
                                    CommandParameter="{Binding .}"/>
                        <MenuItem Text="Delete"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.DeleteCommand}"
                                    CommandParameter="{Binding .}"/>
                    </ViewCell.ContextActions>
                    <Label Text="{Binding .}" />
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

在前面的示例中, `MenuItem`定义了两个对象`Command` , `CommandParameter`并将其和属性绑定到 viewmodel 上的命令。 Viewmodel 包含 XAML 中引用的命令:

```csharp
public class ListPageViewModel : INotifyPropertyChanged
{
    ...

    public ICommand EditCommand => new Command<string>((string item) =>
    {
        Message = $"Edit command was called on: {item}";
    });

    public ICommand DeleteCommand => new Command<string>((string item) =>
    {
        Message = $"Delete command was called on: {item}";
    });
}
```

示例应用程序包含一个`DataService`类, 该类用于获取用于`ListView`填充对象的项的列表。 使用`DataService`类中的项来实例化 viewmodel, 并将其设置为`BindingContext`代码隐藏中的:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>MenuItem 图标

> [!WARNING]
> `MenuItem`对象只显示 Android 上的图标。 在其他平台上, 仅显示由`Text`属性指定的文本。

 图标是使用属性指定`IconImageSource`的。 如果指定了图标, 则不会显示由`Text`属性指定的文本。 以下屏幕截图显示 Android `MenuItem`上带有图标的:

!["Android 上的 MenuItem 图标屏幕截图"](menuitem-images/menuitem-android-icon.png "Android 上菜单菜单的屏幕截图")

有关在 Xamarin 中使用图像的详细信息, 请参阅[xamarin 中的图像](~/xamarin-forms/user-interface/images.md)。

## <a name="cross-platform-context-menu-behavior"></a>跨平台上下文菜单行为

上下文菜单在每个平台上以不同的方式进行访问和显示。

在 Android 上, 通过长时间按列表项激活上下文菜单。 上下文菜单替换标题和导航栏区域, `MenuItem`选项显示为水平按钮。

!["Android 上上下文菜单的屏幕截图"](menuitem-images/menuitem-android-icon.png "Android 上上下文菜单的屏幕截图")

在 iOS 上, 通过在列表项上轻扫激活上下文菜单。 上下文菜单显示在列表项上, 并`MenuItems`显示为水平按钮。

!["IOS 上上下文菜单的屏幕截图"](menuitem-images/menuitem-ios-contextmenu.png "IOS 上上下文菜单的屏幕截图")

在 UWP 上, 右键单击列表项即可激活上下文菜单。 上下文菜单将作为垂直列表显示在光标附近。

!["UWP 上的上下文菜单的屏幕截图"](menuitem-images/menuitem-uwp.png "UWP 上的上下文菜单的屏幕截图")

## <a name="related-links"></a>相关链接

* [MenuItem 演示](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)
* [Xamarin 中的图像](~/xamarin-forms/user-interface/images.md)
