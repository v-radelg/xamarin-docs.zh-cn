
Xamarin.Forms 有一个模式弹出窗口，称为操作工作表，可用于指导用户完成任务。 在本练习中，你将使用 [`Page`](xref:Xamarin.Forms.Page) 类中的 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法来显示指导用户完成任务的操作工作表。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，添加一个新的 [`Button`](xref:Xamarin.Forms.Button) 声明，该声明将显示一个操作工作表：

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

     [`Button.Text`](xref:Xamarin.Forms.Button.Text) 属性指定在 `Button` 中显示的文本。 此外，将 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为名为 `OnDisplayActionSheetButtonClicked` 的事件处理程序，将在下一步中创建该处理程序。

1. 在“解决方案资源管理器”的 PopupsTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开。 然后在 MainPage.xaml.cs 中，将 `OnDisplayActionSheetButtonClicked` 事件处理程序添加到类：

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    点击 [`Button`](xref:Xamarin.Forms.Button) 时，将执行 `OnDisplayActionSheetButtonClicked` 方法。 此方法调用 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法，向用户显示有关如何继续完成任务的一组替代方案。 用户选择其中一个替代方案后，所选内容将作为 `string` 返回。

    > [!IMPORTANT]
    > [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法为异步方法，应始终使用 `await` 关键字等待。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 然后点击添加到 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 的 [`Button`](xref:Xamarin.Forms.Button)：

    [![iOS 和 Android 上操作工作表的屏幕截图](../images/actionsheet.png "用于指导用户完成任务的操作工作表")](../images/actionsheet-large.png#lightbox "Actionsheet that guides users through a task")

    注意，在操作表对话框中选择替代方案后，选择将输出到 Visual Studio“输出”窗口。

    有关显示操作工作表的详细信息，请参阅[显示弹出窗口](~/xamarin-forms/app-fundamentals/navigation/pop-ups.md)指南中的[引导用户完成任务](~/xamarin-forms/app-fundamentals/navigation/pop-ups.md#guiding-users-through-tasks)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，添加一个新的 [`Button`](xref:Xamarin.Forms.Button) 声明，该声明将显示一个操作工作表：

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

    [`Button.Text`](xref:Xamarin.Forms.Button.Text) 属性指定在 `Button` 中显示的文本。 此外，将 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为名为 `OnDisplayActionSheetButtonClicked` 的事件处理程序，将在下一步中创建该处理程序。

1. 在 Solution Pad 的 PopupsTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开。 然后在 MainPage.xaml.cs 中，将 `OnDisplayActionSheetButtonClicked` 事件处理程序添加到类：

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    点击 [`Button`](xref:Xamarin.Forms.Button) 时，将执行 `OnDisplayActionSheetButtonClicked` 方法。 此方法调用 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法，向用户显示有关如何继续完成任务的一组替代方案。 用户选择其中一个替代方案后，所选内容将作为 `string` 返回。

    > [!IMPORTANT]
    > [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法为异步方法，应始终使用 `await` 关键字等待。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 然后点击添加到 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 的 [`Button`](xref:Xamarin.Forms.Button)：

    [![iOS 和 Android 上操作工作表的屏幕截图](../images/actionsheet.png "用于指导用户完成任务的操作工作表")](../images/actionsheet-large.png#lightbox "Actionsheet that guides users through a task")

    注意，在操作表对话框中选择替代方案后，选择将输出到 Visual Studio for Mac“应用程序输出”窗口。

    有关显示操作工作表的详细信息，请参阅[显示弹出窗口](~/xamarin-forms/app-fundamentals/navigation/pop-ups.md)指南中的[引导用户完成任务](~/xamarin-forms/app-fundamentals/navigation/pop-ups.md#guiding-users-through-tasks)。
