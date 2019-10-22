---
title: Xamarin. Forms RefreshView
description: Xamarin RefreshView 是一个容器控件，为可滚动的内容提供拉取到刷新功能。
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
ms.openlocfilehash: b53c58a5e859bf7752855c3954666a062261599d
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697739"
---
# <a name="xamarinforms-refreshview"></a>Xamarin. Forms RefreshView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshview/)

@No__t_0 是一个容器控件，该控件为可滚动的内容提供请求刷新功能。 因此，`RefreshView` 的子级必须是可滚动的控件，如[`ScrollView`](xref:Xamarin.Forms.ScrollView)、 [`CollectionView`](xref:Xamarin.Forms.CollectionView)或[`ListView`](xref:Xamarin.Forms.ListView)。

`RefreshView` 定义以下属性：

- `ICommand` 类型的 `Command`，将在触发刷新时执行。
- `CommandParameter`，属于 `object` 类型，是传递给 `Command` 的参数。
- `bool` 类型的 `IsRefreshing`，指示 `RefreshView` 的当前状态。
- `Color` 类型的 `RefreshColor`，将在刷新过程中出现的进度圆圈的颜色。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着它们可以是数据绑定的目标和样式。

> [!NOTE]
> 在通用 Windows 平台上，可以使用特定于平台的来设置 `RefreshView` 的请求方向。 有关详细信息，请参阅[RefreshView 拉取方向](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)。

## <a name="create-a-refreshview"></a>创建 RefreshView

下面的示例演示如何在 XAML 中实例化 `RefreshView`：

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <ScrollView>
        <FlexLayout Direction="Row"
                    Wrap="Wrap"
                    AlignItems="Center"
                    AlignContent="Center"
                    BindableLayout.ItemsSource="{Binding Items}"
                    BindableLayout.ItemTemplate="{StaticResource ColorItemTemplate}" />
    </ScrollView>
</RefreshView>
```

还可以在代码中创建 `RefreshView`：

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

ScrollView scrollView = new ScrollView();
FlexLayout flexLayout = new FlexLayout { ... };
scrollView.Content = flexLayout;
refreshView.Content = scrollView;
```

在此示例中，`RefreshView` 向刷新功能提供对其子为[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)的[`ScrollView`](xref:Xamarin.Forms.ScrollView)的请求。 @No__t_0 使用可绑定布局，通过绑定到项集合来生成其内容，并使用[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)设置每个项的外观。 有关可绑定布局的详细信息，请参阅[Xamarin 中的可绑定布局](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)。

@No__t_0 属性的值指示 `RefreshView` 的当前状态。 用户触发刷新时，此属性会自动转换为 `true`。 刷新完成后，应将属性重置为 `false`。

当用户启动刷新时，将执行 `Command` 属性定义的 `ICommand`，该属性应刷新正在显示的项。 刷新发生时，会显示刷新可视化效果，其中包含动画进度圆：

[![IOS 和 Android 上 RefreshView 刷新数据的屏幕截图](refreshview-images/default-progress-circle.png "RefreshView 刷新数据")](refreshview-images/default-progress-circle-large.png#lightbox "RefreshView 刷新数据")

> [!NOTE]
> 手动将 `IsRefreshing` 属性设置为 `true` 将触发刷新可视化，并将执行 `Command` 属性定义的 `ICommand`。

## <a name="refreshview-appearance"></a>RefreshView 外观

除了 `RefreshView` 继承自[`VisualElement`](xref:Xamarin.Forms.VisualElement)类的属性以外，`RefreshView` 还会定义 `RefreshColor` 属性。 此属性可设置为定义在刷新过程中出现的进度圆圈的颜色：

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

以下屏幕截图显示了具有 `RefreshColor` 属性集的 `RefreshView`：

[![IOS 和 Android 上带有蓝绿色进度圆圈的 RefreshView 屏幕截图](refreshview-images/teal-progress-circle.png "带有蓝绿色进度圆的 RefreshView")](refreshview-images/teal-progress-circle-large.png#lightbox "带有蓝绿色进度圆的 RefreshView")

此外，`BackgroundColor` 属性可以设置为表示进度圆背景色的[`Color`](xref:Xamarin.Forms.Color) 。

> [!NOTE]
> 在 iOS 上，`BackgroundColor` 属性设置包含进度圆圈的 `UIView` 的背景色。

## <a name="disable-a-refreshview"></a>禁用 RefreshView

应用程序可以输入状态，请求刷新的状态不是有效的操作。 在这种情况下，可以通过将 `RefreshView` 的 `IsEnabled` 属性设置为 `false` 来禁用。 这会阻止用户触发请求刷新。

或者，在定义 `Command` 属性时，可以指定 `ICommand` 的 `CanExecute` 委托，以启用或禁用该命令。

## <a name="related-links"></a>相关链接

- [RefreshView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshview/)
- [Xamarin 中的可绑定布局](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [RefreshView 拉取方向平台特定](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)
