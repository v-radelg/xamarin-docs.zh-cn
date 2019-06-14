---
title: 检查设备方向
description: 本文介绍如何使用 Xamarin.Forms DependencyService 类从共享代码访问设备方向。
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: d8763c6fe8e330181c836bc8d10923ea676a07c1
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740936"
---
# <a name="checking-device-orientation"></a>检查设备方向

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

本文将指导你使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 通过每个平台上的本机 API 从共享代码中检查设备方向。 本演练基于 Ali Ozgur 提供的现有 `DeviceOrientation` 插件。 有关详细信息，请参阅 [GitHub 存储库](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)。

- **[创建接口](#Creating_the_Interface)** &ndash; 了解如何在共享代码中创建接口。
- **[iOS 实现](#iOS_Implementation)** &ndash; 了解如何在 iOS 本机代码中实现接口。
- **[Android 实现](#Android_Implementation)** &ndash; 了解如何在 Android 本机代码中实现接口。
- **[UWP 实现](#WindowsImplementation)** &ndash; 了解如何在通用 Windows 平台 (UWP) 的本机代码中实现接口。
- **[在共享代码中实现](#Implementing_in_Shared_Code)** &ndash; 了解如何使用 `DependencyService` 从共享代码调用本机实现。

使用 `DependencyService` 的应用程序将具有以下结构：

![](device-orientation-images/orientation-diagram.png "DependencyService 应用程序结构")

> [!NOTE]
> 无论共享代码中设备是纵向还是横向，都可以检测到，如[设备方向](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation)所示。 本文中描述的方法使用本机特性来获取关于方向的更多信息（包括设备是否倒置）。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>创建界面

首先，在表示打算实现的功能的共享代码中创建一个接口。 对于此示例，该接口中包含一个方法：

```csharp
namespace DependencyServiceSample.Abstractions
{
    public enum DeviceOrientations
    {
        Undefined,
        Landscape,
        Portrait
    }

    public interface IDeviceOrientation
    {
        DeviceOrientations GetOrientation();
    }
}
```

在共享代码中对这个接口进行编码将允许 Xamarin.Forms 应用程序访问每个平台上的设备方向 API。

> [!NOTE]
> 实现接口的类必须具有无参数的构造函数才能使用 `DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 实现

必须在每个特定于平台的应用程序项目中实现接口。 请注意，类有一个无参数的构造函数，从而使 `DependencyService` 可以创建新实例：

```csharp
using UIKit;
using Foundation;

namespace DependencyServiceSample.iOS
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation(){ }

        public DeviceOrientations GetOrientation()
        {
            var currentOrientation = UIApplication.SharedApplication.StatusBarOrientation;
            bool isPortrait = currentOrientation == UIInterfaceOrientation.Portrait
                || currentOrientation == UIInterfaceOrientation.PortraitUpsideDown;

            return isPortrait ? DeviceOrientations.Portrait: DeviceOrientations.Landscape;
        }
    }
}
```

最后，将该 `[assembly]` 属性添加到类的上面（以及任何已经定义的名称空间的外面），包括任何必需的 `using` 语句：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

此属性将该类注册为 `IDeviceOrientation` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<IDeviceOrientation>` 来创建它的实例。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 实现

下面的代码在 Android 上实现 `IDeviceOrientation`：

```csharp
using DependencyServiceSample.Droid;
using Android.Hardware;

namespace DependencyServiceSample.Droid
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public static void Init() { }

        public DeviceOrientations GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            var rotation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = rotation == SurfaceOrientation.Rotation90 || rotation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientations.Landscape : DeviceOrientations.Portrait;
        }
    }
}
```

将该 `[assembly]` 属性添加到类的上面（以及任何已经定义的名称空间的外面），包括任何必需的 `using` 语句：

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

此属性将该类注册为 `IDeviceOrientaiton` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<IDeviceOrientation>` 来创建它的实例。

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台实现

以下代码在通用 Windows 平台上实现 `IDeviceOrientation` 接口：

```csharp
namespace DependencyServiceSample.WindowsPhone
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public DeviceOrientations GetOrientation()
        {
            var orientation = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            if (orientation == Windows.UI.ViewManagement.ApplicationViewOrientation.Landscape) {
                return DeviceOrientations.Landscape;
            }
            else {
                return DeviceOrientations.Portrait;
            }
        }
    }
}
```

将 `[assembly]` 属性添加到类的上面（以及任何已经定义的名称空间的外面），包括任何必需的 `using` 语句：

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

此属性将该类注册为 `DeviceOrientationImplementation` 接口的实现，这意味着可以在共享代码中使用 `DependencyService.Get<IDeviceOrientation>` 来创建它的实例。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共享代码中实现

现在可以编写和测试访问 `IDeviceOrientation` 接口的共享代码。 该页面比较简单，只包括一个按钮，用于根据设备方向更新其本身的文本。 它使用 `DependencyService` 获取 `IDeviceOrientation` 接口的实例 &ndash; 在运行时，该实例将是特定于平台的实现，可以完全访问本机 SDK：

```csharp
public MainPage ()
{
    var orient = new Button {
        Text = "Get Orientation",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    orient.Clicked += (sender, e) => {
       var orientation = DependencyService.Get<IDeviceOrientation>().GetOrientation();
       switch(orientation){
           case DeviceOrientations.Undefined:
                orient.Text = "Undefined";
                break;
           case DeviceOrientations.Landscape:
                orient.Text = "Landscape";
                break;
           case DeviceOrientations.Portrait:
                orient.Text = "Portrait";
                break;
       }
    };
    Content = orient;
}
```

在 iOS、Android 或 Windows 平台上运行此应用程序并按下该按钮即可根据设备方向更新按钮文本。

![](device-orientation-images/orientation.png "设备方向示例")


## <a name="related-links"></a>相关链接

- [使用 DependencyService（示例）](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyService（示例）](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/)
- [Xamarin.Forms 示例](https://github.com/xamarin/xamarin-forms-samples)
