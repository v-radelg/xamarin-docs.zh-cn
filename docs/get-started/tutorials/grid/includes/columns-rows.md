---
ms.openlocfilehash: d87289e481b69592b68627d053e937856d3d6067
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "61375343"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“MainPage.xaml”中，修改 [`Grid`](xref:Xamarin.Forms.Grid) 声明以定义列和行，并将内容放在列和行中：

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    此代码定义 [`Grid`](xref:Xamarin.Forms.Grid) 的列和行，并将 [`Label`](xref:Xamarin.Forms.Label) 实例放置在特定的列和行中。 列和行数据存储在 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 和 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 属性中，这两个属性分别是 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 和 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 对象的集合。 每个 `ColumnDefinition` 的宽度由 [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 属性设置，每个 `RowDefinition` 的高度由 [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) 属性设置。 有效的宽度值和高度值为：

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) 对列或行进行大小调整以适应内容。
    - 比例值，将列和行的大小设置为剩余空间的比例。 以 `*` 结尾表示比例值。
    - 绝对值，使用特定的固定值调整列或行的大小。

    因此，在上面的代码中，每列的宽度为 [`Grid`](xref:Xamarin.Forms.Grid) 的一半，而每行的高度为 50 个与设备无关的单位。

    使用从零开始的索引，借助 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 附加属性指定 [`Grid`](xref:Xamarin.Forms.Grid) 中每个 [`Label`](xref:Xamarin.Forms.Label) 的位置。 因此，第一列是第 0 列，第一行是第 0 行。 第一个 `Label` 缺少这些附加属性，因为任何未设置这些属性的子视图将自动在第 0 列第 0 行中呈现。

    > [!NOTE]
    > 可以使用 [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 和 [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) 属性设置 [`Grid`](xref:Xamarin.Forms.Grid) 中列和行之间的间距。 有关详细信息，请参阅 [Xamarin.Forms 网格](~/xamarin-forms/user-interface/layouts/grid.md)指南中的[间距](~/xamarin-forms/user-interface/layouts/grid.md#spacing)。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序：

    [![iOS 和 Android 上内容位于多列和多行的网格屏幕截图](../images/columns-rows.png "内容位于多列和多行的网格")](../images/columns-rows-large.png#lightbox "内容位于多列和多行的网格")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在“MainPage.xaml”中，修改 [`Grid`](xref:Xamarin.Forms.Grid) 声明以定义列和行，并将内容放在列和行中：

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    此代码定义 [`Grid`](xref:Xamarin.Forms.Grid) 的列和行，并将 [`Label`](xref:Xamarin.Forms.Label) 实例放置在特定的列和行中。 列和行数据存储在 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 和 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 属性中，这两个属性分别是 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 和 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 对象的集合。 每个 `ColumnDefinition` 的宽度由 [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 属性设置，每个 `RowDefinition` 的高度由 [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) 属性设置。 有效的宽度值和高度值为：

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) 对列或行进行大小调整以适应内容。
    - 比例值，将列和行的大小设置为剩余空间的比例。 以 `*` 结尾表示比例值。
    - 绝对值，使用特定的固定值调整列或行的大小。

    因此，在上面的代码中，每列的宽度为 [`Grid`](xref:Xamarin.Forms.Grid) 的一半，而每行的高度为 50 个与设备无关的单位。

    使用从零开始的索引，借助 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 附加属性指定 [`Grid`](xref:Xamarin.Forms.Grid) 中每个 [`Label`](xref:Xamarin.Forms.Label) 的位置。 因此，第一列是第 0 列，第一行是第 0 行。 第一个 `Label` 缺少这些附加属性，因为任何未设置这些属性的子视图将自动在第 0 列第 0 行中呈现。

    > [!NOTE]
    > 可以使用 [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 和 [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) 属性设置 [`Grid`](xref:Xamarin.Forms.Grid) 中列和行之间的间距。 有关详细信息，请参阅 [Xamarin.Forms 网格](~/xamarin-forms/user-interface/layouts/grid.md)指南中的[间距](~/xamarin-forms/user-interface/layouts/grid.md#spacing)。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![iOS 和 Android 上内容位于多列和多行的网格屏幕截图](../images/columns-rows.png "内容位于多列和多行的网格")](../images/columns-rows-large.png#lightbox "内容位于多列和多行的网格")
