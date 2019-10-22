---
title: Windows 上的 RefreshView 拉取方向
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 Windows 平台的，使 RefreshView 的拉取方向得以更改。
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: cf2ab38bed7b45a48fcf0b5f86add49c0d4cc21f
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697649"
---
# <a name="refreshview-pull-direction-on-windows"></a>Windows 上的 RefreshView 拉取方向

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

这通用 Windows 平台平台特定，使 `RefreshView` 的拉取方向更改，使其与显示数据的可滚动控件的方向相匹配。 它通过将 `RefreshView.RefreshPullDirection` 可绑定属性设置为 `RefreshPullDirection` 枚举的值，在 XAML 中使用：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

或者，可以C#使用 Fluent API：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

@No__t_0 方法指定此平台特定的仅在通用 Windows 平台上运行。 [@No__t_2](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间中的 `RefreshView.SetRefreshPullDirection` 方法用于设置 `RefreshView` 的请求方向，同时 `RefreshPullDirection` 枚举提供四个可能的值：

- `LeftToRight` 指示从左到右的请求启动刷新。
- `TopToBottom` 指示从上到下的请求启动刷新，并且是 `RefreshView` 的默认拉取方向。
- `RightToLeft` 指示从右到左的请求启动刷新。
- `BottomToTop` 指示从下到上的请求启动刷新。

此外，`GetRefreshPullDirection` 方法可用于返回 `RefreshView` 的当前 `RefreshPullDirection`。

结果就是将指定 `RefreshPullDirection` 应用于 `RefreshView`，以将拉取方向设置为匹配显示数据的可滚动控件的方向。 以下屏幕截图显示了一个 `LeftToRight` 拉取方向 `RefreshView`：

[![UWP 上的 RefreshView 的屏幕截图](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView，按从左到右的拉取方向")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView，按从左到右的拉取方向")

> [!NOTE]
> 更改请求方向时，进度圆圈的起始位置会自动旋转，以便在拉取方向的适当位置处开始箭头。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
