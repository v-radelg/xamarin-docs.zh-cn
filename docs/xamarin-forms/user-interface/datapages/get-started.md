---
title: 开始使用 DataPages
description: 本文介绍如何开始构建使用 Xamarin.Forms DataPages 一个简单的数据驱动页面。
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 1f7917784ea66c31979b87f43639a7d03756692c
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725593"
---
# <a name="getting-started-with-datapages"></a>开始使用 DataPages

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> DataPages 要求使用 Xamarin. Forms 主题引用来呈现。 这涉及到将[xamarin. Base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) nuget 包安装到项目中，然后将其后跟[xamarin](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/)或[xamarin. 暗体](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)nuget 包。

若要开始构建使用 DataPages 预览一个简单的数据驱动页面，请执行以下步骤。 在预览中的硬编码样式 （"事件"） 生成此演示使用仅适用于在代码中特定的 JSON 格式。

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

## <a name="1-add-nuget-packages"></a>1. 添加 NuGet 包

将这些 NuGet 包添加到 Xamarin .NET Standard 库和应用程序项目：

- Xamarin.Forms.Pages
- Xamarin.Forms.Theme.Base
- 主题实现 NuGet （例如 Xamarin.Forms.Theme.Light)

## <a name="2-add-theme-reference"></a>2. 添加主题引用

在**app.xaml**文件中，添加主题的自定义 `xmlns:mytheme`，并确保将主题合并到应用程序的资源字典中：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

> [!IMPORTANT]
> 还应按照[以下步骤加载主题程序集（如下所示）](#loadtheme) ：向 iOS `AppDelegate` 和 Android `MainActivity`添加一些样板代码。 这将在将来的预览版的版本中得到改进。

## <a name="3-add-a-xaml-page"></a>3. 添加 XAML 页

向 Xamarin 应用程序添加新的 XAML 页，并将基类从 `ContentPage`*更改*为 `Xamarin.Forms.Pages.ListDataPage`。 这必须在 C# 和 XAML 中进行：

**C#文件**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**XAML 文件**

除了将根元素更改为 `<p:ListDataPage>` 还必须添加 `xmlns:p` 的自定义命名空间：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**应用程序子类**

更改 `App` 类构造函数，以便将 `MainPage` 设置为包含新 `SessionDataPage`的 `NavigationPage`。 *必须*使用导航页。

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. 添加数据源

删除 `Content` 元素，并将其替换为 `p:ListDataPage.DataSource` 以填充包含数据的页面。 在远程 Json 下面的示例从 URL 是正在加载数据文件。

> [!NOTE]
> 预览*要求*`StyleClass` 特性来提供数据源的呈现提示。 `StyleClass="Events"` 是指在预览中预定义的布局，其中包含与所使用的 JSON 数据源相匹配的*硬编码*样式。

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**JSON 数据**

下面显示了演示源中的 JSON 数据的示例：

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4. 运行！

上述步骤应导致工作数据页：

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

这是因为预建的样式 **"事件"** 存在于浅色主题 NuGet 包中，并定义了与数据源匹配的样式（例如 "title"、"图像"、"表示器"）。

生成 "Events" `StyleClass` 是为了显示 `ListDataPage` 控件，其中包含在 Xamarin. Forms 中定义的自定义 `CardView` 控件。 `CardView` 控件具有三个属性： `ImageSource`、`Text`和 `Detail`。 主题是硬编码要绑定的数据源 （从 JSON 文件中） 到显示这些属性的三个字段。

## <a name="5-customize"></a>5. 自定义

可以通过指定的模板并使用数据源绑定中重写继承的样式。 下面的 XAML 使用 new `ListItemControl` 和包含在**Xamarin** NuGet 中的 `{p:DataSourceBinding}` 语法为每一行声明一个自定义模板：

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

提供 `DataTemplate` 此代码将重写 `StyleClass`，而改用 `ListItemControl`的默认布局。

[![](get-started-images/custom-sml.png "DataPages Sample Application")](get-started-images/custom.png#lightbox "DataPages Sample Application")

更喜欢C# XAML 的开发人员也可以创建数据源绑定（请记住要包含 `using Xamarin.Forms.Pages;` 语句）：

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

从头开始创建主题的工作要多一些，但将来的预览版本会使此操作变得更容易。

## <a name="troubleshooting"></a>故障排除

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>无法加载文件或程序集 Xamarin.Forms.Theme.Light 或其某个依赖项

在预览版本中，主题可能不能在运行时加载。 添加代码，如下所示在相关项目来修复此错误。

**iOS**

在**AppDelegate.cs**中的后面添加以下行 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在**MainActivity.cs**中的后面添加以下行 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>相关链接

- [DataPagesDemo 示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
