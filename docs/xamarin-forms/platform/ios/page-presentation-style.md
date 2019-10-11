---
title: IOS 上的模式页呈现样式
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的设置模式页面的呈现样式。
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 3b1a88968334bed42be53119c26de43ef9cd1419
ms.sourcegitcommit: eb23b7d745d1090376f9def07e0f11cb089494d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72171063"
---
# <a name="modal-page-presentation-style-on-ios"></a>IOS 上的模式页呈现样式

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定用于设置模式页的呈现样式。 设置使用在 XAML`Page.ModalPresentationStyle`可绑定属性设置为`UIModalPresentationStyle`枚举值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

`Page.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Page.SetModalPresentationStyle`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于上设置模式的演示文稿样式[ `Page` ](xref:Xamarin.Forms.Page)通过指定以下项之一`UIModalPresentationStyle`枚举值：

- `FullScreen`用于设置模式的演示文稿样式以覆盖整个屏幕。 默认情况下，使用此演示文稿样式显示模式页面。
- `FormSheet`用于设置模式的演示文稿样式上居中且小于屏幕。

此外，`GetModalPresentationStyle`方法可以用于检索的当前值`UIModalPresentationStyle`应用于枚举[ `Page` ](xref:Xamarin.Forms.Page)。

结果是，在模式的演示文稿样式[ `Page` ](xref:Xamarin.Forms.Page)可以设置：

[![](page-presentation-style-images/modal-presentation-style-small.png "IPad 上的模式演示样式")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "IPad 上的模式演示样式")

> [!NOTE]
> 使用此特定于平台的设置模式的演示文稿样式的页面必须使用模式导航。 有关详细信息，请参阅[Xamarin.Forms 模式页面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
