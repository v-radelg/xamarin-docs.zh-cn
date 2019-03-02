---
title: 在多页 Xamarin.Forms 应用程序中执行导航
description: 本文介绍如何将单页面应用程序，能够存储到多页应用程序，可存储多个注释的单个注释。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: f3c5ec1c6d37c74fd9b4fb4980c68a59c9864c05
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197363"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>在多页 Xamarin.Forms 应用程序中执行导航

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)

在此快速入门中，你将了解如何：

- 将其他页添加到 Xamarin.Forms 解决方案。
- 执行页面间导航。
- 使用数据绑定用户界面元素和其数据源之间同步数据。

快速入门教程演示如何将单个页面跨平台 Xamarin.Forms 应用程序，能够存储到多页应用程序，可存储多个注释的单个注释。 最终的应用程序如下所示：

[![](multi-page-images/screenshots1-sml.png "说明页")](multi-page-images/screenshots1.png#lightbox "说明页")
[![](multi-page-images/screenshots2-sml.png "请注意入口页")](multi-page-images/screenshots2.png#lightbox "注意入口页")

### <a name="prerequisites"></a>系统必备

应已成功完成[以前的快速入门](single-page.md)然后再尝试此快速入门。 或者，下载[前面的快速入门示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)并将其用作起始点为本快速入门。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新应用

1. 启动 Visual Studio。 在开始页上单击**打开项目 / 解决方案**，然后在**打开项目**对话框中选择说明项目的解决方案文件：

    ![](multi-page-images/vs/open-solution.png "打开项目")

2. 在中**解决方案资源管理器**，右键单击**说明**项目，然后选择**添加 > 新文件夹**:

    ![](multi-page-images/vs/add-new-item.png "添加新项")

3. 在中**解决方案资源管理器**，将新文件夹命名**模型**:

    ![](multi-page-images/vs/name-folder.png "在 models 文件夹")

4. 在中**解决方案资源管理器**，选择**模型**文件夹，右键单击，并选择**添加 > 新建项...**:

    ![](multi-page-images/vs/add-new-models-file.png "添加新文件")

5. 在中**添加新项**对话框中，选择**VisualC#项 > 类**，将新文件命名**注意**，然后单击**添加**按钮：

    ![](multi-page-images/vs/add-note-class.png "添加注意类")

    这将添加一个名为类**注意**到**模型**文件夹**说明**项目。

6. 在中**Note.cs**，删除所有模板代码并替换为以下代码：

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    此类定义`Note`将应用程序中存储有关每个说明数据模型。    

    保存对更改**Note.cs**通过按**CTRL + S**，然后关闭文件。

7. 在中**解决方案资源管理器**，右键单击**说明**项目，然后选择**添加 > 新建项...**.在中**添加新项**对话框中，选择**VisualC#项 > Xamarin.Forms > 内容页**，将新文件命名**NoteEntryPage**，然后单击**添加**按钮：

    ![](multi-page-images/vs/add-note-entry-page.png "添加 Xamarin.Forms ContentPage")

    这将添加一个名为的新页**NoteEntryPage**项目的根文件夹。 此页将在应用程序中的第二页。

8. 在中**NoteEntryPage.xaml**，删除所有模板代码并替换为以下代码：

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
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
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      此代码以声明方式定义页面，其中包含的用户界面[ `Editor` ](xref:Xamarin.Forms.Editor)文本输入和两个[ `Button` ](xref:Xamarin.Forms.Button)指示应用程序保存或删除的实例一个文件。 这两个`Button`实例水平放置[ `Grid` ](xref:Xamarin.Forms.Grid)，与`Editor`并`Grid`垂直布局[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。 此外，`Editor`使用数据绑定来绑定到`Text`属性的`Note`模型。 有关数据绑定的详细信息，请参阅[数据绑定](deepdive.md#data-binding)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

      保存对更改**NoteEntryPage.xaml**通过按**CTRL + S**，然后关闭文件。

9. 在中**NoteEntryPage.xaml.cs**，删除所有模板代码并替换为以下代码：

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      此代码将存储`Note`实例，表示中的单个注释[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的页面。 当**保存** [ `Button` ](xref:Xamarin.Forms.Button)按下`OnSaveButtonClicked`执行事件处理程序时，这是将内容保存`Editor`到新的文件具有随机生成的文件名，或如果正在更新下现有的文件。 在这两种情况下，该文件存储在应用程序的本地应用程序数据文件夹中。 然后该方法导航回前一页。 当**删除**`Button`按下`OnDeleteButtonClicked`执行事件处理程序时，它将删除该文件，前提是它存在，并导航回前一页。 有关导航的详细信息，请参阅[导航](deepdive.md#navigation)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

      保存对更改**NoteEntryPage.xaml.cs**通过按**CTRL + S**，然后关闭文件。

      > [!WARNING]
      > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

10. 在中**解决方案资源管理器**，右键单击**说明**项目，然后选择**添加 > 新建项...**.在**添加新项**对话框中，选择**VisualC#项 > Xamarin.Forms > 内容页**，将新文件命名**NotesPage**，然后单击**添加**按钮。

      这将添加一个名为页**NotesPage**项目的根文件夹。 此页将为应用程序的根页面。

11. 在中**NotesPage.xaml**，删除所有模板代码并替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
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

    此代码以声明方式定义页面，其中包含的用户界面[ `ListView` ](xref:Xamarin.Forms.ListView)和一个[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)。 `ListView`使用的数据绑定以显示由应用程序，检索任何注释并选择下将导航到`NoteEntryPage`修改注释的位置。 或者，可以通过按创建新便笺`ToolbarItem`。 有关数据绑定的详细信息，请参阅[数据绑定](deepdive.md#data-binding)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**NotesPage.xaml**通过按**CTRL + S**，然后关闭文件。

12. 在中**NotesPage.xaml.cs**，删除所有模板代码并替换为以下代码：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    此代码定义的功能`NotesPage`。 当页出现后时，`OnAppearing`执行方法时，该组件填充[ `ListView` ](xref:Xamarin.Forms.ListView)与已从本地应用程序数据文件夹中检索的任何备注。 当[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)按下`OnNoteAddedClicked`执行事件处理程序。 此方法导航到`NoteEntryPage`，并设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`到新`Note`实例。 中的项后`ListView`处于选中状态`OnListViewItemSelected`执行事件处理程序。 此方法导航到`NoteEntryPage`，并设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`与所选`Note`实例。 有关导航的详细信息，请参阅[导航](deepdive.md#navigation)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**NotesPage.xaml.cs**通过按**CTRL + S**，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

13. 在中**解决方案资源管理器**，双击**App.xaml.cs**以将其打开。 然后使用以下代码替换现有代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    此代码将添加的命名空间声明`System.IO`命名空间，并将添加的声明的静态`FolderPath`类型的属性`string`。 `FolderPath`属性用于在设备上存储路径注意数据将存储位置。 此外，此代码初始化`FolderPath`中的属性`App`构造函数，并将初始化[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)属性设置为[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)承载实例`NotesPage`。 有关导航的详细信息，请参阅[导航](deepdive.md#navigation)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    通过按 **Ctrl+S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

14. 在**解决方案资源管理器**，在**说明**项目，右键单击**MainPage.xaml**，然后选择**删除**。 在对话框中显示按**确定**按钮可以从硬盘中删除该文件。

    这会删除不再使用的页。

15. 生成并运行每个平台上的项目。 有关详细信息，请参阅[构建快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 保存备注应用程序将导航回后**NotesPage**。

    输入一个数字的可变长度，以观察应用程序行为的说明。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新应用

1. 启动 Visual Studio for Mac。 在开始页上单击**打开...**，并在对话框中选择说明项目的解决方案文件：

    ![](multi-page-images/vsmac/open-solution.png "打开解决方案")

2. 在中**Solution Pad**，选择**说明**项目，右键单击，并选择**添加 > 新文件夹**:

    ![](multi-page-images/vsmac/add-new-folder.png "添加新文件夹")

3. 在中**Solution Pad**，将新文件夹命名**模型**:

    ![](multi-page-images/vsmac/name-folder.png "在 models 文件夹")

4. 在中**Solution Pad**，选择**模型**文件夹，右键单击，并选择**添加 > 新建文件...**:

    ![](multi-page-images/vsmac/add-new-models-file.png "添加新文件")

5. 中**新的文件**对话框中，选择**常规 > 的空类**，将新文件命名**注意**，然后单击**新建**按钮：

    ![](multi-page-images/vsmac/add-note-class.png "添加注意类")

    这将添加一个名为类**注意**到**模型**文件夹**说明**项目。

6. 在中**Note.cs**，删除所有模板代码并替换为以下代码：

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    此类定义`Note`将应用程序中存储有关每个说明数据模型。

    保存对更改**Note.cs**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

7. 在中**Solution Pad**，选择**说明**项目，右键单击，并选择**添加 > 新建文件...**.在**新的文件**对话框中，选择**窗体 > 窗体 ContentPage XAML**，将新文件命名**NoteEntryPage**，然后单击**新建**按钮：

    ![](multi-page-images/vsmac/add-note-entry-page.png "添加 Xamarin.Forms ContentPage")

    这将添加一个名为的新页**NoteEntryPage**项目的根文件夹。 此页将在应用程序中的第二页。

8. 在中**NoteEntryPage.xaml**，删除所有模板代码并替换为以下代码：

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
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
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      此代码以声明方式定义页面，其中包含的用户界面[ `Editor` ](xref:Xamarin.Forms.Editor)文本输入和两个[ `Button` ](xref:Xamarin.Forms.Button)指示应用程序保存或删除的实例一个文件。 这两个`Button`实例水平放置[ `Grid` ](xref:Xamarin.Forms.Grid)，与`Editor`并`Grid`垂直布局[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。 此外，`Editor`使用数据绑定来绑定到`Text`属性的`Note`模型。 有关数据绑定的详细信息，请参阅[数据绑定](deepdive.md#data-binding)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

      保存对更改**NoteEntryPage.xaml**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

9. 在中**NoteEntryPage.xaml.cs**，删除所有模板代码并替换为以下代码：

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      此代码将存储`Note`实例，表示中的单个注释[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的页面。 当**保存** [ `Button` ](xref:Xamarin.Forms.Button)按下`OnSaveButtonClicked`执行事件处理程序时，这是将内容保存`Editor`到新的文件具有随机生成的文件名，或如果正在更新下现有的文件。 在这两种情况下，该文件存储在应用程序的本地应用程序数据文件夹中。 然后该方法导航回前一页。 当**删除**`Button`按下`OnDeleteButtonClicked`执行事件处理程序时，它将删除该文件，前提是它存在，并导航回前一页。 有关导航的详细信息，请参阅[导航](deepdive.md#navigation)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

      保存对更改**NoteEntryPage.xaml.cs**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

      > [!WARNING]
      > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

10. 在中**Solution Pad**，选择**说明**项目，右键单击，并选择**添加 > 新建文件...**.在**新的文件**对话框中，选择**窗体 > 窗体 ContentPage XAML**，将新文件命名**NotesPage**，然后单击**新建**按钮。

      这将添加一个名为页**NotesPage**项目的根文件夹。 此页将为应用程序的根页面。

11. 在中**NotesPage.xaml**，删除所有模板代码并替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
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

    此代码以声明方式定义页面，其中包含的用户界面[ `ListView` ](xref:Xamarin.Forms.ListView)和一个[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)。 `ListView`使用的数据绑定以显示由应用程序，检索任何注释并选择下将导航到`NoteEntryPage`修改注释的位置。 或者，可以通过按创建新便笺`ToolbarItem`。 有关数据绑定的详细信息，请参阅[数据绑定](deepdive.md#data-binding)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**NotesPage.xaml**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

12. 在中**NotesPage.xaml.cs**，删除所有模板代码并替换为以下代码：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    此代码定义的功能`NotesPage`。 当页出现后时，`OnAppearing`执行方法时，该组件填充[ `ListView` ](xref:Xamarin.Forms.ListView)与已从本地应用程序数据文件夹中检索的任何备注。 当[ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)按下`OnNoteAddedClicked`执行事件处理程序。 此方法导航到`NoteEntryPage`，并设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`到新`Note`实例。 中的项后`ListView`处于选中状态`OnListViewItemSelected`执行事件处理程序。 此方法导航到`NoteEntryPage`，并设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`与所选`Note`实例。 有关导航的详细信息，请参阅[导航](deepdive.md#navigation)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    保存对更改**NotesPage.xaml.cs**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

13. 在中**Solution Pad**，双击**App.xaml.cs**以将其打开。 然后使用以下代码替换现有代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    此代码将添加的命名空间声明`System.IO`命名空间，并将添加的声明的静态`FolderPath`类型的属性`string`。 `FolderPath`属性用于在设备上存储路径注意数据将存储位置。 此外，此代码初始化`FolderPath`中的属性`App`构造函数，并将初始化[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)属性设置为[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)承载实例`NotesPage`。 有关导航的详细信息，请参阅[导航](deepdive.md#navigation)中[Xamarin.Forms 快速入门的深入探讨](deepdive.md)。

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

14. 在**Solution Pad**，在**说明**项目，右键单击**MainPage.xaml**，然后选择**删除**。 在对话框中显示按**删除**按钮可以从硬盘中删除该文件。

    这会删除不再使用的页。

15. 生成并运行每个平台上的项目。 有关详细信息，请参阅[构建快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 保存备注应用程序将导航回后**NotesPage**。

    输入一个数字的可变长度，以观察应用程序行为的说明。

::: zone-end

## <a name="next-steps"></a>后续步骤

在此快速入门中，你学习了如何：

- 将其他页添加到 Xamarin.Forms 解决方案。
- 执行页面间导航。
- 使用数据绑定用户界面元素和其数据源之间同步数据。

若要修改应用程序，以便将其数据存储在本地数据库中 SQLite.NET，继续学习下一步的快速入门教程。

> [!div class="nextstepaction"]
> [下一页](database.md)

## <a name="related-links"></a>相关链接

- [便笺（示例）](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)
- [Xamarin.Forms 快速入门的深入探讨](deepdive.md)
