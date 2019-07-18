---
title: Xamarin.Essentials:连接性
description: Xamarin.Essentials 中的 Connectivity 类可用于监视设备的网络状况是否发生变化，检查当前的网络访问权限，以及当前的连接方式。
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 01/08/2019
ms.custom: video
ms.openlocfilehash: c70510f7b47f93c6119532b6a1c06f6c2e9e56ea
ms.sourcegitcommit: afe9d93373d66eb45d82cabefca83b5733969634
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855755"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials:连接性

 Connectivity 类可用于监视设备的网络状况是否发生变化，检查当前的网络访问权限，以及当前的连接方式。

## <a name="get-started"></a>入门

[!include[](~/essentials/includes/get-started.md)]

若要访问  Connectivity 功能，需要以下特定于平台的设置。

# <a name="androidtabandroid"></a>[Android](#tab/android)

需要具有 `AccessNetworkState` 权限，并且必须在 Android 项目中进行配置。 可以通过以下方法添加此权限：

打开 Properties 文件夹下的 AssemblyInfo.cs 文件并添加   ：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

或更新 Android 清单：

打开 Properties 文件夹下的 AndroidManifest.xml 文件，并在“manifest”节点内添加以下代码    。

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

或右键单击 Android 项目并打开项目的属性。 在  “Android 清单”下查找  “所需权限:”区域，然后选中  “访问网络状态”权限。 这样会自动更新 AndroidManifest.xml 文件  。

# <a name="iostabios"></a>[iOS](#tab/ios)

无需其他设置。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

无需其他设置。

-----

## <a name="using-connectivity"></a>使用 Connectivity

在你的类中添加对 Xamarin.Essentials 的引用：

```csharp
using Xamarin.Essentials;
```

检查当前网络访问：

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

[网络访问](xref:Xamarin.Essentials.NetworkAccess)分为以下几类：

* Internet  – 本地和 Internet 访问。
* ConstrainedInternet  – 受限 Internet 访问。 指示强制网络门户连接情况，其中可以本地访问 Web 门户，但需要通过门户提供特定凭据才能访问 Internet。
* 本地  – 仅限本地网络访问。
* 无  – 无可用连接。
* **未知** – 无法确定 Internet 连接。

你可以检查设备当前正在使用哪种[连接配置文件](xref:Xamarin.Essentials.ConnectionProfile)：

```csharp
var profiles = Connectivity.ConnectionProfiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

只要连接配置文件或网络访问发生变化，就可以接收已触发的事件：

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(object sender, ConnectivityChangedEventArgs e)
    {
        var access = e.NetworkAccess;
        var profiles = e.ConnectionProfiles;
    }
}
```

## <a name="limitations"></a>限制

需要注意的是 `Internet` 可能由 `NetworkAccess` 报告，但对 Web 的完全访问权限不可用。 由于每个平台上的连接方式不同，因此只能保证连接可用。 例如，设备可能会连接到 Wi-Fi 网络，但路由器与 Internet 断开连接。 在此示例中，可能会报告 Internet，但活动连接不可用。

## <a name="api"></a>API

* [Connectivity 源代码](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Connectivity API 文档](xref:Xamarin.Essentials.Connectivity)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Connectivity-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
