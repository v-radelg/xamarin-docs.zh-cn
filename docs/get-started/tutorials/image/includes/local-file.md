图像文件可以添加到平台项目中，并从 Xamarin.Forms 共享代码中引用。 当图像特定于平台时，例如在不同平台上使用不同分辨率或稍微不同的设计时，需要这种分发图像的方法。

在本练习中，你将修改 ImageTutorial 解决方案以显示本地图像，而不是从 URI 下载的图像。 本地图像是 Xamarin 徽标，应通过单击下面的按钮下载。

> [!div class="nextstepaction"]
> [下载 XamarinLogo.png](https://raw.githubusercontent.com/xamarin/xamarin-forms-samples/master/UserInterface/PlatformSpecifics/Droid/Resources/drawable/XamarinLogo.png)

> [!IMPORTANT]
> 要跨所有平台使用单个图像，必须在每个平台上使用相同的文件名，并且该名称应为有效的 Android 资源名称（即只允许使用小写字母、数字、下划线和句点）。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“解决方案资源管理器”的“ImageTutorial.iOS”项目中，展开“资产目录”，然后双击“资产”将其打开。 然后，在“Assets.xcassets“选项卡中，单击”加号“按钮并选择”添加图像集“：

    ![在 Visual Studio 中的资产目录中创建新图像集的屏幕截图](../images/vs/new-image-set.png "新资产目录图像集")

1. 在“Assets.xcassets”选项卡中，选择新的图像集，然后将显示编辑器：

    ![Visual Studio 中的资产目录中新图像集的屏幕截图](../images/vs/new-image-set-editor.png "资产目录图像集编辑器")

1. 将“XamarinLogo.png”从文件系统拖到“通用”类别的“1x”框：

    ![Visual Studio 中包含图像的图像集的屏幕截图](../images/vs/image-set-with-image.png "包含图像的图像集")

1. 在“Assets.xcassets“选项卡中，右键单击新图像集的名称并将其重命名为“XamarinLogo“：

    ![Visual Studio 中重命名的图像集的屏幕截图](../images/vs/rename-image-set.png "重命名的图像集")

    保存并关闭“Assets.xcassets“选项卡。

1. 在“解决方案资源管理器”中的“ImageTutorial.Android“项目中，展开“资源”文件夹。 然后，将“XamarinLogo.png“从文件系统拖动至“可绘制“文件夹：

    ![Visual Studio 中作为 Android 资源的图像文件的屏幕截图](../images/vs/android-resource.png "Android 资源文件夹中的本地图像文件")

    > [!NOTE]
    > Visual Studio 会自动将图像的生成操作设置为“AndroidResource“。

1. 在“ImageTutorial“项目的“MainPage.xaml“中，修改 [`Image`](xref:Xamarin.Forms.Editor) 声明以显示本地“XamarinLogo“文件：

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    此代码将 [`Source`](xref:Xamarin.Forms.Image.Source) 属性设置为要显示的本地文件。 将 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 属性在 iOS 上设置为 300 个与设备无关的单元，在 Android 上设置为 250 个与设备无关的单元。 此外，[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性指定图像将水平居中。

    > [!NOTE]
    > 对于 iOS 上的 PNG 图像，可以从 [`Source`](xref:Xamarin.Forms.Image.Source) 属性中指定的文件名中省略“.png“扩展名。 对于其他图像格式，需要该扩展名。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![iOS 和 Android 上显示本地图像的图像视图的屏幕截图](../images/local-file.png "显示本地图像的图像视图")](../images/local-file-large.png#lightbox "Image view displaying a local image")

    有关本地图像的详细信息，请参阅 [Xamarin.Forms 中的图像](~/xamarin-forms/user-interface/images.md)指南中的[本地图像](~/xamarin-forms/user-interface/images.md#local-images)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在“Solution Pad“的”ImageTutorial.iOS“项目中，双击”Assets.xcassets“以将其打开。 然后在“资产列表”中，右键单击并选择“新图像集”：

    ![Visual Studio for Mac 中在资产目录中创建新图像集的屏幕截图](../images/vsmac/new-image-set.png "新资产目录图像集")

1. 在“资产列表”选项卡中，选择新的图像集，然后将显示编辑器：

    ![Visual Studio for Mac 中资产目录中的新图像集的屏幕截图](../images/vsmac/new-image-set-editor.png "资产目录图像集编辑器")

1. 将“XamarinLogo.png”从文件系统拖到“通用”类别的“1x”框：

    ![Visual Studio for Mac 中包含图像的图像集的屏幕截图](../images/vsmac/image-set-with-image.png "包含图像的图像集")

1. 在“资产列表“中，双击新图像集的名称并将其重命名为“XamarinLogo“：

    ![Visual Studio for Mac 中重命名的图像集的屏幕截图](../images/vsmac/rename-image-set.png "重命名的图像集")

1. 在“Solution Pad”中的“ImageTutorial.Android“项目中，展开“资源”文件夹。 然后，将“XamarinLogo.png“从文件系统拖动至“可绘制“文件夹。

1. 在“将文件添加到文件夹”对话框中，选择“确认”。

    ![Visual Studio for Mac 中作为 Android 资源的图像文件的屏幕截图](../images/vsmac/android-resource.png "Android 资源文件夹中的本地图像文件")

    > [!NOTE]
    > Visual Studio for Mac 会自动将图像的生成操作设置为“AndroidResource“。

1. 在“ImageTutorial“项目的“MainPage.xaml“中，修改 [`Image`](xref:Xamarin.Forms.Editor) 声明以显示本地“XamarinLogo“文件：

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    此代码将 [`Source`](xref:Xamarin.Forms.Image.Source) 属性设置为要显示的本地文件。 将 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 属性在 iOS 上设置为 300 个与设备无关的单元，在 Android 上设置为 250 个与设备无关的单元。 此外，[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性指定图像将水平居中。

    > [!NOTE]
    > 对于 iOS 上的 PNG 图像，可以从 [`Source`](xref:Xamarin.Forms.Image.Source) 属性中指定的文件名中省略“.png“扩展名。 对于其他图像格式，需要该扩展名。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![iOS 和 Android 上显示本地图像的图像视图的屏幕截图](../images/local-file.png "显示本地图像的图像视图")](../images/local-file-large.png#lightbox "Image view displaying a local image")

    有关本地图像的详细信息，请参阅 [Xamarin.Forms 中的图像](~/xamarin-forms/user-interface/images.md)指南中的[本地图像](~/xamarin-forms/user-interface/images.md#local-images)。
