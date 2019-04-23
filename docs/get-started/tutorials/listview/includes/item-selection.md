---
ms.openlocfilehash: c33db7de63a585cb6fb47aa1145aa724c9eb790f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037401"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“MainPage.xaml”中，修改 [`ListView`](xref:Xamarin.Forms.ListView) 声明，以便为 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件设置处理程序：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    此代码将 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件设置为名为 `OnListViewItemSelected` 的事件处理程序，将 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件设置为名为 `OnListViewItemTapped` 的事件处理程序。 这两个事件处理程序都将在下一步中进行创建。

1. 在“解决方案资源管理器”的 ListViewTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开。 然后在 MainPage.xaml.cs 中，将 `OnListViewItemSelected` 和 `OnListViewItemTapped` 事件处理程序添加到类：

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    点击 [`ListView`](xref:Xamarin.Forms.ListView) 中的项时，会触发 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，将分别执行 `OnListViewItemSelected` 和 `OnListItemTapped` 方法。 这两个方法的 `sender` 参数是负责触发事件的 `ListView` 对象，还可用于访问 `ListView` 对象。 `OnListViewItemSelected` 方法中的 [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 参数提供了所选项，`OnListViewItemTapped` 方法中的 [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) 参数提供了已点击的项。

    > [!IMPORTANT]
    > 只有在 [`ListView`](xref:Xamarin.Forms.ListView) 中选择了新项时才会触发 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 因此，点击同一项两次将触发两个 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，但只会触发一个 `ItemSelected` 事件。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序：

    [![在 iOS 和 Android 上响应项选择和点击的 ListView 的屏幕截图](../images/item-selection.png "ListView 项选择")](../images/item-selection-large.png#lightbox "ListView item selection")

    在两个事件处理程序中设置断点，然后点击 [`ListView`](xref:Xamarin.Forms.ListView) 中的项。 请注意，只有在 [`ListView`](xref:Xamarin.Forms.ListView) 中选择了新项时才会触发 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件，而每次点击一个项时将触发 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件。

    有关项选择和点击的详细信息，请参阅 [ListView 交互](~/xamarin-forms/user-interface/listview/interactivity.md)指南中的[选择和点击](~/xamarin-forms/user-interface/listview/interactivity.md#selection--taps)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在“MainPage.xaml”中，修改 [`ListView`](xref:Xamarin.Forms.ListView) 声明，以便为 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件设置处理程序：

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    此代码将 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件设置为名为 `OnListViewItemSelected` 的事件处理程序，将 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件设置为名为 `OnListViewItemTapped` 的事件处理程序。 这两个事件处理程序都将在下一步中进行创建。

1. 在 Solution Pad 的 ListViewTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开。 然后在 MainPage.xaml.cs 中，将 `OnListViewItemSelected` 和 `OnListViewItemTapped` 事件处理程序添加到类：

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    点击 [`ListView`](xref:Xamarin.Forms.ListView) 中的项时，会触发 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，将分别执行 `OnListViewItemSelected` 和 `OnListItemTapped` 方法。 这两个方法的 `sender` 参数是负责触发事件的 `ListView` 对象，还可用于访问 `ListView` 对象。 `OnListViewItemSelected` 方法中的 [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 参数提供了所选项，`OnListViewItemTapped` 方法中的 [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) 参数提供了已点击的项。

    > [!IMPORTANT]
    > 只有在 [`ListView`](xref:Xamarin.Forms.ListView) 中选择了新项时才会触发 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 因此，点击同一项两次将触发两个 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，但只会触发一个 `ItemSelected` 事件。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![在 iOS 和 Android 上响应项选择和点击的 ListView 的屏幕截图](../images/item-selection.png "ListView 项选择")](../images/item-selection-large.png#lightbox "ListView item selection")

    在两个事件处理程序中设置断点，然后点击 [`ListView`](xref:Xamarin.Forms.ListView) 中的项。 请注意，只有在 [`ListView`](xref:Xamarin.Forms.ListView) 中选择了新项时才会触发 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件，而每次点击一个项时将触发 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件。

    有关项选择和点击的详细信息，请参阅 [ListView 交互](~/xamarin-forms/user-interface/listview/interactivity.md)指南中的[选择和点击](~/xamarin-forms/user-interface/listview/interactivity.md#selection--taps)。
