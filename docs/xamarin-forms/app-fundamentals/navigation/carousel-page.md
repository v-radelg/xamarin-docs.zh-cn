---
title: Xamarin.Forms Carousel Page
description: Xamarin.Forms CarouselPage 页就像一个库，用户可从一侧轻扫到另一侧以浏览内容页面。 本文演示如何使用 CarouselPage 浏览页集合。
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 5cfaeb8589514972dfcccd11009a02c00f95bfac
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696446"
---
# <a name="xamarinforms-carousel-page"></a>Xamarin.Forms Carousel Page

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Xamarin.Forms CarouselPage 页就像一个库，用户可从一侧轻扫到另一侧以浏览内容页面。本文演示如何使用 CarouselPage 浏览页集合。 

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) 取代了 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)并提供一个可供用户轻扫浏览一系列项的可滚动布局。 有关 `CarouselView` 的详细信息，请参阅 [Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md)。

以下屏幕截图显示每个平台上的 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

![](carousel-page-images/thirdpage.png "CarouselPage Third Item")

每个平台上的 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 布局完全相同。 可以通过从右向左轻扫的方式在集合中向前浏览页面，也可以通过从左向右轻扫的方式在集合中向后浏览页面。 以下屏幕截图显示 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 实例中的第一页：

![](carousel-page-images/firstpage.png "CarouselPage First Item")

从右向左轻扫以移动到第二个页面，如以下屏幕截图中所示：

![](carousel-page-images/secondpage.png "CarouselPage Second Item")

再次从右向左轻扫会移动到第三个页面上，而从左向右轻扫则返回到前一页面。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 不支持 UI 虚拟化。 因此，如果 `CarouselPage` 包含太多子元素，可能会影响性能。

如果 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 嵌入到 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 的 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 页，则 [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) 属性应设置为 `false`，以防止 `CarouselPage` 和 `MasterDetailPage` 之间的手势冲突。

有关 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 的详细信息，请参阅 Charles Petzold 所著的 Xamarin.Forms 书籍的[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)。

## <a name="create-a-carouselpage"></a>创建 CarouselPage

可以使用两种方法创建 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

- 使用子 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例集合[填充](#Populating_a_CarouselPage_with_a_Page_Collection) `CarouselPage`。
- 将集合[分配](#Populating_a_CarouselPage_with_a_Template)给 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 属性并将 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 分配给 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 属性以返回集合中对象的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例。

使用这两种方法，`CarouselPage` 将依次显示每个页面，轻扫交互会移动到要显示的下一个页面。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 仅可以使用 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例或 `ContentPage` 派生类填充。

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populate-a-carouselpage-with-a-page-collection"></a>使用页集合填充 CarouselPage

下面的 XAML 代码示例演示显示三个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例的 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <ContentPage>
        <ContentPage.Padding>
          <OnPlatform x:TypeArguments="Thickness">
              <On Platform="iOS, Android" Value="0,40,0,0" />
          </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
            <Label Text="Red" FontSize="Medium" HorizontalOptions="Center" />
            <BoxView Color="Red" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
        </StackLayout>
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
</CarouselPage>
```

下面的代码示例介绍了 C# 中的等效 UI：

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        var redContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                Children = {
                    new Label {
                        Text = "Red",
                        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                        HorizontalOptions = LayoutOptions.Center
                    },
                    new BoxView {
                        Color = Color.Red,
                        WidthRequest = 200,
                        HeightRequest = 200,
                        HorizontalOptions = LayoutOptions.Center,
                        VerticalOptions = LayoutOptions.CenterAndExpand
                    }
                }
            }
        };
        var greenContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };
        var blueContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };

        Children.Add (redContentPage);
        Children.Add (greenContentPage);
        Children.Add (blueContentPage);
    }
}
```

每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 只显示特定颜色的 [`Label`](xref:Xamarin.Forms.Label) 和该颜色的 [`BoxView`](xref:Xamarin.Forms.BoxView)。

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populate-a-carouselpage-with-a-template"></a>使用模板填充 CarouselPage

下面的 XAML 代码示例演示通过将 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 分配给 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 属性来构造 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)，以返回集合中对象的页面：

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <CarouselPage.ItemTemplate>
        <DataTemplate>
            <ContentPage>
                <ContentPage.Padding>
                  <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS, Android" Value="0,40,0,0" />
                  </OnPlatform>
                </ContentPage.Padding>
                <StackLayout>
                    <Label Text="{Binding Name}" FontSize="Medium" HorizontalOptions="Center" />
                    <BoxView Color="{Binding Color}" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
                </StackLayout>
            </ContentPage>
        </DataTemplate>
    </CarouselPage.ItemTemplate>
</CarouselPage>
```

通过在代码隐藏文件的构造函数中设置 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 属性，使用数据填充 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

以下代码示例演示了在 C# 中创建的等效 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        ItemTemplate = new DataTemplate (() => {
            var nameLabel = new Label {
                FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                HorizontalOptions = LayoutOptions.Center
            };
            nameLabel.SetBinding (Label.TextProperty, "Name");

            var colorBoxView = new BoxView {
                WidthRequest = 200,
                HeightRequest = 200,
                HorizontalOptions = LayoutOptions.Center,
                VerticalOptions = LayoutOptions.CenterAndExpand
            };
            colorBoxView.SetBinding (BoxView.ColorProperty, "Color");

            return new ContentPage {
                Padding = padding,
                Content = new StackLayout {
                    Children = {
                        nameLabel,
                        colorBoxView
                    }
                }
            };
        });

        ItemsSource = ColorsDataModel.All;
    }
}
```

每个 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 只显示特定颜色的 [`Label`](xref:Xamarin.Forms.Label) 和该颜色的 [`BoxView`](xref:Xamarin.Forms.BoxView)。

## <a name="related-links"></a>相关链接

- [页类型](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
