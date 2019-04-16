---
title: 将数据存储在本地 SQLite.NET 数据库中
description: 本文介绍如何在本地 SQLite.NET 数据库中存储数据。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 5c3daf04c08e2109c46b24c198fef8e71fac2f3d
ms.sourcegitcommit: e7f27ba75cae5099ef053b819b84132a77d4f9e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "58854985"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>在本地 SQLite.NET 数据库中存储数据

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)

在此快速入门中，你将了解如何：

- 使用 NuGet 包管理器将 NuGet 包添加到项目。
- SQLite.NET 数据库中的本地存储数据。

快速入门教程演示如何在本地 SQLite.NET 数据库中存储数据。 最终的应用程序如下所示：

[![](database-images/screenshots1-sml.png "说明页")](database-images/screenshots1.png#lightbox "说明页")
[![](database-images/screenshots2-sml.png "请注意入口页")](database-images/screenshots2.png#lightbox "注意入口页")

### <a name="prerequisites"></a>系统必备

应已成功完成[以前的快速入门](multi-page.md)然后再尝试此快速入门。 或者，下载[前面的快速入门示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)并将其用作起始点为本快速入门。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新应用

1. 启动 Visual Studio 并打开说明解决方案。

2. 在中**解决方案资源管理器**，选择**说明**项目，右键单击并选择**管理 NuGet 包...**:

    ![](database-images/vs/add-nuget-packages.png "添加 NuGet 程序包")    

3. 在“NuGet 包管理器”中，选择“浏览”选项卡，搜索“sqlite-net-pcl”NuGet 包，选择它，然后单击“安装”按钮将其添加到项目：

    ![](database-images/vs/add-package.png "添加包")

    > [!NOTE]
    > 许多 NuGet 包都有着类似的名称。 正确的包具有以下属性：
    > - **创建者：** Frank A. Krueger
    > - **ID：** sqlite net pcl
    > - **NuGet 链接：**[sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 尽管包的名称如此，此 NuGet 包也可以用于 .NET Standard 项目。

    此包将用于将数据库操作合并到应用程序。

4. 在中**解决方案资源管理器**，在**说明**项目中，打开**Note.cs**中**模型**文件夹和替换现有代码使用以下代码：

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    此类定义`Note`将应用程序中存储有关每个说明数据模型。 `ID`属性将标有`PrimaryKey`并`AutoIncrement`属性来确保每个`Note`SQLite.NET 数据库中的实例将具有由 SQLite.NET 提供的唯一 id。

    保存对更改**Note.cs**通过按**CTRL + S**，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

5. 在中**解决方案资源管理器**，添加名为的新文件夹**数据**到**说明**项目。

6. 在中**解决方案资源管理器**，在**说明**项目，添加一个名为的新类**NoteDatabase**到**数据**文件夹。

7. 在中**NoteDatabase.cs**，现有代码替换为以下代码：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    此类包含代码以创建数据库、 从中读取数据、 将数据写入到它，并从其中删除数据。 代码使用将数据库操作移动到后台线程的异步 SQLite.NET API。 此外，`NoteDatabase` 构造函数将数据库文件的路径作为参数。 此路径将由提供`App`下一步中的类。

    保存对更改**NoteDatabase.cs**通过按**CTRL + S**，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

8. 在中**解决方案资源管理器**，在**说明**项目中，双击**App.xaml.cs**以将其打开。 然后使用以下代码替换现有代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    此代码定义`Database`创建一个新的属性`NoteDatabase`实例作为单一实例，将中的数据库的文件名作为参数传递`NoteDatabase`构造函数。 以单一实例的形式公开数据库的优势是，所创建的单一数据库连接在应用程序运行时保持打开状态，因此避免了每次执行数据库操作时打开和关闭数据库文件所产生的费用。

    通过按 **Ctrl+S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

9. 在中**解决方案资源管理器**，在**说明**项目中，双击**NotesPage.xaml.cs**以将其打开。 然后，替换`OnAppearing`方法使用以下代码：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    这段代码填入[ `ListView` ](xref:Xamarin.Forms.ListView)与存储在数据库中的任何备注。

    保存对更改**NotesPage.xaml.cs**通过按**CTRL + S**，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

10. 在中**解决方案资源管理器**，双击**NoteEntryPage.xaml.cs**以将其打开。 然后，替换`OnSaveButtonClicked`和`OnDeleteButtonClicked`方法使用以下代码：

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage`存储`Note`实例，表示中的单个注释[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的页面。 当`OnSaveButtonClicked`执行事件处理程序时，`Note`实例保存到数据库和应用程序导航回前一页。 当`OnDeleteButtonClicked`执行事件处理程序时，`Note`从数据库中删除实例和应用程序导航回前一页。

      保存对更改**NoteEntryPage.xaml.cs**通过按**CTRL + S**，然后关闭文件。

11. 生成并运行每个平台上的项目。 有关详细信息，请参阅[构建快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 保存备注应用程序将导航回后**NotesPage**。

    输入一个数字的可变长度，以观察应用程序行为的说明。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新应用

1. 启动 Visual Studio for Mac，并打开说明项目。

2. 在中**Solution Pad**，选择**说明**项目，右键单击并选择**添加 > 添加 NuGet 包...**:

    ![](database-images/vsmac/add-nuget-packages.png "添加 NuGet 程序包")    

3. 在“添加包”窗口中，搜索“sqlite-net-pcl”NuGet 包，选择它，然后单击“添加包”按钮将其添加到项目：

    ![](database-images/vsmac/add-package.png "添加包")

    > [!NOTE]
    > 许多 NuGet 包都有着类似的名称。 正确的包具有以下属性：
    > - **创建者：** Frank A. Krueger
    > - **ID：** sqlite net pcl
    > - **NuGet 链接：**[sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 尽管包的名称如此，此 NuGet 包也可以用于 .NET Standard 项目。

    此包将用于将数据库操作合并到应用程序。

4. 在中**Solution Pad**，在**说明**项目中，打开**Note.cs**中**模型**文件夹，然后将以下现有代码替换为代码：

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    此类定义`Note`将应用程序中存储有关每个说明数据模型。 `ID`属性将标有`PrimaryKey`并`AutoIncrement`属性来确保每个`Note`SQLite.NET 数据库中的实例将具有由 SQLite.NET 提供的唯一 id。

    保存对更改**Note.cs**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

5. 在中**Solution Pad**，添加名为的新文件夹**数据**到**说明**项目。

6. 在中**Solution Pad**，在**说明**项目，添加一个名为的新类**NoteDatabase**到**数据**文件夹。

7. 在中**NoteDatabase.cs**，现有代码替换为以下代码：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    此类包含代码以创建数据库、 从中读取数据、 将数据写入到它，并从其中删除数据。 代码使用将数据库操作移动到后台线程的异步 SQLite.NET API。 此外，`NoteDatabase` 构造函数将数据库文件的路径作为参数。 此路径将由提供`App`下一步中的类。

    保存对更改**NoteDatabase.cs**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

8. 在中**Solution Pad**，在**说明**项目中，双击**App.xaml.cs**以将其打开。 然后使用以下代码替换现有代码：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    此代码定义`Database`创建一个新的属性`NoteDatabase`实例作为单一实例，将中的数据库的文件名作为参数传递`NoteDatabase`构造函数。 以单一实例的形式公开数据库的优势是，所创建的单一数据库连接在应用程序运行时保持打开状态，因此避免了每次执行数据库操作时打开和关闭数据库文件所产生的费用。

    通过选择“文件”>“保存”，或按 **&#8984; + S**，保存对 **App.xaml.cs** 所做的更改，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

9. 在中**Solution Pad**，在**说明**项目中，双击**NotesPage.xaml.cs**以将其打开。 然后，替换`OnAppearing`方法使用以下代码：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    这段代码填入[ `ListView` ](xref:Xamarin.Forms.ListView)与存储在数据库中的任何备注。

    保存对更改**NotesPage.xaml.cs**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

    > [!WARNING]
    > 尝试生成应用程序现在将导致将在后续步骤中修复的错误。

10. 在中**Solution Pad**，双击**NoteEntryPage.xaml.cs**以将其打开。 然后，替换`OnSaveButtonClicked`和`OnDeleteButtonClicked`方法使用以下代码：

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage`存储`Note`实例，表示中的单个注释[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的页面。 当`OnSaveButtonClicked`执行事件处理程序时，`Note`实例保存到数据库和应用程序导航回前一页。 当`OnDeleteButtonClicked`执行事件处理程序时，`Note`从数据库中删除实例和应用程序导航回前一页。

      保存对更改**NoteEntryPage.xaml.cs**通过选择**文件 > 保存**(或通过按 **&#8984; + S**)，然后关闭文件。

11. 生成并运行每个平台上的项目。 有关详细信息，请参阅[构建快速入门](single-page.md#building-the-quickstart)。

    上**NotesPage**按 **+** 按钮导航到**NoteEntryPage**和输入的说明。 保存备注应用程序将导航回后**NotesPage**。

    输入一个数字的可变长度，以观察应用程序行为的说明。

::: zone-end

## <a name="next-steps"></a>后续步骤

在此快速入门中，你学习了如何：

- 使用 NuGet 包管理器将 NuGet 包添加到项目。
- SQLite.NET 数据库中的本地存储数据。

应用程序使用 XAML 样式的样式，继续学习下一步的快速入门教程。

> [!div class="nextstepaction"]
> [下一页](styling.md)

## <a name="related-links"></a>相关链接

- [便笺（示例）](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)
- [Xamarin.Forms 快速入门的深入探讨](deepdive.md)
