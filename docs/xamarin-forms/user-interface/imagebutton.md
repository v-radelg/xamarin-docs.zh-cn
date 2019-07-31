---
title: Xamarin.Forms ImageButton
description: ImageButton 显示图像和响应点击或单击，将定向的应用程序来执行特定任务。
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 90d860cb6429025672a058e22e76f029872d3b34
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657177"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_ImageButton 显示图像和响应点击或单击，将定向的应用程序来执行特定任务。_

`ImageButton`查看合并[ `Button` ](xref:Xamarin.Forms.Button)视图并[ `Image` ](xref:Xamarin.Forms.Image)视图，以创建其内容的一个按钮是一个图像。 用户按`ImageButton`用手指或用来指示应用程序来执行特定任务的鼠标单击。 但是，与不同`Button`视图中，`ImageButton`视图具有文本和文本外观的概念。

> [!NOTE]
> 虽然[ `Button` ](xref:Xamarin.Forms.Button)视图定义[ `Image` ](xref:Xamarin.Forms.Button.Image)属性，您可在显示的图像`Button`，此属性应显示一个小图标时使用下一步`Button`文本。

本指南中的代码示例摘自[FormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)。

## <a name="setting-the-image-source"></a>设置图像源

`ImageButton` 定义`Source`属性应设置为图像显示在按钮中，图像源为文件、 URI、 资源或流。 了解如何从不同的源加载映像的详细信息，请参阅[Xamarin.Forms 中的映像](images.md)。

下面的示例演示如何实例化`ImageButton`在 XAML 中：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Source`属性指定在显示的图像`ImageButton`。 在此示例中将它设置为本地文件将加载从每个平台项目，从而导致下面的屏幕截图：

[![基本 ImageButton](imagebutton-images/BasicImageButton.png "基本 ImageButton")](imagebutton-images/BasicImageButton-Large.png#lightbox "基本 ImageButton")

默认情况下`ImageButton`都是矩形状，但可以通过使用进行舍入的 it 角`CornerRadius`属性。 有关详细信息`ImageButton`外观，请参阅[ImageButton 外观](#imagebutton-appearance)。

下面的示例演示如何创建在功能上等效于上一个 XAML 示例，但完全在页C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children = { header, imageButton }
        };
    }
}
```

## <a name="handling-imagebutton-clicks"></a>处理 ImageButton 单击

`ImageButton` 定义`Clicked`在用户点击时激发的事件`ImageButton`用手指或鼠标指针。 图面中释放手指或鼠标按钮时触发该事件`ImageButton`。 `ImageButton`必须具有其`IsEnabled`属性设置为`true`响应分流点。

下面的示例演示如何实例化`ImageButton`XAML 和句柄中其`Clicked`事件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand"
                    Clicked="OnImageButtonClicked" />

        <Label x:Name="label"
               Text="0 ImageButton clicks"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Clicked`事件设置为事件处理程序名为`OnImageButtonClicked`位于代码隐藏文件：

```csharp
public partial class ImageButtonDemoPage : ContentPage
{
    int clickTotal;

    public ImageButtonDemoPage()
    {
        InitializeComponent();
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

当`ImageButton`点击，`OnImageButtonClicked`方法执行。 `sender`自变量是`ImageButton`负责此事件。 可以使用此访问`ImageButton`对象，或以区分多个`ImageButton`对象共享相同`Clicked`事件。

此特定`Clicked`处理程序递增计数器，并显示中的计数器值[ `Label` ](xref:Xamarin.Forms.Label):

[![单击此基本 ImageButton](imagebutton-images/ImageButton.png "基本 ImageButton 单击")](imagebutton-images/ImageButton-Large.png#lightbox "基本 ImageButton 单击")

下面的示例演示如何创建在功能上等效于上一个 XAML 示例，但完全在页C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    Label label;
    int clickTotal = 0;

    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };
        imageButton.Clicked += OnImageButtonClicked;

        label = new Label
        {
            Text = "0 ImageButton clicks",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children =
            {
                header,
                imageButton,
                label
            }
        };
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

## <a name="disabling-the-imagebutton"></a>禁用 ImageButton

有时应用程序处于特定状态的特定`ImageButton`单击不是有效的操作。 在这些情况下，`ImageButton`应禁用通过设置其`IsEnabled`属性设置为`false`。

## <a name="using-the-command-interface"></a>使用命令界面

它是应用程序以响应`ImageButton`分流点无需处理`Clicked`事件。 `ImageButton`实现调用了替代通知机制_命令_或_命令_接口。 这包括两个属性：

- `Command` 类型的[ `ICommand` ](xref:System.Windows.Input.ICommand)，在中定义的接口[ `System.Windows.Input` ](xref:System.Windows.Input)命名空间。
- `CommandParameter` 类型的属性[ `Object` ](xref:System.Object)。

尤其是在实现模型-视图-视图模型 (MVVM) 体系结构时，此方法非常适合与数据绑定和。

有关使用命令界面的详细信息，请参阅[使用命令界面](button.md#using-the-command-interface)中[按钮](button.md)指南。

## <a name="pressing-and-releasing-the-imagebutton"></a>按下和释放 ImageButton

除了`Clicked`事件，`ImageButton`还定义了`Pressed`和`Released`事件。 `Pressed`手指按上时发生事件`ImageButton`，或使用指针置于其上按下鼠标按钮`ImageButton`。 `Released`松开手指或鼠标按钮时发生事件。 通常情况下，`Clicked`还在相同的时间触发事件`Released`事件，但如果手指或鼠标指针滑离开的面`ImageButton`之前被释放，`Clicked`事件可能会发生。

有关这些事件的详细信息，请参阅[按下并松开按钮](button.md#pressing-and-releasing-the-button)中[按钮](button.md)指南。

## <a name="imagebutton-appearance"></a>ImageButton 外观

除了属性外，`ImageButton`继承自[ `View` ](xref:Xamarin.Forms.View)类，`ImageButton`还定义了多个会影响其外观的属性：

- `Aspect` 是将如何缩放图像以适合显示区域。
- `BorderColor` 是的周围区域的颜色`ImageButton`。
- `BorderWidth` 为边框的宽度。
- `CornerRadius` 是的圆角半径`ImageButton`。

`Aspect`属性可以设置为一个的成员[ `Aspect` ](xref:Xamarin.Forms.Aspect)枚举：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -拉伸图像以完全且完全填充`ImageButton`。 这可能会导致被扭曲图像。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -裁剪图像，以便它填充`ImageButton`同时保存纵横比。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -上下黑边映像 （如有必要），以便适应整个图像`ImageButton`，与添加到顶部/底部或根据该映像是高或宽的两侧的空格。 这是默认值[ `Aspect` ](xref:Xamarin.Forms.Aspect)枚举。

> [!NOTE]
> `ImageButton`类还具有[ `Margin` ](xref:Xamarin.Forms.View.Margin)并`Padding`控件的布局行为的属性`ImageButton`。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="imagebutton-visual-states"></a>ImageButton 可视状态

`ImageButton` 具有`Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState)可用于启动到可视更改`ImageButton`时用户按下，前提启用了。

下面的 XAML 示例演示如何定义的可视状态`Pressed`状态：

```xaml
<ImageButton Source="XamarinLogo.png"
             ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

`Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState)指定当`ImageButton`按下时，其[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)属性将更改从其默认值为 1 到 0.8。 `Normal` `VisualState`指定当`ImageButton`处于正常状态，其`Scale`属性将设置为 1。 因此，总体效果是，当`ImageButton`是按下时，它重新缩放为更略小，并且当`ImageButton`是发布，它将其重新缩放为其默认大小。

可视状态的详细信息，请参阅[Xamarin.Forms 视觉状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相关链接

- [FormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
