---
title: 在多页 Xamarin.Forms 应用程序中执行导航
description: 本文介绍如何将单页面应用程序（可以存储单个便笺）转换为可存储多个注释的多页面应用程序。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 9ce02b4c6412eab1f4b1003b262573c59940286c
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "68653800"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>在多页 Xamarin 窗体应用程序中执行导航

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

在本快速入门中，你将学习如何：

- 向 Xamarin 解决方案添加其他页面。
- 在页面间进行导航。
- 使用数据绑定在用户界面元素与其数据源之间同步数据。

快速入门介绍如何将单个页面跨平台 Xamarin 窗体应用程序（可将单个便笺存储到多页应用程序，并可以存储多个注释）。 最终的应用程序如下所示：

[![](multi-page-images/screenshots1-sml.png "备注")](multi-page-images/screenshots1.png#lightbox "备注页")页
备注[输入(multi-page-images/screenshots2-sml.png "")页![]](multi-page-images/screenshots2.png#lightbox "便笺条目页面")

### <a name="prerequisites"></a>系统必备

在尝试此快速入门之前，应成功完成[以前的快速入门](single-page.md)。 或者，下载[前面的快速入门示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)，并将其用作本快速入门的起点。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新应用

1. 启动 Visual Studio。 在 "开始" 窗口中，单击 "最近使用的项目/解决方案" 列表中的**Notes**解决方案，或者单击 "**打开项目或解决方案**"，然后在 "**打开项目/解决方案**" 对话框中，选择 "注释" 项目的解决方案文件：

    ![](multi-page-images/vs/open-solution.png "打开项目")

2. 在**解决方案资源管理器**中，右键单击**Notes**项目，然后选择 "**添加 > 新文件夹**"：

    ![](multi-page-images/vs/add-new-item.png "添加新项")

3. 在**解决方案资源管理器**中，将**新文件夹命名为：**

    ![](multi-page-images/vs/name-folder.png "模型文件夹")

4. 在**解决方案资源管理器**中，选择 "**模型**" 文件夹，右键单击，然后选择 "**添加 > 新项 ...** "：

    ![](multi-page-images/vs/add-new-models-file.png "添加新文件")

5. 在 "**添加新项**" 对话框中，选择 "**可视C#项 > 类**"，为**新文件命名，然后**单击 "**添加**" 按钮：

    ![](multi-page-images/vs/add-note-class.png "添加便笺类")

    这会将名为**Note**的类添加到**Notes**项目的**模型**文件夹中。

6. 在**Note.cs**中，删除所有模板代码并将其替换为以下代码：

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

    此类定义一个`Note`模型，该模型将在应用程序中存储有关每个注释的数据。    

    通过按**CTRL + S**保存对**Note.cs**所做的更改，并关闭该文件。

7. 在**解决方案资源管理器**中，右键单击**Notes**项目，然后选择 "**添加 > 新项 ...** "在 "**添加新项**" 对话框中，选择 "  **C#可视项" > Xamarin. Forms > 内容 "页**上，将新文件命名为**NoteEntryPage**，然后单击"**添加**"按钮：

    ![](multi-page-images/vs/add-note-entry-page.png "添加 Xamarin。窗体 ContentPage")

    这会将名为**NoteEntryPage**的新页添加到项目的根文件夹中。 此页将是应用程序中的第二页。

8. 在**NoteEntryPage**中，删除所有模板代码并将其替换为以下代码：

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

      此代码以声明方式定义了页面的用户界面，其中包含[`Editor`](xref:Xamarin.Forms.Editor)用于文本输入的和两个[`Button`](xref:Xamarin.Forms.Button)指示应用程序保存或删除文件的实例。 这两`Button`个实例[`Grid`](xref:Xamarin.Forms.Grid)在中水平布局， `Editor`并且`Grid`在中[`StackLayout`](xref:Xamarin.Forms.StackLayout)垂直布局。 此外， `Editor`使用数据绑定来绑定`Text`到`Note`模型的属性。 有关数据绑定的详细信息，请参阅 Xamarin 中的[数据绑定](deepdive.md#data-binding) [。窗体快速入门深入探讨](deepdive.md)。

      通过按**CTRL + S**保存对**NoteEntryPage**所做的更改，并关闭该文件。

9. 在**NoteEntryPage.xaml.cs**中，删除所有模板代码并将其替换为以下代码：

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

      此代码`Note` [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)在页面的中存储一个表示单个便笺的实例。 按下 "**保存** [`Button`](xref:Xamarin.Forms.Button) " `OnSaveButtonClicked`时，将执行事件处理程序，这会将的`Editor`内容保存到带有随机生成的文件名的新文件，或者保存到现有文件（如果正在更新注释）。 在这两种情况下，文件都存储在应用程序的本地应用程序数据文件夹中。 然后，该方法将导航回上一页。 按下 "**删除** `Button` " `OnDeleteButtonClicked`时，将执行事件处理程序（如果该文件存在，则删除该文件），然后导航到上一页。 有关导航的详细信息，请参阅 Xamarin 中的[导航](deepdive.md#navigation)[深入探讨](deepdive.md)。

      通过按**CTRL + S**保存对**NoteEntryPage.xaml.cs**所做的更改，并关闭该文件。

      > [!WARNING]
      > 尝试在此时构建应用程序将导致后续步骤中将修复错误。

10. 在**解决方案资源管理器**中，右键单击**Notes**项目，然后选择 "**添加 > 新项 ...** "在 "**添加新项**" 对话框中，选择 "  **C#可视项" > Xamarin. Forms > 内容 "页**上，将新文件命名为**NotesPage**，然后单击"**添加**"按钮。

      这会将名为**NotesPage**的页面添加到项目的根文件夹中。 此页将是应用程序的根页。

11. 在**NotesPage**中，删除所有模板代码并将其替换为以下代码：

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

    此代码以声明方式定义了页面的用户界面，该用户界面[`ListView`](xref:Xamarin.Forms.ListView)由[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)和组成。 使用数据绑定来显示应用程序检索到的任何说明，选择注释会导航到可以修改注释的`NoteEntryPage`位置。 `ListView` 或者，可以通过按`ToolbarItem`来创建新的便笺。 有关数据绑定的详细信息，请参阅 Xamarin 中的[数据绑定](deepdive.md#data-binding) [。窗体快速入门深入探讨](deepdive.md)。

    通过按**CTRL + S**保存对**NotesPage**所做的更改，并关闭该文件。

12. 在**NotesPage.xaml.cs**中，删除所有模板代码并将其替换为以下代码：

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

    此代码定义了的功能`NotesPage`。 当显示该页时， `OnAppearing`将执行方法，该方法将[`ListView`](xref:Xamarin.Forms.ListView)使用从本地应用程序数据文件夹中检索到的任何注释填充。 按下时，将执行事件处理程序。`OnNoteAddedClicked` [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 此方法导航`NoteEntryPage`到，并将的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)设置`NoteEntryPage`为新`Note`实例。 选择中`ListView`的项时，将执行事件处理程序。`OnListViewItemSelected` 此方法导航`NoteEntryPage`到，并将的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)设置`NoteEntryPage`为所选`Note`实例。 有关导航的详细信息，请参阅 Xamarin 中的[导航](deepdive.md#navigation)[深入探讨](deepdive.md)。

    通过按**CTRL + S**保存对**NotesPage.xaml.cs**所做的更改，并关闭该文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致后续步骤中将修复错误。

13. 在**解决方案资源管理器**中，双击 " **App.xaml.cs** " 将其打开。 然后，将现有代码替换为以下代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

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
            // ...
        }
    }
    ```

    此代码将添加`System.IO`命名空间的命名空间声明，并为类型`string`的静态`FolderPath`属性添加声明。 `FolderPath`属性用于将路径存储在要存储注释数据的设备上。 此外，该`FolderPath`代码在`App` [`MainPage`](xref:Xamarin.Forms.Application.MainPage)构造函数中初始化属性，并将[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)属性初始化`NotesPage`为承载实例的。 有关导航的详细信息，请参阅 Xamarin 中的[导航](deepdive.md#navigation)[深入探讨](deepdive.md)。

    通过按 **Ctrl+S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

14. 在**解决方案资源管理器**的 "**注释**" 项目中，右键单击**MainPage**，然后选择 "**删除**"。 在出现的对话框中，按 "**确定"** 按钮以从硬盘中删除该文件。

    这会删除不再使用的页面。

15. 在每个平台上生成并运行项目。 有关详细信息，请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 保存该注释后，应用程序将导航回**NotesPage**。

    输入一些不同长度的说明，以观察应用程序的行为。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新应用

1. 启动 Visual Studio for Mac。 在 "开始" 窗口中，单击 "**打开**"，然后在对话框中选择 "注释" 项目的解决方案文件：

    ![](multi-page-images/vsmac/open-solution.png "打开解决方案")

2. 在**Solution Pad**中，选择**Notes**项目，右键单击，然后选择 "**添加 > 新文件夹**"：

    ![](multi-page-images/vsmac/add-new-folder.png "添加新文件夹")

3. 在**Solution Pad**中，将**新文件夹命名为：**

    ![](multi-page-images/vsmac/name-folder.png "模型文件夹")

4. 在**Solution Pad**中，选择 "**模型**" 文件夹，右键单击，然后选择 "**添加 > 新文件 ...** "：

    ![](multi-page-images/vsmac/add-new-models-file.png "添加新文件")

5. 在 "**新建文件**" 对话框中，选择 "**常规 > 空类**"，将新文件命名为**Note**，并单击 "**新建**" 按钮：

    ![](multi-page-images/vsmac/add-note-class.png "添加便笺类")

    这会将名为**Note**的类添加到**Notes**项目的**模型**文件夹中。

6. 在**Note.cs**中，删除所有模板代码并将其替换为以下代码：

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

    此类定义一个`Note`模型，该模型将在应用程序中存储有关每个注释的数据。

    通过选择 "文件" > "**保存**" （或按 **&#8984; + S**）来保存对**Note.cs**所做的更改，然后关闭该文件。

7. 在**Solution Pad**中，选择**Notes**项目，右键单击，然后选择 "**添加 > 新文件 ...** "。在 "**新建文件**" 对话框中，选择**Forms > forms ContentPage XAML**，将新文件命名为**NoteEntryPage**，然后单击 "**新建**" 按钮：

    ![](multi-page-images/vsmac/add-note-entry-page.png "添加 Xamarin。窗体 ContentPage")

    这会将名为**NoteEntryPage**的新页添加到项目的根文件夹中。 此页将是应用程序中的第二页。

8. 在**NoteEntryPage**中，删除所有模板代码并将其替换为以下代码：

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

      此代码以声明方式定义了页面的用户界面，其中包含[`Editor`](xref:Xamarin.Forms.Editor)用于文本输入的和两个[`Button`](xref:Xamarin.Forms.Button)指示应用程序保存或删除文件的实例。 这两`Button`个实例[`Grid`](xref:Xamarin.Forms.Grid)在中水平布局， `Editor`并且`Grid`在中[`StackLayout`](xref:Xamarin.Forms.StackLayout)垂直布局。 此外， `Editor`使用数据绑定来绑定`Text`到`Note`模型的属性。 有关数据绑定的详细信息，请参阅 Xamarin 中的[数据绑定](deepdive.md#data-binding) [。窗体快速入门深入探讨](deepdive.md)。

      通过选择 "**文件" > "保存**" （或按 **&#8984; + S**）来保存对**NoteEntryPage**的更改，并关闭该文件。

9. 在**NoteEntryPage.xaml.cs**中，删除所有模板代码并将其替换为以下代码：

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

      此代码`Note` [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)在页面的中存储一个表示单个便笺的实例。 按下 "**保存** [`Button`](xref:Xamarin.Forms.Button) " `OnSaveButtonClicked`时，将执行事件处理程序，这会将的`Editor`内容保存到带有随机生成的文件名的新文件，或者保存到现有文件（如果正在更新注释）。 在这两种情况下，文件都存储在应用程序的本地应用程序数据文件夹中。 然后，该方法将导航回上一页。 按下 "**删除** `Button` " `OnDeleteButtonClicked`时，将执行事件处理程序（如果该文件存在，则删除该文件），然后导航到上一页。 有关导航的详细信息，请参阅 Xamarin 中的[导航](deepdive.md#navigation)[深入探讨](deepdive.md)。

      通过选择 "文件" > "**保存**" （或按 **&#8984; + S**）来保存对**NoteEntryPage.xaml.cs**所做的更改，然后关闭该文件。

      > [!WARNING]
      > 尝试在此时构建应用程序将导致后续步骤中将修复错误。

10. 在**Solution Pad**中，选择**Notes**项目，右键单击，然后选择 "**添加 > 新文件 ...** "。在 "**新建文件**" 对话框中，选择**Forms > forms ContentPage XAML**，将新文件命名为**NotesPage**，然后单击 "**新建**" 按钮。

      这会将名为**NotesPage**的页面添加到项目的根文件夹中。 此页将是应用程序的根页。

11. 在**NotesPage**中，删除所有模板代码并将其替换为以下代码：

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

    此代码以声明方式定义了页面的用户界面，该用户界面[`ListView`](xref:Xamarin.Forms.ListView)由[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)和组成。 使用数据绑定来显示应用程序检索到的任何说明，选择注释会导航到可以修改注释的`NoteEntryPage`位置。 `ListView` 或者，可以通过按`ToolbarItem`来创建新的便笺。 有关数据绑定的详细信息，请参阅 Xamarin 中的[数据绑定](deepdive.md#data-binding) [。窗体快速入门深入探讨](deepdive.md)。

    通过选择 "**文件" > "保存**" （或按 **&#8984; + S**）来保存对**NotesPage**的更改，并关闭该文件。

12. 在**NotesPage.xaml.cs**中，删除所有模板代码并将其替换为以下代码：

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

    此代码定义了的功能`NotesPage`。 当显示该页时， `OnAppearing`将执行方法，该方法将[`ListView`](xref:Xamarin.Forms.ListView)使用从本地应用程序数据文件夹中检索到的任何注释填充。 按下时，将执行事件处理程序。`OnNoteAddedClicked` [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 此方法导航`NoteEntryPage`到，并将的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)设置`NoteEntryPage`为新`Note`实例。 选择中`ListView`的项时，将执行事件处理程序。`OnListViewItemSelected` 此方法导航`NoteEntryPage`到，并将的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)设置`NoteEntryPage`为所选`Note`实例。 有关导航的详细信息，请参阅 Xamarin 中的[导航](deepdive.md#navigation)[深入探讨](deepdive.md)。

    通过选择 "文件" > "**保存**" （或按 **&#8984; + S**）来保存对**NotesPage.xaml.cs**所做的更改，然后关闭该文件。

    > [!WARNING]
    > 尝试在此时构建应用程序将导致后续步骤中将修复错误。

13. 在**Solution Pad**中，双击 " **App.xaml.cs** " 将其打开。 然后，将现有代码替换为以下代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

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
            // ...
        }
    }
    ```

    此代码将添加`System.IO`命名空间的命名空间声明，并为类型`string`的静态`FolderPath`属性添加声明。 `FolderPath`属性用于将路径存储在要存储注释数据的设备上。 此外，该`FolderPath`代码在`App` [`MainPage`](xref:Xamarin.Forms.Application.MainPage)构造函数中初始化属性，并将[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)属性初始化`NotesPage`为承载实例的。 有关导航的详细信息，请参阅 Xamarin 中的[导航](deepdive.md#navigation)[深入探讨](deepdive.md)。

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

14. 在**Solution Pad**的 "**注释**" 项目中，右键单击**MainPage**，然后选择 "**删除**"。 在出现的对话框中，按 "**删除**" 按钮以从硬盘中删除该文件。

    这会删除不再使用的页面。

15. 在每个平台上生成并运行项目。 有关详细信息，请参阅[生成快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 保存该注释后，应用程序将导航回**NotesPage**。

    输入一些不同长度的说明，以观察应用程序的行为。

::: zone-end

## <a name="next-steps"></a>后续步骤

在本快速入门中, 你学习了如何:

- 向 Xamarin 解决方案添加其他页面。
- 在页面间进行导航。
- 使用数据绑定在用户界面元素与其数据源之间同步数据。

若要修改应用程序以便将其数据存储在本地 SQLite.NET 数据库中，请继续学习下一个快速入门教程。

> [!div class="nextstepaction"]
> [下一页](database.md)

## <a name="related-links"></a>相关链接

- [便笺（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Xamarin. 窗体快速入门深入探讨](deepdive.md)
