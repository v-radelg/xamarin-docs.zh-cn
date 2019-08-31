---
title: Xamarin 中的文件压缩
description: 本文档介绍如何在 Xamarin 中使用 libcompression API。 它讨论了 deflating、因为这样做和支持的不同算法。
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: bcc63aa4e1926f5502d571bf47c83b0c8ea7e429
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199520"
---
# <a name="file-compression-in-xamarinios"></a>Xamarin 中的文件压缩

面向 iOS 9.0 或 macOS 10.11 (和更高版本) 的 Xamarin 应用可以使用_压缩框架_来压缩 (编码) 和解压缩 (解码) 数据。 Xamarin 在流 API 之后提供此框架。 压缩框架允许开发人员与压缩和解压缩数据交互, 就像它们是普通的流, 而无需使用回调或委托。

压缩框架为以下算法提供支持:

* LZ4
* LZ4 Raw
* Lzfse
* Lzma
* Zlib

使用压缩框架, 开发人员可以在没有任何第三方库或 Nuget 的情况下执行压缩操作。 这会减少外部依赖关系, 并确保所有平台上都支持压缩操作 (前提是它们满足最低操作系统要求)。

## <a name="general-file-decompression"></a>常规文件解压缩

压缩框架在 Xamarin 和 Xamarin 中使用流 API。 此 API 意味着, 若要压缩数据, 开发人员可以使用 .NET 中其他 IO Api 中使用的正常模式。 下面的示例演示如何将数据与压缩框架 (类似于`System.IO.Compression.DeflateStream` api 中找到的 api) 解压缩:

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

实现接口 (如其他`System.IO.Streams`), 因此开发人员应确保在不再需要资源后将其释放。 `IDisposable` `CompressionStream`

## <a name="general-file-compression"></a>常规文件压缩

压缩 API 还允许开发人员压缩相同 API 之后的数据。 可以使用`CompressionAlgorithm`枚举器中声明的提供算法之一来压缩数据。

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

支持支持的所有异步操作, 这意味着开发人员可以使用 async 关键字来执行压缩/解压缩操作, 而不会阻止 UI 线程。 `System.IO.DeflateStream` `CompressionStream`
