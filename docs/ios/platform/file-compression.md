---
title: Xamarin.iOS 中的文件压缩
description: 本文档介绍如何在 Xamarin.iOS libcompression API 使用。 它讨论了以放气都，和其他支持的算法。
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: f7a1df65047fd8040dd40e9f7f057d6bfe6dea61
ms.sourcegitcommit: 4c97f5d73be7eb2da153a85183be4258b6b11ca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58290920"
---
# <a name="file-compression-in-xamarinios"></a>Xamarin.iOS 中的文件压缩

面向 iOS 9.0 或 macOS 10.11 及更高版本的 Xamarin 应用程序可以使用_压缩 Framework_压缩 （编码） 和解压缩 （解码） 数据。 Xamarin.iOS 提供了以下 Stream API 此框架。 压缩框架允许开发人员与压缩和解压缩数据，就好像它们是正常流而无需使用回调或委托。

压缩 framework 提供支持以下算法：

* LZ4
* 原始 LZ4
* Lzfse
* Lzma
* Zlib

使用压缩框架允许开发人员执行压缩操作而无需任何第三方库或 Nuget。 这可以减少外部依赖关系，并确保 （只要它们满足最低操作系统要求） 将在所有平台上支持的压缩操作。

## <a name="general-file-decompression"></a>常规文件解压缩

压缩框架使用 Xamarin.iOS 和 Xamarin.Mac 流 API。 此 API，则意味着，若要压缩数据，开发人员可以使用其他 IO Api 在.NET 中使用的正常模式。 下面的示例演示如何解压缩压缩 framework，类似于 API 中找到数据`System.IO.Compression.DeflateStream`API:

```csharp
// sample zlib data
static byte [] compressed_data = { 0xf3, 0x48, 0xcd, 0xc9, 0xc9, 0xe7, 0x02, 0x00 };

using (var backing = new MemoryStream (compressed_data)) // compress data to read
using (var decompressing = new CompressionStream (backing, CompressionMode.Decompress, CompressionAlgorithm.Zlib)) // create decompression stream with the correct algorithm
using (var reader = new StreamReader (decompressing))
{
    // perform the required stream operations
    Console.WriteLine (reader.ReadLine ());
}
```

`CompressionStream`实现`IDisposable`接口，与其他值一样`System.IO.Streams`，因此开发人员应确保要求一次性释放资源。

## <a name="general-file-compression"></a>常规文件压缩

压缩 API 还允许开发人员遵循相同的 API 的数据压缩。 可以使用其中一个提供的算法中所述压缩数据`CompressionAlgorithm`枚举器。

```csharp
// sample method that copies the data from the source stream to the destination stream
static void CopyStream (Stream src, Stream dest)
{
    byte[] array = new byte[1024];
    int bytes_read;
    bytes_read = src.Read (array, 0, 1024);
    while (bytes_read != 0) {
        dest.Write (array, 0, bytes_read);
        bytes_read = src.Read (array, 0, 1024);
    }
}

static void CompressExample ()
{
    // create some sample data to compress
    byte [] data = new byte[100000];
    for (int i = 0; i < 100000; i++) {
        data[i] = (byte) i;
    }

    using (var dataStream = new MemoryStream (data)) // stream that contains the data to compress
    using (var backing = new MemoryStream ()) // stream in which the compress data will be written
    using (var compressing = new CompressionStream (backing, CompressionMode.Compress, CompressionAlgorithm.Zlib, true))
    {
        // copy the data to the compressing stream
        CopyStream (dataStream, compressing);
        // at this point, the CompressionStream contains all the data from the dataStream but compressed
        // using the zlib algorithm
    }
```

## <a name="async-support"></a>异步支持

`CompressionStream`支持通过支持的所有异步操作`System.IO.DeflateStream`，这意味着开发人员可以使用 async 关键字而不会阻塞 UI 线程执行压缩/解压缩操作。