---
ms.openlocfilehash: d6dbc82e56959399c2befb6a12f0a2cf3793ee5b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382486"
---
[`StackLayout`](xref:Xamarin.Forms.StackLayout) 中子视图的大小和位置取决于子视图 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 属性的值，以及 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性的值。

可以将 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性设置为 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 结构中的字段，该结构封装了两个布局首选项：

- **对齐方式** - 子视图的首选对齐方式，用于确定其父级布局中的位置和大小。
- **扩展** - 表示子视图是否应使用额外空间（如果可用）（仅由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 使用）。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 声明，为每个 [`Label`](xref:Xamarin.Forms.Label) 设置对齐方式和扩展选项：

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    此代码设置前四个 [`Label`](xref:Xamarin.Forms.Label) 实例的对齐方式首选项，以及最后四个 `Label` 实例的扩展首选项。 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)[`Center`](xref:Xamarin.Forms.LayoutOptions.Center)、[`End`](xref:Xamarin.Forms.LayoutOptions.End) 和 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 字段用于定义父级 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中 [`Label`](xref:Xamarin.Forms.Label) 实例的对齐方式。 [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)、[`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)、[`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) 和 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 字段用于定义对齐方式首选项，以及如果该视图在父级 `StackLayout` 内可用，是否将占用更多空间。

    > [!NOTE]
    > 视图的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性的默认值为 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序：

    [![iOS 和 Android 上具有对齐方式和扩展选项集的 StackLayout 中子视图的屏幕截图](../images/alignment-expansion.png "包含 Label 实例和对齐方式及扩展集的 StackLayout")](../images/alignment-expansion-large.png#lightbox "StackLayout containing Label instances, with alignment and expansion set")

    [`StackLayout`](xref:Xamarin.Forms.StackLayout) 仅遵循与 `StackLayout` 方向相反的子视图的对齐方式首选项。 因此，垂直方向的 `StackLayout` 中的 [`Label`](xref:Xamarin.Forms.Label) 子视图将其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性设置为对齐方式字段中的其中一种：

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)，它将 [`Label`](xref:Xamarin.Forms.Label) 置于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的左侧。
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)，它将 [`Label`](xref:Xamarin.Forms.Label) 置于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中心。
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End)，它将 [`Label`](xref:Xamarin.Forms.Label) 置于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的右侧。
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)，确保 [`Label`](xref:Xamarin.Forms.Label) 填充到 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的宽度。

    [`StackLayout`](xref:Xamarin.Forms.StackLayout) 只能按照其方向展开子视图。 因此，垂直方向的 `StackLayout` 可以扩展 [`Label`](xref:Xamarin.Forms.Label) 子视图，该视图将 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性设置为其中一个扩展字段。 这意味着，对于垂直对齐方式，每个 `Label` 在 `StackLayout` 内占据相同的空间量。 但是，只有最后一个 `Label`（可将 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性设置为 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)）具有不同的大小。

    > [!IMPORTANT]
    > 使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的所有空间时，扩展首选项不起作用。

    有关对齐方式和扩展的详细信息，请参阅 [Xamarin.Forms 中的布局选项](~/xamarin-forms/user-interface/layouts/layout-options.md)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 声明，为每个 [`Label`](xref:Xamarin.Forms.Label) 设置对齐方式和扩展选项：

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    此代码设置前四个 [`Label`](xref:Xamarin.Forms.Label) 实例的对齐方式首选项，以及最后四个 `Label` 实例的扩展首选项。 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)[`Center`](xref:Xamarin.Forms.LayoutOptions.Center)、[`End`](xref:Xamarin.Forms.LayoutOptions.End) 和 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 字段用于定义父级 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中 [`Label`](xref:Xamarin.Forms.Label) 实例的对齐方式。 [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)、[`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)、[`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) 和 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 字段用于定义对齐方式首选项，以及如果该视图在父级 `StackLayout` 内可用，是否将占用更多空间。

    > [!NOTE]
    > 视图的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性的默认值为 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![iOS 和 Android 上具有对齐方式和扩展选项集的 StackLayout 中子视图的屏幕截图](../images/alignment-expansion.png "包含 Label 实例和对齐方式及扩展集的 StackLayout")](../images/alignment-expansion-large.png#lightbox "StackLayout containing Label instances, with alignment and expansion set")

    [`StackLayout`](xref:Xamarin.Forms.StackLayout) 仅遵循与 `StackLayout` 方向相反的子视图的对齐方式首选项。 因此，垂直方向的 `StackLayout` 中的 [`Label`](xref:Xamarin.Forms.Label) 子视图将其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性设置为对齐方式字段中的其中一种：

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)，它将 [`Label`](xref:Xamarin.Forms.Label) 置于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的左侧。
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)，它将 [`Label`](xref:Xamarin.Forms.Label) 置于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中心。
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End)，它将 [`Label`](xref:Xamarin.Forms.Label) 置于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的右侧。
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)，确保 [`Label`](xref:Xamarin.Forms.Label) 填充到 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的宽度。

    [`StackLayout`](xref:Xamarin.Forms.StackLayout) 只能按照其方向展开子视图。 因此，垂直方向的 `StackLayout` 可以扩展 [`Label`](xref:Xamarin.Forms.Label) 子视图，该视图将 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性设置为其中一个扩展字段。 这意味着，对于垂直对齐方式，每个 `Label` 在 `StackLayout` 内占据相同的空间量。 但是，只有最后一个 `Label`（可将 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 属性设置为 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)）具有不同的大小。

    > [!IMPORTANT]
    > 使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的所有空间时，扩展首选项不起作用。

    有关对齐方式和扩展的详细信息，请参阅 [Xamarin.Forms 中的布局选项](~/xamarin-forms/user-interface/layouts/layout-options.md)。
