---
title: XAML 标准（预览）
description: 本文介绍如何开始探索在 Xamarin.Forms 中 XAML 标准预览版使用。
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/15/2017
ms.openlocfilehash: d9fb19fb233c25e5dd525c7c157013ef66f4a4f3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169660"
---
# <a name="xaml-standard-preview"></a>XAML 标准（预览）

![预览](~/media/shared/preview.png)

请执行以下步骤，尝试使用 Xamarin.Forms 中 XAML 标准：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 下载[预览此处 NuGet 包](https://aka.ms/xf-xamlstandard-nuget)。
2. 添加**Xamarin.Forms.Alias**到 Xamarin.Forms.NET Standard 和平台项目的 NuGet 包。
3. 初始化具有包 `Alias.Init()`
4. 添加`xmlns:a`引用 `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. 在 XAML 中使用的类型-请参阅[控件参考](controls.md)有关详细信息。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 下载[预览此处 NuGet 包](https://aka.ms/xf-xamlstandard-nuget)。
2. 添加**Xamarin.Forms.Alias**到 Xamarin.Forms.NET Standard 和平台项目的 NuGet 包。
3. 初始化具有包 `Alias.Init()`
4. 添加`xmlns:a`引用 `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. 在 XAML 中使用的类型-请参阅[控件参考](controls.md)有关详细信息。

-----

以下 XAML 演示了在 Xamarin.Forms 中使用某些 XAML 标准控件`ContentPage`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ContentPage 
    xmlns="http://xamarin.com/schemas/2014/forms" 
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
    xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"
    x:Class="XAMLStandardSample.ItemsPage" 
    Title="{Binding Title}" x:Name="BrowseItemsPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddItem_Clicked" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <a:StackPanel>
            <ListView x:Name="ItemsListView" ItemsSource="{Binding Items}" VerticalOptions="FillAndExpand" HasUnevenRows="true" RefreshCommand="{Binding LoadItemsCommand}" IsPullToRefreshEnabled="true" IsRefreshing="{Binding IsBusy, Mode=OneWay}" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Padding="10">
                                <a:TextBlock Text="{Binding Text}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemTextStyle}" FontSize="16" />
                                <a:TextBlock Text="{Binding Description}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemDetailTextStyle}" FontSize="13" />
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </a:StackPanel>
    </ContentPage.Content>
</ContentPage>
```

> [!NOTE]
> 需要 xmlns `a:` XAML 标准控件上的前缀是当前的预览版的限制。


## <a name="related-links"></a>相关链接

- [预览 NuGet](https://aka.ms/xf-xamlstandard-nuget)
- [控件引用](controls.md)
