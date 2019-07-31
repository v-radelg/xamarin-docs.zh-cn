---
title: 设计跨平台的 Xamarin.Forms 应用程序
description: 本文介绍如何使用 XAML 样式对跨平台 Xamarin 应用程序应用程序进行样式。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: e26a71ad72b557a27841bfee1d26001126e2a2a2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654670"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>样式跨平台 Xamarin. Forms 应用程序

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

在本快速入门中, 你将学习如何:

- 使用 XAML 样式为 Xamarin 应用程序应用样式。

快速入门演示如何使用 XAML 样式为跨平台 Xamarin 应用程序应用程序进行样式。 最终的应用程序如下所示：

[ ![(styling-images/screenshots1-sml.png "")]备注页](styling-images/screenshots1.png#lightbox "备注页")便笺条目页面(styling-images/screenshots2.png#lightbox "备注输入页") [ ![(styling-images/screenshots2-sml.png " ")]] 


### <a name="prerequisites"></a>系统必备

在尝试此快速入门之前, 应成功完成[以前的快速入门](database.md)。 或者, 下载[前面的快速入门示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/), 并将其用作本快速入门的起点。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新应用

1. 启动 Visual Studio 并打开 Notes 解决方案。

2. 在**解决方案资源管理器**的 "**注释**" 项目中, 双击 " **app.config** " 以将其打开。 然后, 将现有代码替换为以下代码:

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

    此代码定义[`Thickness`](xref:Xamarin.Forms.Thickness) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)和[`Color`](xref:Xamarin.Forms.Color) [的`ContentPage`](xref:Xamarin.Forms.ContentPage)值、一系列值和隐式样式。 请注意, 在应用程序中可以使用这些样式 ( [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)这些样式位于应用程序级别)。 有关 XAML 样式的详细信息, 请参阅 Xamarin 中的[样式设置](deepdive.md#styling)[深入探讨](deepdive.md)。

    通过按**CTRL + S**将更改保存到**app.xaml** , 并关闭文件。

3. 在**解决方案资源管理器**的 "**注释**" 项目中, 双击 " **NotesPage** " 将其打开。 然后, 将现有代码替换为以下代码:

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

    此代码将[`ListView`](xref:Xamarin.Forms.ListView)的隐式样式添加到页面级别[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), 并将`ListView.Margin`属性设置为在应用程序级别`ResourceDictionary`中定义的值。 请注意, `ResourceDictionary` `NotesPage`隐式样式已添加到页面级别, 因为它仅`ListView`由使用。 有关 XAML 样式的详细信息, 请参阅 Xamarin 中的[样式设置](deepdive.md#styling)[深入探讨](deepdive.md)。

    通过按**CTRL + S**保存对**NotesPage**所做的更改, 并关闭该文件。

4. 在**解决方案资源管理器**的 "**注释**" 项目中, 双击 " **NoteEntryPage** " 将其打开。 然后, 将现有代码替换为以下代码:

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

    此代码会[`Editor`](xref:Xamarin.Forms.Editor)将和[`Button`](xref:Xamarin.Forms.Button)视图的隐式样式添加到页面`StackLayout.Margin`级别[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), 并将属性设置为在应用程序级别`ResourceDictionary`中定义的值。 请注意, `Button` `ResourceDictionary` `NoteEntryPage`和隐式样式已添加到页级, 因为它们仅由使用。 `Editor` 有关 XAML 样式的详细信息, 请参阅 Xamarin 中的[样式设置](deepdive.md#styling)[深入探讨](deepdive.md)。

    通过按**CTRL + S**保存对**NoteEntryPage**所做的更改, 并关闭该文件。

5. 在每个平台上生成并运行项目。 有关详细信息, 请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 在每个页面上, 观察样式在以前的快速入门中的变化方式。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新应用

1. 启动 Visual Studio for Mac 并打开 "注释" 项目。

2. 在**Solution Pad**的 "**注释**" 项目中, 双击 " **app.config** " 以打开它。 然后, 将现有代码替换为以下代码:

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

    此代码定义[`Thickness`](xref:Xamarin.Forms.Thickness) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)和[`Color`](xref:Xamarin.Forms.Color) [的`ContentPage`](xref:Xamarin.Forms.ContentPage)值、一系列值和隐式样式。 请注意, 在应用程序中可以使用这些样式 ( [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)这些样式位于应用程序级别)。 有关 XAML 样式的详细信息, 请参阅 Xamarin 中的[样式设置](deepdive.md#styling)[深入探讨](deepdive.md)。

    通过选择 " **> 文件**" "保存" (或按 **&#8984; + S**) 来保存对**app.xaml**所做的更改, 然后关闭该文件。

3. 在**Solution Pad**的 "**注释**" 项目中, 双击 " **NotesPage** " 将其打开。 然后, 将现有代码替换为以下代码:

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

    此代码将[`ListView`](xref:Xamarin.Forms.ListView)的隐式样式添加到页面级别[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), 并将`ListView.Margin`属性设置为在应用程序级别`ResourceDictionary`中定义的值。 请注意, `ResourceDictionary` `NotesPage`隐式样式已添加到页面级别, 因为它仅`ListView`由使用。 有关 XAML 样式的详细信息, 请参阅 Xamarin 中的[样式设置](deepdive.md#styling)[深入探讨](deepdive.md)。

    通过选择 "**文件" > "保存**" (或按 **&#8984; + S**) 来保存对**NotesPage**的更改, 并关闭该文件。

4. 在**Solution Pad**的 "**注释**" 项目中, 双击 " **NoteEntryPage** " 将其打开。 然后, 将现有代码替换为以下代码:

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

    此代码会[`Editor`](xref:Xamarin.Forms.Editor)将和[`Button`](xref:Xamarin.Forms.Button)视图的隐式样式添加到页面`StackLayout.Margin`级别[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), 并将属性设置为在应用程序级别`ResourceDictionary`中定义的值。 请注意, `Button` `ResourceDictionary` `NoteEntryPage`和隐式样式已添加到页级, 因为它们仅由使用。 `Editor` 有关 XAML 样式的详细信息, 请参阅 Xamarin 中的[样式设置](deepdive.md#styling)[深入探讨](deepdive.md)。

    通过选择 "**文件" > "保存**" (或按 **&#8984; + S**) 来保存对**NoteEntryPage**的更改, 并关闭该文件。

5. 在每个平台上生成并运行项目。 有关详细信息, 请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 在每个页面上, 观察样式在以前的快速入门中的变化方式。

::: zone-end


## <a name="next-steps"></a>后续步骤

在本快速入门中, 你学习了如何:

- 使用 XAML 样式为 Xamarin 应用程序应用样式。

若要了解有关使用 Xamarin 进行应用程序开发的基础知识的详细信息, 请继续深入了解快速入门教程。

> [!div class="nextstepaction"]
> [下一页](deepdive.md)

## <a name="related-links"></a>相关链接

- [便笺（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Xamarin. 窗体快速入门深入探讨](deepdive.md)
