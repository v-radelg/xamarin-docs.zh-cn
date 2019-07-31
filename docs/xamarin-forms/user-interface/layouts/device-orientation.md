---
title: 设备方向
description: 此文章介绍了如何查找在纵向和横向方向上出色的布局 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: dccae3b3da4ab84c8ba14f204263e4230f31e833
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656787"
---
# <a name="device-orientation"></a>设备方向

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

请务必要考虑将如何使用你的应用程序和如何合并横向方向以改善用户体验。 可以设计单独的布局以容纳多个方向和最佳使用的可用空间。 在应用程序级别，可以禁用或启用旋转。

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>控制方向

使用 Xamarin.Forms 时, 控制设备方向的受支持的方法是为每个单独的项目使用的设置。

### <a name="ios"></a>iOS

在 iOS 上，设备方向配置使用的应用程序**Info.plist**文件。 如果该应用包含它作为目标，此文件将包含的 iPhone 和 iPod，方向设置，以及适用于 iPad 的设置。 以下是对 IDE 的特定说明。 在本文档的顶部使用 IDE 选项选择你想要看到的说明进行操作：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，打开 iOS 项目，并打开**Info.plist**。 该文件将打开到配置面板中，从 iPhone 部署信息选项卡开始：

![iPhone Visual Studio 中的部署信息](device-orientation-images/orientation-vs-iphone.png)

若要配置 iPad 方向，请选择**iPad 部署信息**左上方的面板中，然后选择从可用方向的选项卡：

![在 Visual Studio 中支持的设备方向](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，打开 iOS 项目，并打开**Info.plist**。 下**应用程序**选项卡上，各节将才可设置方向：

![iPhone 部署信息在 Visual Studio for Mac](device-orientation-images/orientation-xam-ui.png)

如果你想要编辑使用键 / 值编辑器界面，选择的值**源**> 在屏幕底部的选项卡：

![在 Visual Studio for Mac 支持设备方向](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

若要控制在 Android 上的方向，打开**MainActivity.cs**并设置使用属性修饰的方向`MainActivity`类：

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android 支持多个选项用于指定方向：

- **横向**&ndash;强制应用程序方向为横向，而不考虑传感器数据。
- **纵向**&ndash;强制应用程序方向为纵向，而不考虑传感器数据。
- **用户**&ndash;导致应用程序使用用户的首选的方向显示。
- **后面**&ndash;会导致应用程序的方向是相同的方向[活动](xref:Android.App.Activity)它后面。
- **传感器**&ndash;导致应用程序的方向传感器，确定，即使用户已禁用自动旋转。
- **SensorLandscape** &ndash;会导致应用程序以使用横向方向，同时使用传感器数据来更改 （以便在屏幕不为正面向下所示） 面向在屏幕的方向。
- **SensorPortrait** &ndash;使应用程序使用纵向方向时使用的传感器数据更改 （以便在屏幕不为正面向下所示） 面向屏幕方向。
- **ReverseLandscape** &ndash;会导致应用程序以使用横向方向，面向的相反方向平常，以便显示"倒置。"
- **ReversePortrait** &ndash;使应用程序使用纵向方向，面向的相反方向平常，以便显示"倒置。"
- **FullSensor** &ndash;导致应用程序依赖于传感器数据以选择正确的方向 （从可能 4)。
- **FullUser** &ndash;使应用程序使用用户的方向首选项。 如果启用自动旋转，则可以使用所有 4 个方向。
- **UserLandscape** &ndash; _\[不支持\]_ 会导致应用程序以使用横向方向，除非用户具有自动旋转启用，在这种情况下它将使用若要确定方向的传感器。 此选项将中断编译。
- **UserPortrait** &ndash; _\[不支持\]_ 使应用程序使用纵向方向，除非用户具有自动旋转启用，在这种情况下它将使用若要确定方向的传感器。 此选项将中断编译。
- **锁定** &ndash; _\[不支持\]_ 使应用程序使用的屏幕方向，无论它是启动时，而无需对设备中的更改作出响应的物理方向。 此选项将中断编译。

请注意，本机 Android Api 提供了很多方向的管理方式的控制，包括显式与用户相矛盾的选项以表示首选项。

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP)，支持的方向中设置**Package.appxmanifest**文件。 打开清单，将显示在其中选择支持的方向配置面板。

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>对方向中的更改作出反应

Xamarin.Forms 不提供任何本机事件用于通知的共享代码中的方向更改您的应用程序。 但是，`SizeChanged`的事件`Page`，则会激发的宽度或高度`Page`更改。 时的宽度`Page`大于高度，在设备处于横向模式。 有关详细信息，请参阅[显示基于屏幕方向的映像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)。

> [!NOTE]
> 没有用于接收通知的方向更改共享代码中的现有的免费 NuGet 包。 请参阅[GitHub 存储库](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)有关详细信息。

或者，则可以重写[ `OnSizeAllocated` ](xref:Xamarin.Forms.Page.OnSizeAllocated*)方法`Page`，插入任何布局更改那里逻辑。 `OnSizeAllocated`调用方法时`Page`分配新的大小，这种情况发生时将设备旋转。 请注意的基实现`OnSizeAllocated`执行重要布局功能，因此，必须在重写中调用基实现：

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

未能执行该步骤将导致非正常运行的页面。

请注意，`OnSizeAllocated`方法可能会多次调用旋转设备时。 每次更改你的布局是一种浪费的资源，并可能会导致闪烁。 请考虑使用在页面内的一个实例变量跟踪是否方向为横向或纵向，并只重绘更改时：

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

后检测到在设备方向更改，你可能想要添加或删除与用户界面的可用空间的更改做出反应的其他视图。 例如，考虑纵向时的每个平台上内置的计算器：

![](device-orientation-images/calculator-portrait.png "纵向时的计算器应用程序")

和横向：

![](device-orientation-images/calculator-landscape.png "在环境中的计算器应用程序")

请注意，应用程序通过添加更多的功能在环境中充分利用可用空间。

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>响应式布局

就可以使用内置的布局，以便适当地过渡旋转设备时的设计界面。 设计将继续响应方向中的更改时很吸引人的接口时请考虑以下一般规则：

- **注意比率**&ndash;时方面比进行某些假设，方向中的更改可能会导致问题。 例如，一个视图，它会在 1/3 的纵向时的屏幕的垂直空间中具有足够的空间可能无法放入 1/3 的环境中的垂直空间。
- **请小心使用绝对值**&ndash;意义纵向时的绝对 （像素） 值在环境中可能毫无意义。 如果绝对值是必需的使用嵌套的布局隔离它们的影响。 例如，它将合理地使用中的绝对值`TableView``ItemTemplate`时已有保证的统一高度的项模板。

上述规则同样适用时通常实现接口，用于多个屏幕大小以及被视为最佳做法。 本指南的其余部分将介绍在 Xamarin.Forms 中使用上述每种主要布局的响应式布局的具体示例。

> [!NOTE]
> 为清楚起见，以下各节演示如何实现使用的只是一种类型的响应式布局`Layout`一次。 在实践中，它通常会更简单混合`Layout`以实现所需的布局使用更简单或最直观`Layout`为每个组件。

### <a name="stacklayout"></a>StackLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-stack-portrait.png "纵向时的照片应用程序")

和横向：

![](device-orientation-images/photo-stack-landscape.png "在环境中的照片应用程序")

这是使用以下 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

某些 C# 用于更改的方向`outerStack`基于设备的方向：

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

请注意以下事项：

- `outerStack` 将调整为作为水平或垂直方向，以最有效地利用可用空间根据堆栈中显示的图像和控件。


### <a name="absolutelayout"></a>AbsoluteLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-abs-portrait.png "纵向时的照片应用程序")

和横向：

![](device-orientation-images/photo-abs-landscape.png "在环境中的照片应用程序")

这是使用以下 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

请注意以下事项：

- 由于页面布局的方式，不是需要程序代码引入响应能力。
- `ScrollView`用于允许甚至时屏幕的高度是固定的按钮和图像的高度之和小于为可见的标签。


### <a name="relativelayout"></a>RelativeLayout

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-rel-portrait.png "纵向时的照片应用程序")

和横向：

![](device-orientation-images/photo-rel-landscape.png "在环境中的照片应用程序")

这是使用以下 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

请注意以下事项：

- 由于页面布局的方式，不是需要程序代码引入响应能力。
- `ScrollView`用于允许甚至时屏幕的高度是固定的按钮和图像的高度之和小于为可见的标签。

### <a name="grid"></a>Grid

请考虑以下应用程序，在纵向显示：

![](device-orientation-images/photo-grid-portrait.png "纵向时的照片应用程序")

和横向：

![](device-orientation-images/photo-grid-landscape.png "在环境中的照片应用程序")

这是使用以下 XAML 来实现：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

以下过程与代码一起处理旋转更改：

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

请注意以下事项：

- 页面布局的方式，因此没有方法来更改网格放置控件。


## <a name="related-links"></a>相关链接

- [布局 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 示例 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [响应式布局 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [显示基于屏幕方向的映像](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
