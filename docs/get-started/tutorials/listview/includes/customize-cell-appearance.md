---
ms.openlocfilehash: 3c88b71cea834f5e6ef20d43332904c052c6e3a6
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "61037431"
---
以前，使用数据绑定为 [`ListView`](xref:Xamarin.Forms.ListView) 填充数据。 但是，尽管数据绑定到每个对象都定义了多个数据项的集合，但每个对象只显示一个数据项（`Monkey` 对象的 `Name` 属性）。

在本练习中，你将修改 ListViewTutorial 项目，以便 [`ListView`](xref:Xamarin.Forms.ListView) 在每行中显示多个数据项。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`ListView`](xref:Xamarin.Forms.Image) 声明以自定义每行的外观：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    此代码将 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，该属性定义 [`ListView`](xref:Xamarin.Forms.ListView) 中每行的外观。 `DataTemplate` 的子级必须属于或派生自 [`Cell`](xref:Xamarin.Forms.Cell) 类型。 此代码使用 [`ViewCell`](xref:Xamarin.Forms.ViewCell)，它派生自 `Cell`，为每个 `ListView` 行创建自定义布局。 `ViewCell` 中的布局由 [`Grid`](xref:Xamarin.Forms.Grid) 管理，其中包含一个 [`Image`](xref:Xamarin.Forms.Image) 对象和两个 [`Label`](xref:Xamarin.Forms.Label) 对象。 `Image` 对象数据将其 [`Source`](xref:Xamarin.Forms.Image.Source) 属性绑定到每个 `Monkey` 对象的 `ImageUrl` 属性，而 `Label` 对象将其 [`Text`](xref:Xamarin.Forms.Label.Text) 属性绑定到每个 `Monkey` 对象的 `Name` 和 `Location` 属性。

    默认情况下，[`ListView`](xref:Xamarin.Forms.ListView) 中的各行高度相同。 但是，此代码将 [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) 属性设置为 `true`，以便可以根据内容调整行高。 这适用于具有可变长度字符串的每个 `Monkey` 对象的 `Name` 和 `Location` 属性。

    有关 [`ListView`](xref:Xamarin.Forms.ListView) 单元格外观的详细信息，请参阅[自定义 ListView 单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。 有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序：

    [![项已使用数据模板进行模板化的 ListView 屏幕截图](../images/customize-cell-appearance.png "显示模板化数据的 ListView")](../images/customize-cell-appearance-large.png#lightbox "显示模板化数据的 ListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`ListView`](xref:Xamarin.Forms.Image) 声明以自定义每行的外观：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    此代码将 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 属性设置为 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，该属性定义 [`ListView`](xref:Xamarin.Forms.ListView) 中每行的外观。 `DataTemplate` 的子级必须属于或派生自 [`Cell`](xref:Xamarin.Forms.Cell) 类型。 此代码使用 [`ViewCell`](xref:Xamarin.Forms.ViewCell)，它派生自 `Cell`，为每个 `ListView` 行创建自定义布局。 `ViewCell` 中的布局由 [`Grid`](xref:Xamarin.Forms.Grid) 管理，其中包含一个 [`Image`](xref:Xamarin.Forms.Image) 对象和两个 [`Label`](xref:Xamarin.Forms.Label) 对象。 `Image` 对象数据将其 [`Source`](xref:Xamarin.Forms.Image.Source) 属性绑定到每个 `Monkey` 对象的 `ImageUrl` 属性，而 `Label` 对象将其 [`Text`](xref:Xamarin.Forms.Label.Text) 属性绑定到每个 `Monkey` 对象的 `Name` 和 `Location` 属性。

    默认情况下，[`ListView`](xref:Xamarin.Forms.ListView) 中的各行高度相同。 但是，此代码将 [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) 属性设置为 `true`，以便可以根据内容调整行高。 这适用于具有可变长度字符串的每个 `Monkey` 对象的 `Name` 和 `Location` 属性。

    有关 [`ListView`](xref:Xamarin.Forms.ListView) 单元格外观的详细信息，请参阅[自定义 ListView 单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。 有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![项已使用数据模板进行模板化的 ListView 屏幕截图](../images/customize-cell-appearance.png "显示模板化数据的 ListView")](../images/customize-cell-appearance-large.png#lightbox "显示模板化数据的 ListView")
