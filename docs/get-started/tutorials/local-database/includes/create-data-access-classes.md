---
ms.openlocfilehash: b11a5972c2aabace8a6991a82f5719f34450297d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841463"
---
在本练习中，你将向 LocalDatabaseTutorial 项目添加数据访问类，用于将人员相关数据保存到数据库中  。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在解决方案资源管理器的 LocalDatabaseTutorial 项目中，向该项目添加名为 `Person` 的新类   。 然后，在 Person.cs 中，删除所有模板代码，替换为以下代码  ：

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    此代码定义 `Person` 类，该类将在应用程序中存储关于每个人的数据。 使用 `PrimaryKey` 和 `AutoIncrement` 特性标记 `ID` 属性，以确保数据库中的每个 `Person` 实例均具有 SQLite.NET 提供的惟一 ID。

1. 在解决方案资源管理器的 LocalDatabaseTutorial 项目中，向该项目添加名为 `Database` 的新类   。 然后，在 Database.cs 中，删除所有模板代码并替换为以下代码  ：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    此类包含用于创建数据库、从中读取数据和向其写入数据的代码。 代码使用将数据库操作移动到后台线程的异步 SQLite.NET API。 此外，`Database` 构造函数将数据库文件的路径作为参数。 在下一个练习中，此路径由 `App` 类提供。

1. 在解决方案资源管理器的 LocalDatabaseTutorial 项目中，展开 App.xaml，然后双击 App.xaml.cs 将其打开     。 然后在“App.xaml.cs”中，删除所有模板代码并替换为以下代码  ：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    此代码定义新建 `Database` 实例作为单一实例的 `Database` 属性。 表示数据库存储位置的本地文件路径和文件名被作为参数传递给 `Database` 类构造函数。

    > [!IMPORTANT]
    > 以单一实例的形式公开数据库的优势是，所创建的单一数据库连接在应用程序运行时保持打开状态，因此避免了每次执行数据库操作时打开和关闭数据库文件所产生的费用。

1. 生成解决方案，确保没有任何错误。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 Solution Pad 的 LocalDatabaseTutorial 项目中，向该项目添加名为 `Person` 的新类   。 然后，在 Person.cs 中，删除所有模板代码，替换为以下代码  ：

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    此代码定义 `Person` 类，该类将在应用程序中存储关于每个人的数据。 使用 `PrimaryKey` 和 `AutoIncrement` 特性标记 `ID` 属性，以确保数据库中的每个 `Person` 实例均具有 SQLite.NET 提供的惟一 ID。

1. 在 Solution Pad 的 LocalDatabaseTutorial 项目中，向该项目添加名为 `Database` 的新类   。 然后，在 Database.cs 中，删除所有模板代码并替换为以下代码  ：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    此类包含用于创建数据库、从中读取数据和向其写入数据的代码。 代码使用将数据库操作移动到后台线程的异步 SQLite.NET API。 此外，`Database` 构造函数将数据库文件的路径作为参数。 在下一个练习中，此路径由 `App` 类提供。

1. 在 Solution Pad 的 LocalDatabaseTutorial 项目中，展开 App.xaml，然后双击 App.xaml.cs 将其打开     。 然后在“App.xaml.cs”中，删除所有模板代码并替换为以下代码  ：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    此代码定义新建 `Database` 实例作为单一实例的 `Database` 属性。 表示数据库存储位置的本地文件路径和文件名被作为参数传递给 `Database` 类构造函数。

    > [!IMPORTANT]
    > 以单一实例的形式公开数据库的优势是，所创建的单一数据库连接在应用程序运行时保持打开状态，因此避免了每次执行数据库操作时打开和关闭数据库文件所产生的费用。

1. 生成解决方案，确保没有任何错误。
