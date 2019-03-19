---
title: Xamarin.Essentials:共享
description: Xamarin.Essentials 中的 Share 类使应用程序能够将数据（例如文本和 Web 链接）共享到设备上的其他应用程序。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: ad56a626133e03c1ca75b1db26b0904d5df7fea3
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58175325"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials:共享

Share 类使应用程序能够将数据（例如文本和 Web 链接）共享到设备上的其他应用程序。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>使用 Share

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

通过调用具有数据请求有效负载的 `RequestAsync` 方法来使用 Share 功能，此有效负载包括要共享到其他应用程序的信息。 文本和 URI 可以混合使用，每个平台都将根据内容进行筛选处理。

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

在进行请求时显示的共享到外部应用程序的用户界面：

![共享](images/share.png)

## <a name="platform-differences"></a>平台差异

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` 属性用于所需的消息主题。

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` 未使用。
* `Title` 未使用。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* 如果未设置，`Title` 将默认为应用程序名称。
* `Subject` 未使用。

-----

## <a name="api"></a>API

- [Share 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Share API 文档](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
