---
title: Xamarin.Forms 选项卡式页面
description: Xamarin.Forms TabbedPage 包含一系列选项卡和较大的详细信息区域，其中每个选项卡都将内容加载到详细信息区域。 本文演示如何使用 TabbedPage 浏览页集合。
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 6979b4d3e0d750ee962346a94dd832c86c92d995
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652950"
---
# <a name="xamarinforms-tabbed-page"></a>Xamarin.Forms 选项卡式页面

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

_Xamarin.Forms TabbedPage 包含一系列选项卡和较大的详细信息区域，其中每个选项卡都将内容加载到详细信息区域。本文演示如何使用 TabbedPage 浏览页集合。_

## <a name="overview"></a>概述

以下屏幕截图显示每个平台上的 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

![](tabbed-page-images/tab1.png "TabbedPage 示例")

以下屏幕截图显示每个平台上的选项卡格式：

![](tabbed-page-images/tabbedpage-components.png "TabbedPage 选项卡组件")

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 的布局以及其选项卡取决于不同的平台：

- 在 iOS 上，选项卡的列表显示在屏幕的底部，而详细信息区域显示在上方。 每个选项卡还有一个图标图像，正常分辨率为 30x30 具有透明度的 PNG，高分辨率为 60x60，iPhone 6 Plus 分辨率为 90x90。 如果有五个以上的选项卡，会显示“更多”选项卡，可用于访问其他选项卡  。 有关在 Xamarin.Forms 应用程序中加载图像的详细信息，请参阅[使用图像](~/xamarin-forms/user-interface/images.md)。 有关图标要求的详细信息，请参阅[创建选项卡式应用程序](~/ios/user-interface/controls/creating-tabbed-applications.md)。

  > [!NOTE]
  > 请注意，`TabbedRenderer` for iOS 有可替代的 `GetIcon` 方法，可以用来从指定的源加载选项卡图标。 使用此替代方法可以将 SVG 图像用作 `TabbedPage` 上的图标。 此外，可以提供选定和未选定版本的图标。

- 在 Android 上，默认情况下选项卡列表显示在屏幕顶部，详细信息区域显示在下方。 但是，可以使用特定于平台布局将选项卡列表移动至屏幕底部。 有关详细信息，请参阅[设置 TabbedPage 工具栏位置和颜色](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md)。

  > [!NOTE]
  > 请注意，当在 Android 上使用 AppCompat 时，每个选项卡也将显示一个图标。 此外，`TabbedPageRenderer` for Android AppCompat 有可替代的 `GetIconDrawable` 方法，可以用来从自定义 `Drawable` 加载选项卡图标。 使用此替代方法可以将 SVG 图像用作 `TabbedPage` 上的图标，并且可以使用顶部和底部选项卡。 或者，可替代的 `SetTabIcon` 方法可用于从顶部标签栏的自定义 `Drawable` 加载选项卡图标。

- 考虑到 Windows 平板电脑外形尺寸，选项卡并不总是可见的，用户需要向下滑动（或如果连接了鼠标用右键点击）以查看 `TabbedPage` 中的选项卡（如下所示）。

    ![](tabbed-page-images/windows-tabs.png "Windows 上的 TabbedPage 选项卡")

## <a name="creating-a-tabbedpage"></a>创建 TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 定义以下属性:

- [`Color`](xref:Xamarin.Forms.Color) 类型的 [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor)，设置选项卡栏的背景色。
- [`Color`](xref:Xamarin.Forms.Color) 类型的 [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor)，设置选项卡栏上的文本的颜色。
- [`Color`](xref:Xamarin.Forms.Color) 类型的 [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor)，选定选项卡后选项卡的颜色。
- [`Color`](xref:Xamarin.Forms.Color) 类型的 [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor)，未选定选项卡时选项卡的颜色。

所有这些属性都由 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 对象提供支持，这意味着可设置他们的样式，并且这些属性可以作为数据绑定的目标。

可以使用两种方法创建 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

- 用 [`Page`](xref:Xamarin.Forms.Page) 对象的子集合（例如 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例的集合）来[填充](#Populating_a_TabbedPage_with_a_Page_Collection) [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)。
- 将集合[分配](#Populating_a_TabbedPage_with_a_Template)给 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 属性，并将 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 分配给 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 属性以返回集合中对象的页面。

使用这两种方法，[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 将在用户选择每个标签时显示每个页面。

> [!NOTE]
> 建议仅使用 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例填充 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)。 这将有助于确保在所有平台上都有一致的用户体验。

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>使用页集合填充 TabbedPage

以下 XAML 代码示例显示通过使用子级 [`Page`](xref:Xamarin.Forms.Page) 对象的集合填充来构建 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" IconImageSource="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

以下代码示例演示了在 C# 中创建的等效 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

使用两个子级 [`Page`](xref:Xamarin.Forms.Page) 对象填充 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)。 第一个子级是 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例，第二个选项卡是包含 `ContentPage` 实例的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)。

> [!NOTE]
> [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 不支持 UI 虚拟化。 因此，如果 `TabbedPage` 包含太多子元素，可能会影响性能。

以下屏幕截图显示了 `TodayPage`[`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例，该实例显示在“今天”选项卡上  ：

![](tabbed-page-images/today-page.png "TabbedPage 中的 ContentPage")

选择“计划”选项卡以显示 `SchedulePage` [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例，该实例被包装在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 实例中，并由以下屏幕截图显示  ：

![](tabbed-page-images/schedule-page.png "TabbedPage 中的 NavigationPage")

有关 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 布局的详细信息，请参阅[执行导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

> [!NOTE]
> 虽然将 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 放入 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 是可以接受的，但不建议将 `TabbedPage` 放入 `NavigationPage`。 这是因为，在 iOS 上，`UITabBarController` 始终充当 `UINavigationController` 的包装器。 有关详细信息，请参阅 iOS 开发人员库中的[组合的视图控制器接口](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html)。

#### <a name="navigation-inside-a-tab"></a>选项卡内的导航

如下面的代码示例所示，可以通过调用 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 属性上的 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 方法来从第二个选项卡执行导航：

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

这会将 `UpcomingAppointmentsPage` 实例推送到导航堆栈中，在堆栈中，它成为活动页。 以下屏幕截图演示了此过程：

![](tabbed-page-images/navigationpage.png "选项卡内的导航")

有关使用 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 类执行导航的详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>使用模板填充 TabbedPage

下面的 XAML 代码示例演示通过将 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 分配给 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 属性来构造 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)，以返回集合中对象的页面：

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" IconImageSource="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

通过在代码隐藏文件的构造函数中设置 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 属性，使用数据填充 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

以下代码示例演示了在 C# 中创建的等效 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

每个选项卡都会显示 [`ContentPage`](xref:Xamarin.Forms.ContentPage)，它使用一系列 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 和 [`Label`](xref:Xamarin.Forms.Label) 实例来显示选项卡的数据。以下屏幕截图显示 Tamarin 选项卡的内容  ：

![](tabbed-page-images/tab3.png "使用模板填充 TabbedPage")

然后选择另一个选项卡显示该选项卡的内容。

> [!NOTE]
> [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 不支持 UI 虚拟化。 因此，如果 `TabbedPage` 包含太多子元素，可能会影响性能。

有关 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 的详细信息，请参阅 Charles Petzold 所著的 Xamarin.Forms 书籍的[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)。

## <a name="summary"></a>总结

本文演示如何使用 TabbedPage 浏览页集合。 Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 包含一系列选项卡和较大的详细信息区域，其中每个选项卡都将内容加载到详细信息区域。


## <a name="related-links"></a>相关链接

- [页类型](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
