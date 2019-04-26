---
title: 使用 Azure 移动应用的脱机数据同步
description: 本文介绍如何将脱机同步功能添加到使用 Azure 移动应用后端的 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: 6080b4dc152558d6f532399cee7424670c588c28
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319566"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>使用 Azure 移动应用的脱机数据同步

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)

_脱机同步允许用户与移动应用程序、 查看、 添加或修改数据，甚至在没有网络连接。更改存储在本地数据库中，并在设备联机后，可以与 Azure 移动应用实例同步所做的更改。本文介绍如何将脱机同步功能添加到 Xamarin.Forms 应用程序。_

## <a name="overview"></a>概述

[Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供了`IMobileServiceTable`接口，它表示可在 Azure 移动应用实例中存储的表执行的操作。 这些操作直接连接到 Azure 移动应用实例，并且如果移动设备没有网络连接将失败。

若要支持脱机同步，Azure 移动客户端 SDK 支持*同步表*，其中提供的`IMobileServiceSyncTable`接口。 此接口提供相同的创建、 读取、 更新和删除 (CRUD) 操作作为`IMobileServiceTable`接口，但操作读取或写入到本地存储。 本地存储不填充新数据从 Azure 移动应用实例，直到调用*拉取*数据。 同样，数据对的调用之前不会发送到 Azure 移动应用实例*推送*本地更改。

脱机同步还支持在 Azure 移动应用实例和自定义冲突解决在本地存储和已更改同一记录时检测冲突。 在本地存储或 Azure 移动应用实例中，也可以处理冲突。

有关脱机同步的详细信息，请参阅[Azure 移动应用中的脱机数据同步](/azure/app-service-mobile/app-service-mobile-offline-data-sync/)并[为 Xamarin.Forms 移动应用启用脱机同步](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/)。

## <a name="setup"></a>安装

将集成的 Xamarin.Forms 应用程序与 Azure 移动应用实例之间的脱机同步的过程如下所示：

1. 创建 Azure 移动应用实例。 有关详细信息，请参阅[使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 添加[Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/)到 Xamarin.Forms 解决方案中的所有项目的 NuGet 包。
1. （可选）启用 Azure 移动应用实例和 Xamarin.Forms 应用程序中的身份验证。 有关详细信息，请参阅[使用 Azure 移动应用进行身份验证用户](~/xamarin-forms/data-cloud/authentication/azure.md)。

以下部分提供了其他安装程序说明如何配置通用 Windows 平台 (UWP) 项目以使用 Microsoft.Azure.Mobile.Client.SQLiteStore NuGet 包。 无需其他设置，才能在 iOS 和 Android 上使用 Microsoft.Azure.Mobile.Client.SQLiteStore NuGet 包。

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要使用 SQLite 通用 Windows 平台 (UWP) 上，执行以下步骤：

1. 安装[适用于通用 Windows 平台的 SQLite](http://sqlite.org/2016/sqlite-uwp-3120200.vsix)在开发环境中的 Visual Studio 扩展。
1. 在 Visual Studio 中的 UWP 项目，右键单击**引用 > 添加引用**，导航到**扩展**，并添加**适用于通用 Windows 平台的 SQLite**并**通用 Windows 平台应用的 visual c + + 2015年运行时**的 UWP 项目扩展。

## <a name="initializing-the-local-store"></a>初始化线程本地存储区

必须初始化本地存储区，然后才可以执行任何同步表操作。 在可移植类库 (PCL) 项目中的 Xamarin.Forms 解决方案被实现此目的：

```csharp
using Microsoft.WindowsAzure.MobileServices;
using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
using Microsoft.WindowsAzure.MobileServices.Sync;

namespace TodoAzure
{
    public partial class TodoItemManager
    {
        static TodoItemManager defaultInstance = new TodoItemManager();
        IMobileServiceClient client;
        IMobileServiceSyncTable<TodoItem> todoTable;

        private TodoItemManager()
        {
            this.client = new MobileServiceClient(Constants.ApplicationURL);
            var store = new MobileServiceSQLiteStore("localstore.db");
            store.DefineTable<TodoItem>();
            this.client.SyncContext.InitializeAsync(store);
            this.todoTable = client.GetSyncTable<TodoItem>();
        }
        ...
  }
}
```

通过创建一个新的本地 SQLite 数据库`MobileServiceSQLiteStore`类，前提是它尚不存在。 然后，将`DefineTable<T>`方法中的字段匹配的本地存储中创建一个表`TodoItem`类型，前提是它尚不存在。

一个*同步上下文*与关联`MobileServiceClient`实例，并对同步表所做的跟踪更改。 同步上下文维护的队列，使创建、 更新和删除 (CUD) 操作的排序的列表将发送到 Azure 移动应用实例更高版本。 `IMobileServiceSyncContext.InitializeAsync()`方法用于将本地存储与同步上下文相关联。

`todoTable`字段是`IMobileServiceSyncTable`，因此所有 CRUD 操作都使用本地存储。

## <a name="performing-synchronization"></a>执行同步

本地存储区同步使用 Azure 移动应用实例时`SyncAsync`调用方法：

```csharp
public async Task SyncAsync()
{
  ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

  try
  {
    await this.client.SyncContext.PushAsync();

    // The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
    // Use a different query name for each unique query in your program.
    await this.todoTable.PullAsync("allTodoItems", this.todoTable.CreateQuery());
  }
  catch (MobileServicePushFailedException exc)
  {
    if (exc.PushResult != null)
    {
      syncErrors = exc.PushResult.Errors;
    }
  }

  // Simple error/conflict handling.
  if (syncErrors != null)
  {
    foreach (var error in syncErrors)
    {
      if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
      {
        // Update failed, revert to server's copy
        await error.CancelAndUpdateItemAsync(error.Result);
      }
      else
      {
        // Discard local change
        await error.CancelAndDiscardItemAsync();
      }

      Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
    }
  }
}
```

`IMobileServiceSyncTable.PushAsync`方法对同步上下文，而不是特定的表，并将所有 CUD 更改都发送自上一次推送。

由执行拉取`IMobileServiceSyncTable.PullAsync`对单个表的方法。 第一个参数`PullAsync`方法是只能在移动设备使用的查询名称。 提供非 null 查询名称在 Azure 移动客户端 SDK 执行的结果*增量同步*，其中每次提取操作返回的结果，最新`updatedAt`从结果的时间戳存储在本地系统表。 后续拉取操作然后仅记录后检索该时间戳。 或者，*完全同步*可以通过传递`null`作为查询名称，这会导致正在检索每个提取操作的所有记录。 任何同步操作后，收到的数据插入到本地存储中。

> [!NOTE]
> 如果针对包含挂起本地更新的表执行拉取，则请求将首先执行对同步上下文推送。 这最大程度减少已排队的更改和新数据从 Azure 移动应用实例之间的冲突。

`SyncAsync`方法还包括用于在本地存储和 Azure 移动应用实例中更改同一记录时处理冲突的基本实现。 当冲突，则本地存储中并在 Azure 移动应用实例中，指出已更新数据时`SyncAsync`方法更新存储在 Azure 移动应用实例中的数据从本地存储中的数据。 任何其他冲突时，`SyncAsync`方法会放弃本地更改。 这将处理方案中本地更改所在的数据的已从 Azure 移动应用实例中删除。

开发人员应在生产应用程序中，编写自定义`IMobileServiceSyncHandler`都适合于其用例的冲突处理实现。 有关详细信息，请参阅[使用乐观并发解决冲突](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency)在 Azure 门户中，并[深入探讨在托管客户端 SDK 中的脱机支持](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/)MSDN 博客上。

## <a name="purging-data"></a>清除数据

可以使用数据的清除本地存储中的表`IMobileServiceSyncTable.PurgeAsync`方法。 此方法支持方案，例如删除应用程序不再需要的过时数据。 例如，示例应用程序仅显示`TodoItem`未完成的实例。 因此，已完成的项不再需要在本地存储。 清除本地存储中的已完成的项目可以按以下方式实现：

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

调用`PurgeAsync`还会触发推送操作。 因此，任何本地标记为已完成的项将从本地存储区被删除前发送到 Azure 移动应用实例中。 但是，如果有同步与 Azure 移动应用实例挂起的操作，否则清除将引发`InvalidOperationException`除非`force`参数设置为`true`。 替代策略是检查`IMobileServiceSyncContext.PendingOperations`属性，它返回挂起的操作，还没有已推送到 Azure 移动应用的实例，并仅执行清除，如果该属性为零的数。

> [!NOTE]
> 调用`PurgeAsync`与`force`参数设置为`true`将会丢失任何挂起的更改。

## <a name="initiating-synchronization"></a>启动同步

在示例应用程序，`SyncAsync`方法通过调用`TodoList.OnAppearing`方法：

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

这意味着应用程序将尝试在启动时同步与 Azure 移动应用实例。

此外，同步可以启动的 iOS 和 Android 中通过使用下拉以刷新和 Windows 平台上的数据，列表中，通过使用**同步**用户界面上的按钮。 有关详细信息，请参阅[下拉以刷新](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh)。

## <a name="summary"></a>总结

本文介绍了如何将脱机同步功能添加到 Xamarin.Forms 应用程序。 脱机同步允许用户与移动应用程序、 查看、 添加或修改数据，甚至在没有网络连接。 更改存储在本地数据库中，并在设备联机后，可以与 Azure 移动应用实例同步所做的更改。


## <a name="related-links"></a>相关链接

- [TodoAzureAuthOfflineSync （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [使用 Azure 移动应用](~/xamarin-forms/data-cloud/consuming/azure.md)
- [使用 Azure 移动应用的用户进行身份验证](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
