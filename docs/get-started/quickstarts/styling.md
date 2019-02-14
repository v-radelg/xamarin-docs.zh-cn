---
title: 样式的跨平台 Xamarin.Forms 应用程序
description: 本文介绍如何使用 XAML 样式的3样式的跨平台 Xamarin.Forms 应用程序。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 56f8632c9cc7d170b4c6594fd51e6aa3e92ad02b
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292809"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>样式的跨平台 Xamarin.Forms 应用程序

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)

在此快速入门中，你将了解如何：

- 设置 Xamarin.Forms 应用程序使用 XAML 样式的样式。

快速入门教程演示如何使用 XAML 样式的样式的跨平台 Xamarin.Forms 应用程序。 最终的应用程序如下所示：

[![](styling-images/screenshots1-sml.png "说明页")](styling-images/screenshots1.png#lightbox "说明页")
[![](styling-images/screenshots2-sml.png "请注意入口页")](styling-images/screenshots2.png#lightbox "注意入口页")

### <a name="prerequisites"></a>系统必备

应已成功完成[以前的快速入门](database.md)然后再尝试此快速入门。 或者，下载[前面的快速入门示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)并将其用作起始点为本快速入门。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新应用

1. 启动 Visual Studio 并打开说明解决方案。

2. 在中**解决方案资源管理器**，在**说明**项目中，双击**App.xaml**以将其打开。 然后使用以下代码替换现有代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此代码定义[ `Thickness` ](xref:Xamarin.Forms.Thickness)值，一系列[ `Color` ](xref:Xamarin.Forms.Color)值和隐式样式[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)和[ `ContentPage`](xref:Xamarin.Forms.ContentPage). 注意，这些样式，后者位于应用程序级[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，可以使用整个应用程序。 有关 XAML 样式设置的详细信息，请参阅[样式](deepdive.md#styling)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**App.xaml**通过按**CTRL + S**，然后关闭文件。

3. 在中**解决方案资源管理器**，在**说明**项目中，双击**NotesPage.xaml**以将其打开。 然后使用以下代码替换现有代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    此代码将添加的隐式样式[ `ListView` ](xref:Xamarin.Forms.ListView)到页级[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，并设置`ListView.Margin`属性中应用程序级别定义的值`ResourceDictionary`. 请注意，`ListView`隐式样式添加到页面级`ResourceDictionary`，因为它仅供`NotesPage`。 有关 XAML 样式设置的详细信息，请参阅[样式](deepdive.md#styling)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**NotesPage.xaml**通过按**CTRL + S**，然后关闭文件。

5. 在中**Solution Pad**，在**说明**项目中，双击**NoteEntryPage.xaml**以将其打开。 然后使用以下代码替换现有代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    此代码将添加隐式样式[ `Editor` ](xref:Xamarin.Forms.Editor)并[ `Button` ](xref:Xamarin.Forms.Button)页级视图[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，并设置`StackLayout.Margin`属性在应用程序级别中定义的值`ResourceDictionary`。 请注意，`Editor`并`Button`隐式样式添加到页面级`ResourceDictionary`，因为它们仅供`NoteEntryPage`。 有关 XAML 样式设置的详细信息，请参阅[样式](deepdive.md#styling)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**NoteEntryPage.xaml**通过按**CTRL + S**，然后关闭文件。

6. 生成并运行每个平台上的项目。 有关详细信息，请参阅[构建快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 在每个页上，观察如何样式已从上一个快速入门。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新应用

1. 启动 Visual Studio for Mac，并打开说明项目。

2. 在中**Solution Pad**，在**说明**项目中，双击**App.xaml**以将其打开。 然后使用以下代码替换现有代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此代码定义[ `Thickness` ](xref:Xamarin.Forms.Thickness)值，一系列[ `Color` ](xref:Xamarin.Forms.Color)值和隐式样式[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)和[ `ContentPage`](xref:Xamarin.Forms.ContentPage). 注意，这些样式，后者位于应用程序级[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，可以使用整个应用程序。 有关 XAML 样式设置的详细信息，请参阅[样式](deepdive.md#styling)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**App.xaml**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

3. 在中**解决方案资源管理器**，在**说明**项目中，双击**NotesPage.xaml**以将其打开。 然后使用以下代码替换现有代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    此代码将添加的隐式样式[ `ListView` ](xref:Xamarin.Forms.ListView)到页级[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，并设置`ListView.Margin`属性中应用程序级别定义的值`ResourceDictionary`. 请注意，`ListView`隐式样式添加到页面级`ResourceDictionary`，因为它仅供`NotesPage`。 有关 XAML 样式设置的详细信息，请参阅[样式](deepdive.md#styling)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**NotesPage.xaml**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

5. 在中**解决方案资源管理器**，在**说明**项目中，双击**NoteEntryPage.xaml**以将其打开。 然后使用以下代码替换现有代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    此代码将添加隐式样式[ `Editor` ](xref:Xamarin.Forms.Editor)并[ `Button` ](xref:Xamarin.Forms.Button)页级视图[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，并设置`StackLayout.Margin`属性在应用程序级别中定义的值`ResourceDictionary`。 请注意，`Editor`并`Button`隐式样式添加到页面级`ResourceDictionary`，因为它们仅供`NoteEntryPage`。 有关 XAML 样式设置的详细信息，请参阅[样式](deepdive.md#styling)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**NoteEntryPage.xaml**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

6. 生成并运行每个平台上的项目。 有关详细信息，请参阅[构建快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 在每个页上，观察如何样式已从上一个快速入门。

::: zone-end


## <a name="next-steps"></a>后续步骤

在此快速入门中，你学习了如何：

- 设置 Xamarin.Forms 应用程序使用 XAML 样式的样式。

若要详细了解使用 Xamarin.Forms 开发应用程序的基础知识，继续快速入门的深入探讨。

> [!div class="nextstepaction"]
> [下一页](deepdive.md)

## <a name="related-links"></a>相关链接

- [便笺（示例）](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)
- [Xamarin.Forms 快速入门的深入探讨](deepdive.md)
