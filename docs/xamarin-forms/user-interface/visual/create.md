---
title: 创建 Xamarin.Forms 视觉对象呈现器
description: Xamarin.Forms Visual 使要有选择地应用于 VisualElement 对象，而不必子类 Xamarin.Forms 控件的呈现器。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 3c614bcd0044a0aa4a698c830d2f2af5aba6c65a
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557509"
---
# <a name="xamarinforms-visual"></a>Xamarin.Forms 视觉对象

Xamarin.Forms Visual 使呈现器有选择地应用于`VisualElement`对象，而不必子类 Xamarin.Forms 控件。 指定任何呈现器`VisualType`作为的一部分`ExportRendererAttribute`将用于呈现器视图，而不是默认呈现器。 在呈现器所选内容时，`Visual`检查并呈现器选择过程中包含在视图的属性。

> [!IMPORTANT]
> 当前`Visual`已呈现该控件，但这将在未来版本中更改后，无法更改。

Xamarin.Forms 具有基于材料设计一个可视外观。 有关详细信息，请参阅[Xamarin.Forms 材料 Visual](material-visual.md)。

## <a name="create-a-visual"></a>创建视觉对象

在你的跨平台库，创建派生的类型`IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

## <a name="register-the-visual-type"></a>注册的视觉对象类型

创建所需的视图的呈现器，并将视觉对象类型注册为平台的一部分`ExportRenderAttribute`:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ContentPage), typeof(CustomPageRenderer), new[] { typeof(CustomVisual) })]
namespace MyApp.Android
{
    public class CustomPageRenderer : PageRenderer
    {
        ...
    }
}
```

## <a name="consume-the-visual"></a>使用视觉对象

应用程序可以选择使用自定义视觉对象通过`Visual`属性：

```xaml
<ContentPage ...
             Visual="Custom">
    ...
</ContentPage>
```

等效的 C# 代码是：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = new CustomVisual();
```

## <a name="register-a-name-for-a-visual"></a>注册一个视觉对象的名称

不同的可视库之间可能会发生冲突或可能是您只是想要按不同的名称，请参阅到视觉对象。 在此方案中，您可以使用程序集特性注册给定的视觉对象的不同名称：

```csharp
[assembly: Visual("MyMaterialName", typeof(VisualMarker.MaterialVisual))]
```

然后可以通过其注册的名称来使用自定义视觉对象：

```xaml
<ContentPage ...
             Visual="MyMaterialName">
    ...
</ContentPage>
````

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 材料视觉对象](material-visual.md)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
