---
title: 'GTK # 平台安装程序'
description: 'Xamarin.Forms 现在具有对 GTK # 平台的预览支持'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 6243f7d90b921207f4dd406a1f33f4d7af40ecfb
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668357"
---
# <a name="gtk-platform-setup"></a>GTK # 平台安装程序

![预览](~/media/shared/preview.png)

Xamarin.Forms 现在具有对 GTK # 应用程序的预览支持。 GTK # 是允许使用 Mono 和.NET 的图形应用程序的开发完全本机 GNOME GTK + 工具包和多种 GNOME 库链接起来，一个图形用户界面工具包。 本文演示如何将一个 GTK # 项目添加到 Xamarin.Forms 解决方案。

启动、 创建新的 Xamarin.Forms 解决方案，或使用现有的 Xamarin.Forms 解决方案，例如之前, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/)。

> [!NOTE]
> 尽管本文着重介绍到 Xamarin.Forms 解决方案在 VS2017 和 Visual Studio for Mac 添加 GTK # 应用程序，它还可以执行在[MonoDevelop](http://www.monodevelop.com/)适用于 Linux。

## <a name="adding-a-gtk-app"></a>添加一个 GTK # 应用程序

GTK # 适用于 macOS 和 Linux 安装的一部分[Mono](https://www.mono-project.com/download/stable/)。 用于.NET 的 GTK # 可以安装在 Windows 与[GTK # 安装程序](https://www.mono-project.com/download/stable/#download-win)。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

按照这些说明添加将在 Windows 桌面上运行的 GTK # 应用：

1. 在 Visual Studio 2017 中，右键单击解决方案名称在**解决方案资源管理器**，然后选择**添加 > 新建项目...**.

2. 在中**新的项目**窗口中的，在左侧选择**Visual C#** 并**Windows 经典桌面**。 在项目类型列表中，选择**类库 (.NET Framework)**，并确保**Framework**下拉列表设置为.NET Framework 4.7 最小值。

3. 键入与项目的名称**GTK**扩展，例如**GameOfLife.GTK**。 单击**浏览**按钮，选择包含在其他平台的文件夹的项目，然后按**选择文件夹**。 这会将 GTK 项目与解决方案中的其他项目相同的目录中。

    ![添加新的 GTK 项目](gtk-images/win/add-new-project.png "添加新的 GTK 项目")

    按**确定**按钮创建项目。

4. 在中**解决方案资源管理器**，右键单击新的 GTK 项目并选择**管理 NuGet 包**。 选择**浏览**选项卡，并搜索**Xamarin.Forms** 3.0 或更高版本。

    ![选择 Xamarin.Forms NuGet 包](gtk-images/win/select-forms-nuget-package.png "选择 Xamarin.Forms NuGet 包")

    选择包，然后单击**安装**按钮。

5. 在其中搜索**Xamarin.Forms.Platform.GTK** 3.0 包或更高版本。

    ![选择 Xamarin.Forms.Platform.GTK NuGet 包](gtk-images/win/select-forms-platform-nuget-package.png "选择 Xamarin.Forms.Platform.GTK NuGet 包")

    选择包，然后单击**安装**按钮。

6. 在中**解决方案资源管理器**，右键单击解决方案名称，然后选择**为解决方案管理 NuGet 包**。 选择**更新**选项卡和**Xamarin.Forms**包。 选择所有项目，并将其更新为与 GTK 项目使用相同的 Xamarin.Forms 版本。

7. 在中**解决方案资源管理器**，右键单击**引用**GTK 项目中。 在中**引用管理器**对话框中，选择**项目**左侧，并检查到.NET Standard 或共享项目旁的复选框：

    ![引用共享的项目](gtk-images/win/reference-shared-project.png "引用共享的项目")

8. 在中**引用管理器**对话框中，按**浏览**按钮并浏览到**C:\Program Files (x86)\GtkSharp\2.12\lib**文件夹，然后选择**atk sharp.dll**， **gdk sharp.dll**， **glade sharp.dll**， **glib sharp.dll**， **gtk dotnet.dll**， **gtk sharp.dll**文件。

    ![引用 GTK # 库](gtk-images/win/reference-gtk-libraries.png "引用 GTK # 库")

    按**确定**按钮以添加引用。

9. 在 GTK 项目中，重命名**Class1.cs**到**Program.cs**。

10. 在 GTK 项目中，编辑**Program.cs**文件，以便它类似于以下代码：

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    此代码初始化 GTK # 和 Xamarin.Forms 中，创建一个应用程序窗口，并运行的应用程序。

11. 在中**解决方案资源管理器**，右键单击 GTK 项目并选择**属性**。

12. 在中**属性**窗口中，选择**应用程序**选项卡并更改**输出类型**下拉列表到**Windows 应用程序**。

    ![更改项目的输出类型](gtk-images/win/change-project-output-type.png "更改项目的输出类型")

13. 在中**解决方案资源管理器**，右键单击 GTK 项目并选择**设为启动项目**。 按 f5 键以与 Visual Studio 调试器在 Windows 桌面上运行该程序：

    ![GTK # 游戏生命](gtk-images/win/gtk-gameoflife.png "GTK # 游戏的生命周期")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

按照这些说明添加将在 Mac 桌面运行的 GTK # 应用：

1. 在 Visual Studio for Mac 中，右键单击的 Xamarin.Forms 解决方案并选择**添加 > 添加新项目...**.

2. 在中**新的项目**窗口中选择**其他 >.NET > Gtk # 2.0 项目**然后按**下一步**。

3. 键入与项目的名称**GTK**扩展，例如**GameOfLife.GTK**，然后按**创建**。

4. 在中**Solution Pad**，右键单击**包 > 添加包...** GTK 的项目，并添加 Xamarin.Forms 3.0 预发布 NuGet 包或更高版本。

    ![选择 Xamarin.Forms NuGet 包](gtk-images/mac/select-forms-nuget-package.png "选择 Xamarin.Forms NuGet 包")

5. 在中**Solution Pad**，右键单击**包 > 添加包...** GTK 的项目，并添加 Xamarin.Forms.Platform.GTK 3.0 预发布 NuGet 包或更高版本。

    ![选择 Xamarin.Forms.Platform.GTK NuGet 包](gtk-images/mac/select-forms-platform-nuget-package.png "选择 Xamarin.Forms.Platform.GTK NuGet 包")

6. 更新其他平台项目所使用的 GTK 项目使用相同的 Xamarin.Forms 版本。

7. 在中**Solution Pad**，右键单击**引用 > 编辑引用...** GTK 的项目，并添加到 Xamarin.Forms 项目 （.NET Standard 或共享项目） 的引用。

    ![引用共享的项目](gtk-images/mac/reference-shared-project.png "引用共享的项目")

8. 编辑**Program.cs** GTK 项目以便其类似于下面的代码文件：

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    此代码初始化 GTK # 和 Xamarin.Forms 中，创建一个应用程序窗口，并运行的应用程序。

9. 在中**Solution Pad**，右键单击 GTK 项目并选择**设为启动项目**。

10. 在 Visual Studio for Mac 工具栏中，按**启动**按钮 （类似于播放按钮的三角形按钮） 以启动应用。

    ![GTK # 游戏生命](gtk-images/mac/gtk-gameoflife.png "GTK # 游戏的生命周期")

-----

## <a name="next-steps"></a>后续步骤

### <a name="platform-specifics"></a>平台特定信息

您可以确定在 Xamarin.Forms 应用程序运行 XAML 或代码中哪些平台。 这样即可在 GTK # 上运行时更改程序特性。 在代码中，进行比较的值`Device.RuntimePlatform`与`Device.GTK`常量 （它等于字符串"GTK"）。 如果没有匹配项，该应用程序正在上 GTK #。

在 XAML 中，你可以使用`OnPlatform`标记来选择特定于平台的属性值：

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>应用程序图标

在启动时，可以设置应用图标：

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>主题

有各种主题可用于 GTK # 中，并且它们可以从一个 Xamarin.Forms 应用程序使用：

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>本机窗体

本机窗体允许 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页可供本机项目，包括 GTK # 项目。 这可以通过创建的实例来实现[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-派生页，并将其转换为本机 GTK # 类型使用`CreateContainer`扩展方法：

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

有关本机窗体的详细信息，请参阅[本机窗体](~/xamarin-forms/platform/native-forms.md)。

## <a name="issues"></a>问题

这是预览版，因此可以预见，并非所有内容都可用于生产。 当前的实现状态，请参阅[状态](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)，和当前的已知问题，请参阅[挂起和已知问题](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md)。
