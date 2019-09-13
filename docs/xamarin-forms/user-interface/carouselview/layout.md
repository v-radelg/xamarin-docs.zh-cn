---
title: Xamarin. Forms CarouselView
description: 默认情况下，CarouselView 会在水平列表中显示其项。 但是，它还可以访问 CollectionView 的相同布局，包括垂直方向。
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 5bbaeead524089ea604cfd9a9fd7f3a85d04e724
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908342"
---
# <a name="xamarinforms-carouselview-layouts"></a>Xamarin CarouselView 布局

![](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)

## <a name="introduction"></a>介绍

CarouselView 提供的很多布局功能都源自 CollectionView。 可以参考 CollectionView 的[布局文档](../collectionview/layout.md)来了解各种布局的使用情况。

## <a name="differences-from-collectionview"></a>与 CollectionView 的差异

默认情况下，CarouselView 中的项将会水平放置，就像应用程序中的轮播的典型功能一样。

CarouselView 还提供了一些其他属性：

> [!IMPORTANT]
> CarouselView 的其他属性仍处于开发阶段，此列表尚未完成。

| API | 函数 |
|---|---|---|
| NumberOfSideItems | 设置在当前项的每一侧显示的项数。 默认值为0。
| PeekAreaInsets | 提供了一种直观地向用户指示 CarouselView 是否有要滚动的其他项，方法是调整当前项旁边的可见性级别。

## <a name="setting-the-number-of-fully-visible-items"></a>设置完全可见项的数目

默认情况下，CarouselView 在屏幕上全部显示一项。 用户可以将`NumberOfSideItems`属性设置为允许更多项显示在当前项旁。 请注意，任何设置为`PeekAreaInsets`的值仍将应用。

```xaml
<StackLayout Margin="20">
    <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" NumberOfSideItems="1">
        <CarouselView.ItemTemplate>
            <DataTemplate>
                <Frame BorderColor="Black">
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Label Grid.Column="1"
                            Text="{Binding Name}"
                            FontAttributes="Bold"
                            FontSize="14"/>
                        <Label Grid.Row="1"
                            Grid.Column="1"
                            Text="{Binding Location}"
                            FontAttributes="Italic"
                            FontSize="12"
                            VerticalOptions="End" />
                    </Grid>
                </Frame>
            </DataTemplate>
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

[ ![Android](carouselview-images/side-items.png "CarouselView 端项目")的 CarouselView 的屏幕截图](carouselview-images/side-items-large.png#lightbox "CarouselView 端项")

## <a name="making-adjacent-items-partially-visible"></a>使相邻项部分可见

在应用程序中使用 CarouselView 时，通过将`PeekAreaInsets`属性设置为非零值（默认值）来向用户指示 CarouselView 的功能非常有用，在屏幕上部分显示这些值。

```xaml
<StackLayout Margin="20">
  <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" PeekAreaInsets="100">
      <CarouselView.ItemTemplate>
          <DataTemplate>
              <Frame BorderColor="Black">
                  <Grid>
                      <Grid.RowDefinitions>
                          <RowDefinition Height="Auto" />
                          <RowDefinition Height="Auto" />
                      </Grid.RowDefinitions>
                      <Grid.ColumnDefinitions>
                          <ColumnDefinition Width="Auto" />
                          <ColumnDefinition Width="Auto" />
                      </Grid.ColumnDefinitions>
                      <Label Grid.Column="1"
                          Text="{Binding Name}"
                          FontAttributes="Bold"
                          FontSize="14"/>
                      <Label Grid.Row="1"
                          Grid.Column="1"
                          Text="{Binding Location}"
                          FontAttributes="Italic"
                          FontSize="12"
                          VerticalOptions="End" />
                  </Grid>
              </Frame>
          </DataTemplate>
      </CarouselView.ItemTemplate>
  </CarouselView>
</StackLayout>
```

[ ![Android](carouselview-images/peek-area-insets.png "CarouselView 端项目")的 CarouselView 的屏幕截图](carouselview-images/peek-area-insets-large.png#lightbox "CarouselView 端项")

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)
- [CollectionView 布局文档](../collectionview/layout.md)
