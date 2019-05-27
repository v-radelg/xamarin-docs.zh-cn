---
title: Xamarin.Forms Shell 自定义呈现器
description: 可通过各种 Shell 类公开的属性和方法对 Xamarin.Forms Shell 应用程序进行高度自定义。 不过，在需要更为复杂的平台特定的自定义时，也可创建 Shell 自定义呈现器。
ms.prod: xamarin
ms.assetid: 3B1A6AE8-1D1E-4C34-B9AB-48F4444FEF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 59dba2fed0422db72b0617d9a831e3a9364320bd
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970788"
---
# <a name="xamarinforms-shell-custom-renderers"></a>Xamarin.Forms Shell 自定义呈现器

Xamarin.Forms Shell 应用程序的优势之一是可通过各种 Shell 类公开的属性和方法对其外观和行为进行高度自定义。 不过，在需要更为复杂的平台特定的自定义时，也可创建 Shell 自定义呈现器。 与其他自定义呈现器一样，可以将 Shell 自定义呈现器添加到一个平台项目以自定义外观和行为，同时允许其他平台上的默认行为；或者可将其他 Shell 自定义呈现器添加到各个平台项目以自定义 iOS 和 Android 上的外观和行为。

在 iOS 和 Android 上，使用 `ShellRenderer` 类呈现 Shell 应用程序。 在 iOS 上，`ShellRenderer` 类位于 `Xamarin.Forms.Platform.iOS` 命名空间。 在 Android 上，`ShellRenderer` 类位于 `Xamarin.Forms.Platform.Android` 命名空间。

创建 Shell 自定义呈现器的过程如下所示：

1. 创建 `Shell` 类的子类。 此操作将已在 Shell 应用程序中完成。
1. 使用子类 `Shell` 类。 此操作将已在 Shell 应用程序中完成。
1. 在所需的平台上创建派生自 `ShellRenderer` 类的自定义呈现器类。

## <a name="create-a-custom-renderer-class"></a>创建自定义呈现器类

创建 Shell 自定义呈现器类的过程如下所示：

1. 创建 `ShellRenderer` 类的子类。
1. 重写所需的方法，以执行所需的自定义。
1. 将 `ExportRendererAttribute` 添加到 `ShellRenderer` 子类，以指定要使用它来呈现 Shell 应用程序。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 可选择在每个平台项目中提供 Shell 自定义呈现器。 如果未注册自定义呈现器，则将使用默认的 `ShellRenderer` 类。

`ShellRenderer` 类公开下列可重写的方法：

| iOS | Android |
| --- | --- |
| `SetElementSize`<br />`CreateFlyoutRenderer`<br />`CreateNavBarAppearanceTracker`<br />`CreatePageRendererTracker`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellItemTransition`<br />`CreateShellSearchResultsRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTabBarAppearanceTracker`<br />`Dispose`<br />`OnCurrentItemChanged`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`UpdateBackgroundColor` | `CreateFragmentForPage`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellFlyoutRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTrackerForToolbar`<br />`CreateToolbarAppearanceTracker`<br />`CreateTabLayoutAppearanceTracker`<br />`CreateBottomNavViewAppearanceTracker`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`SwitchFragment`<br />`Dispose` |

> [!IMPORTANT]
> iOS 和 Android 上都包含其他 Shell 呈现器类，如 `ShellSectionRenderer` 和 `ShellItemRenderer`。 但这些其他呈现器类由 `ShellRenderer` 类的重写创建而成。 因此，可以创建这些其他呈现器类的子类，并在子类 `ShellRenderer` 类的相应重写中创建该子类的实例，以此方式来自定义这些呈现器类的行为。

### <a name="ios-example"></a>iOS 示例

以下代码示例说明了用于 iOS 的子类 `ShellRenderer`，它将设置 Shell 应用程序导航栏的背景图像：

```csharp
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.iOS.MyShellRenderer))]
namespace Xaminals.iOS
{
    public class MyShellRenderer : ShellRenderer
    {
        protected override IShellSectionRenderer CreateShellSectionRenderer(ShellSection shellSection)
        {
            var renderer = base.CreateShellSectionRenderer(shellSection);
            if (renderer != null)
            {
                (renderer as ShellSectionRenderer).NavigationBar.SetBackgroundImage(UIImage.FromFile("monkey.png"), UIBarMetrics.Default);
            }
            return renderer;
        }
    }
}
```

`MyShellRenderer` 类重写 `CreateShellSectionRenderer` 方法，并检索基类创建的呈现器。 然后它将设置导航栏的背景图像，以修改呈现器，然后再返回呈现器。

### <a name="android-example"></a>Android 示例

以下代码示例说明了用于 Android 的子类 `ShellRenderer`，它将设置 Shell 应用程序导航栏的背景图像：

```csharp
using Android.Content;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.Droid.MyShellRenderer))]
namespace Xaminals.Droid
{
    public class MyShellRenderer : ShellRenderer
    {
        public MyShellRenderer(Context context) : base(context)
        {
        }

        protected override IShellToolbarAppearanceTracker CreateToolbarAppearanceTracker()
        {
            return new MyShellToolbarAppearanceTracker(this);
        }
    }
}
```

`MyShellRenderer` 类重写 `CreateToolbarAppearanceTracker` 方法，然后返回 `MyShellToolbarAppearanceTracker` 类的实例。 以下示例说明了派生自 `ShellToolbarAppearanceTracker` 类的 `MyShellToolbarAppearanceTracker` 类：

```csharp
using Android.Support.V7.Widget;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

namespace Xaminals.Droid
{
    public class MyShellToolbarAppearanceTracker : ShellToolbarAppearanceTracker
    {
        public MyShellToolbarAppearanceTracker(IShellContext context) : base(context)
        {
        }

        public override void SetAppearance(Toolbar toolbar, IShellToolbarTracker toolbarTracker, ShellAppearance appearance)
        {
            base.SetAppearance(toolbar, toolbarTracker, appearance);
            toolbar.SetBackgroundResource(Resource.Drawable.monkey);
        }
    }
}
```

`MyShellToolbarAppearanceTracker` 类重写 `SetAppearance` 方法，然后设置工具栏的背景图像，以修改工具栏。

> [!IMPORTANT]
> 只需将 `ExportRendererAttribute` 添加到派生自 `ShellRenderer` 类的自定义呈现器。 其他子类 Shell 呈现器类由子类 `ShellRenderer` 类创建而成。

## <a name="related-links"></a>相关链接

- [Xamarin.Forms 自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
