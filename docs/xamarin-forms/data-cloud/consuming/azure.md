---
title: 使用 Azure 移动应用
description: 此文章，这仅适用于使用 Node.js 后端的 Azure 移动应用，介绍了如何查询、 插入、 更新和删除数据存储在 Azure 移动应用实例中的表。
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 8bd77ec4975fae3cc7245c5adc2b5ef18568b9e1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328372"
---
# <a name="consuming-an-azure-mobile-app"></a>使用 Azure 移动应用

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)

_Azure 移动应用让你能够使用移动身份验证、 脱机同步和推送通知的支持在 Azure 应用服务中托管的可缩放后端进行开发的应用。此文章，这仅适用于使用 Node.js 后端的 Azure 移动应用，介绍了如何查询、 插入、 更新和删除数据存储在 Azure 移动应用实例中的表。_

> [!NOTE]
> 自 6 月 30 日，所有新的 Azure 移动应用将使用 TLS 1.2 默认情况下创建。 此外，我们还建议，现有 Azure 移动应用进行重新配置为使用 TLS 1.2。 有关如何强制执行在 Azure 移动应用中的 TLS 1.2 的信息，请参阅[强制实施 TLS 版本](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions)。 有关如何配置为使用 TLS 1.2 的 Xamarin 项目的信息，请参阅[传输层安全 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)。

有关如何创建可供 Xamarin.Forms 的 Azure 移动应用实例的信息，请参阅[创建 Xamarin.Forms 应用](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)。 按照这些说明进行操作之后, 可下载的示例应用程序可以将配置为使用设置，以使用 Azure 移动应用实例`Constants.ApplicationURL`到 Azure 移动应用实例的 URL。 然后，运行示例应用程序时它将连接到 Azure 移动应用的实例，如以下屏幕截图中所示：

![](azure-images/portal.png "示例应用程序")

对 Azure 移动应用的访问是通过[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)，和 Xamarin.Forms 示例应用程序到 Azure 中的所有连接通过 HTTPS 都发出。

> [!NOTE]
> 在 iOS 9 及更高版本中，应用传输安全 (ATS) 强制执行安全连接之间 （例如应用程序的后端服务器） 的 internet 资源和应用程序中，从而防止意外泄露敏感信息。 由于默认情况下构建适用于 iOS 9 应用程序中启用了 ATS，所有连接都将遵循 ATS 安全要求。 如果连接不能满足这些要求，它们将失败并出现异常。
> 如果不能使用 ATS 可以选择退出的`HTTPS`协议并确保对 internet 资源的通信安全。 这可以通过更新应用程序的实现**Info.plist**文件。 有关详细信息请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-an-azure-mobile-app-instance"></a>使用 Azure 移动应用实例

[Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供了`MobileServiceClient`类，该类用于通过 Xamarin.Forms 应用程序访问 Azure 移动应用实例，如下面的代码示例中所示：

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

当`MobileServiceClient`创建实例时，必须指定应用程序 URL 来标识 Azure 移动应用实例。 此值可以从仪表板中的移动应用获取[Microsoft Azure 门户](https://portal.azure.com/)。

对引用`TodoItem`必须获取存储在 Azure 移动应用实例中的表，然后才可以对该表执行操作。 这通过调用来实现`GetTable`方法`MobileServiceClient`实例，它将返回`IMobileServiceTable<TodoItem>`引用。

### <a name="querying-data"></a>查询数据

可以通过调用来检索表的内容`IMobileServiceTable.ToEnumerableAsync`方法以异步方式计算查询并返回结果。 数据也可以通过包括筛选服务器端`Where`在查询中的子句。 `Where`子句将行筛选谓词对表中，查询应用，如下面的代码示例中所示：

```csharp
public async Task<ObservableCollection<TodoItem>> GetTodoItemsAsync (bool syncItems = false)
{
  ...
  IEnumerable<TodoItem> items = await todoTable
              .Where (todoItem => !todoItem.Done)
              .ToEnumerableAsync ();

  return new ObservableCollection<TodoItem> (items);
}
```

此查询将返回所有项从`TodoItem`表`Done`属性等于`false`。 查询结果然后置于`ObservableCollection`进行显示。

### <a name="inserting-data"></a>插入数据

在 Azure 移动应用实例中插入数据时，将自动生成新列中所需的表，提供在 Azure 移动应用实例中启用该动态架构。 `IMobileServiceTable.InsertAsync`方法用于将新的数据行插入到指定的表，如下面的代码示例中所示：

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

插入请求时，传递给 Azure 移动应用实例的数据中必须未指定 ID。 如果插入请求中包含一个 ID`MobileServiceInvalidOperationException`将引发。

之后`InsertAsync`方法完成后，Azure 移动应用实例中的数据的 ID 将填入`TodoItem`Xamarin.Forms 应用程序中的实例。

### <a name="updating-data"></a>更新数据

当更新 Azure 移动应用实例中的数据时，将自动生成新列中所需的表，提供在 Azure 移动应用实例中启用该动态架构。 `IMobileServiceTable.UpdateAsync`方法用于更新现有数据的新信息，如下面的代码示例中所示：

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

在更新请求时，必须指定 ID，以便 Azure 移动应用实例可以确定要更新的数据。 此 ID 值存储在`TodoItem.ID`属性。 如果更新请求不包含 ID 没有更新，以确定的数据的 Azure 移动应用实例方法，因此`MobileServiceInvalidOperationException`将引发。

### <a name="deleting-data"></a>删除数据

`IMobileServiceTable.DeleteAsync`方法用于删除数据从 Azure 移动应用表，如下面的代码示例中所示：

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

删除请求时，必须指定 ID，以便 Azure 移动应用 sinstance 可以确定要删除的数据。 此 ID 值存储在`TodoItem.ID`属性。 如果 delete 请求不包含一个 ID，没有要删除，以确定的数据的 Azure 移动应用实例方法，因此`MobileServiceInvalidOperationException`将引发。

## <a name="summary"></a>总结

本文介绍了如何使用[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)来查询、 插入、 更新和删除数据存储在表中的 Azure 移动应用实例。 该 SDK 提供`MobileServiceClient`Xamarin.Forms 应用程序用于访问 Azure 移动应用实例的类。


## <a name="related-links"></a>相关链接

- [TodoAzure （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [创建 Xamarin.Forms 应用](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Azure 移动客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
