---
title: 存储和访问 Azure 存储中的数据
description: Azure 存储是一种可缩放的云存储解决方案，可以用于存储非结构化和结构化数据。 本文介绍如何使用 Xamarin.Forms 来在 Azure 存储中存储文本和二进制数据以及如何访问数据。
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
ms.openlocfilehash: 4ecffa0902d186b659e7df07dbcf17053e29c818
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319907"
---
# <a name="storing-and-accessing-data-in-azure-storage"></a>存储和访问 Azure 存储中的数据

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)

_Azure 存储是一种可缩放的云存储解决方案，可以用于存储非结构化和结构化数据。本文演示如何使用 Xamarin.Forms 来在 Azure 存储中存储文本和二进制数据以及如何访问数据。_

Azure 存储提供了四个存储服务：

- Blob 存储。 Blob 可以是文本或二进制数据，例如备份、 虚拟机、 媒体文件或文档。
- 表存储是 NoSQL 键-属性存储。
- 队列存储是工作流处理和云服务之间通信的消息传递服务。
- 文件存储提供了使用 SMB 协议的共享的存储。

有两种类型的存储帐户：

- 常规用途存储帐户提供对 Azure 存储服务的访问，从单个帐户。
- Blob 存储帐户是用于存储 blob 的专用的存储帐户。 只需存储 blob 数据，则建议使用此帐户类型。

本文中，以及随附的示例应用程序，演示了 blob 存储，并将其下载到上传的图像和文本的文件。 此外，它还演示从 blob 存储中检索文件的列表和删除文件。

有关 Azure 存储的详细信息，请参阅[存储空间简介](https://azure.microsoft.com/documentation/articles/storage-introduction/)。

## <a name="introduction-to-blob-storage"></a>Blob 存储简介

Blob 存储包含三个组件，如下图中所示：

![](azure-storage-images/blob-storage.png "Blob 存储概念")

到 Azure 存储的所有访问都都通过存储帐户。 存储帐户可以包含无限的数量的容器，和一个容器可以存储无限的数量的 blob，直至达到存储帐户的容量极限。

Blob 是任何类型和大小的文件。 Azure 存储支持三种不同的 blob 类型：

- 块 blob 进行了优化的流式处理和存储云对象，并且十分适合用于存储备份、 媒体文件和文档等。块 blob 可以是最多为 195 Gb。
- 追加 blob 类似于块 blob，但进行了优化的追加操作，例如日志记录。 追加 blob 可以是最多为 195 Gb。
- 页 blob 针对频繁的读/写操作进行了优化，并且通常用于存储虚拟机和其磁盘。 页 blob 可以达 1 Tb 的大小。

> [!NOTE]
> 请注意，blob 存储帐户支持块和追加 blob，但不是页 blob。

Blob 是上传到 Azure 存储，从 Azure 存储下载的字节流的形式。 因此，文件必须转换为上传，并为其原始的表示形式，下载后转换的回之前的字节流。

Azure 存储中存储每个对象具有唯一的 URL 地址。 存储帐户名称构成该地址和子域和域名名称窗体的组合的子域*终结点*的存储帐户。 例如，如果名为你的存储帐户*mystorageaccount*，存储帐户的默认 blob 终结点是`https://mystorageaccount.blob.core.windows.net`。

将存储帐户中的对象的位置附加到终结点可生成用于访问存储帐户中的某个对象的 URL。 例如，blob 地址将具有格式`https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`。

## <a name="setup"></a>安装

将 Azure 存储帐户集成到 Xamarin.Forms 应用程序的过程如下所示：

1. 创建存储帐户。 有关详细信息，请参阅[创建存储帐户](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account)。
1. 添加[Azure 存储客户端库](https://www.nuget.org/packages/WindowsAzure.Storage/)到 Xamarin.Forms 应用程序。
1. 配置存储连接字符串。 有关详细信息，请参阅[连接到 Azure 存储](#connecting)。
1. 添加`using`指令`Microsoft.WindowsAzure.Storage`和`Microsoft.WindowsAzure.Storage.Blob`到将访问 Azure 存储的类的命名空间。

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>连接到 Azure 存储

针对存储帐户资源发出的每个请求必须进行身份验证。 尽管 blob 可以配置为支持匿名身份验证，有两个应用程序可以使用存储帐户进行身份验证的主要方法：

- 共享的密钥。 此方法使用 Azure 存储帐户名称和帐户密钥访问存储服务。 存储帐户分配两个专用密钥对创建可用于共享密钥身份验证。
- 共享的访问签名。 这是可以附加到 URL，实现对存储资源的委托的访问令牌，权限使用它，为指定的是有效的时间段。

可以包括从应用程序访问 Azure 存储资源所需的身份验证信息指定连接字符串。 此外，可以配置连接字符串以从 Visual Studio 连接到 Azure 存储模拟器。

> [!NOTE]
> Azure 存储连接字符串中支持 HTTP 和 HTTPS。 但是，建议使用 HTTPS。

### <a name="connecting-to-the-azure-storage-emulator"></a>连接到 Azure 存储模拟器

Azure 存储模拟器提供了一个模拟 Azure blob、 队列和表服务以进行开发的本地环境。

应使用以下连接字符串连接到 Azure 存储模拟器：

```csharp
UseDevelopmentStorage=true
```

有关 Azure 存储模拟器的详细信息，请参阅[使用 Azure 存储模拟器进行开发和测试](https://azure.microsoft.com/documentation/articles/storage-use-emulator/)。

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>连接到 Azure 存储中使用共享的密钥

以下连接字符串的格式应该用于连接到 Azure 存储使用共享密钥：

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` 应替换为你的存储帐户的名称和`myAccountKey`应替换为两个帐户访问密钥之一。

> [!NOTE]
> 何时使用共享密钥身份验证、 帐户名和帐户密钥将分发到每个用户都使用应用程序，这将提供完全读/写访问存储帐户中。 因此，使用共享密钥身份验证用于测试目的，并且永远不会分发给其他用户的密钥。

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>连接到 Azure 存储中使用共享访问签名

以下连接字符串的格式应该用于连接到 Azure 存储的 SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` 应替换为你的 blob 终结点的 URL 和`mySharedAccessSignature`应替换为你的 SAS。 SAS 提供协议、 服务终结点，以及用于访问资源的凭据。

> [!NOTE]
> SAS 身份验证被建议用于生产应用程序。 但是，在生产应用程序应从后端服务按需，而不是与应用程序捆绑在正在检索 SAS。

有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。

## <a name="creating-a-container"></a>创建容器

`GetContainer`方法用于检索到的命名容器，然后从容器中检索 blob，或将 blob 添加到容器使用的引用。 下面的代码示例演示`GetContainer`方法：

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

`CloudStorageAccount.Parse`方法分析连接字符串，并返回`CloudStorageAccount`实例，它表示存储帐户。 一个`CloudBlobClient`实例，这用于检索容器和 blob，然后由`CreateCloudBlobClient`方法。 `GetContainerReference`方法检索与指定的容器`CloudBlobContainer`实例，返回到调用方法之前。 在此示例中，容器名称是`ContainerType`转换为小写字符串的枚举值。

> [!NOTE]
> 容器名称必须小写，并且必须以字母或数字开头。 此外，它们只能包含字母、 数字和短划线字符，并且必须是介于 3 到 63 个字符之间。

`GetContainer`方法调用，如下所示：

```csharp
var container = GetContainer(containerType);
```

`CloudBlobContainer`实例然后可用于创建容器，如果它尚不存在：

```csharp
await container.CreateIfNotExistsAsync();
```

默认情况下，新创建的容器是专用的。 这意味着必须指定存储访问密钥才能从容器中检索 blob。 有关使容器公开中的 blob 的信息，请参阅[创建容器](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container)。

## <a name="uploading-data-to-a-container"></a>将数据上传到容器

`UploadFileAsync`方法用于将上传到 blob 存储的字节数据的流，并在下面的代码示例所示：

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

检索容器引用之后, 该方法创建容器，如果它尚不存在。 一个新`Guid`然后创建要用作唯一的 blob 名称，以及作为检索 blob 块引用`CloudBlockBlob`实例。 数据的流然后上传到 blob 使用`UploadFromStreamAsync`方法中，将创建 blob，如果它尚不存在，或者覆盖它，如果不存在。

文件上载到 blob 存储中使用此方法之前，它必须首先转换为字节流。 在下面的代码示例说明了这一点：

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

`text`数据转换为字节数组，然后传递到流的形式包装`UploadFileAsync`方法。

## <a name="downloading-data-from-a-container"></a>从容器下载数据

`GetFileAsync`方法用于从 Azure 存储下载 blob 数据，并在下面的代码示例所示：

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

检索容器引用之后, 该方法检索存储的数据的 blob 引用。 如果存在 blob，通过检索其属性`FetchAttributesAsync`方法。 创建正确的大小的字节数组，并获取返回到调用方法的字节数组形式下载 blob。

下载后的 blob 字节数据，它必须转换为其原始的表示形式。 在下面的代码示例说明了这一点：

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

从 Azure 存储中检索的字节数组`GetFileAsync`方法，然后转换回 UTF8 编码的字符串。

## <a name="listing-data-in-a-container"></a>列出容器中的数据

`GetFilesListAsync`方法用于检索存储在一个容器中 blob 的列表，并在下面的代码示例所示：

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

在检索之后容器引用，该方法使用容器的`ListBlobsSegmentedAsync`方法来检索对容器中 blob 的引用。 返回的结果`ListBlobsSegmentedAsync`方法将枚举虽然`BlobContinuationToken`实例不是`null`。 每个 blob 将强制转换返回`IListBlobItem`到`CloudBlockBlob`顺序访问`Name`blob 之前它是值的属性添加到`allBlobsList`集合。 一次`BlobContinuationToken`实例是`null`，已返回的最后一个的 blob 名称，并执行退出循环。

## <a name="deleting-data-from-a-container"></a>从容器中删除数据

`DeleteFileAsync`方法用于在容器中，删除 blob，并在下面的代码示例所示：

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

检索容器引用之后, 该方法检索指定的 blob 的 blob 引用。 使用然后删除该 blob`DeleteIfExistsAsync`方法。

## <a name="related-links"></a>相关链接

- [Azure 存储 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [存储简介](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [如何通过 Xamarin 使用 Blob 存储](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [使用共享的访问签名 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows Azure 存储 (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)
