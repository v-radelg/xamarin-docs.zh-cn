---
title: Xamarin.Essentials:剪贴板
description: 本文档介绍 Xamarin.Essentials 中的 Clipboard 类，此类使你能够在应用程序之间将文本复制并粘贴到系统剪贴板。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: c186f5c61bd2fa3df305be92a03135e57e302d02
ms.sourcegitcommit: 6e04246207aa743820029e8c217a43cfdd24f991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2019
ms.locfileid: "67352128"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials:剪贴板

Clipboard 类使你能够在应用程序之间将文本复制并粘贴到系统剪贴板  。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>使用 Clipboard

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

检查 Clipboard 是否有当前已准备好要粘贴的文本  ：

```csharp
var hasText = Clipboard.HasText;
```

将文本设置到 Clipboard  ：

```csharp
await Clipboard.SetTextAsync("Hello World");
```

从 Clipboard 读取文本  ：

```csharp
var text = await Clipboard.GetTextAsync();
```

> [!TIP]
> 必须在主用户界面线程上完成对 Clipboard 的访问。 若要了解如何在主用户界面线程上调用方法，请参阅 [MainThread](~/essentials/main-thread.md) API。

## <a name="api"></a>API

- [Clipboard 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Clipboard API 文档](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
