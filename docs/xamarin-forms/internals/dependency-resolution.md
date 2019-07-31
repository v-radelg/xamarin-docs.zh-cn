---
title: 在 Xamarin.Forms 中的依赖项解析
description: 此文章介绍了如何将依赖项解析方法注入到 Xamarin.Forms，以便应用程序的依赖关系注入容器具有控制创建和自定义呈现器、 效果和 DependencyService 实现的生存期。
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
ms.openlocfilehash: e2cab7191bb4877deacb3f3f3235a3a4d5c65612
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656367"
---
# <a name="dependency-resolution-in-xamarinforms"></a>在 Xamarin.Forms 中的依赖项解析

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_此文章介绍了如何将依赖项解析方法注入到 Xamarin.Forms，以便应用程序的依赖关系注入容器具有控制创建和自定义呈现器、 效果和 DependencyService 实现的生存期。在本文中的代码示例摘自[使用容器的依赖项解析](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)示例。_

在上下文中使用模型-视图-视图模型 (MVVM) 模式的 Xamarin.Forms 应用程序，用于注册和解析视图模型，以及注册服务和将其注入到视图模型，可以使用依赖关系注入容器。 在视图模型创建期间容器会注入任何所需的依存关系。 如果尚未创建这些依赖项，该容器创建，并将首先解析依赖项。 有关依赖关系注入，包括将依赖项注入到视图模型的示例的详细信息请参阅[依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

对创建的控制和 Xamarin.Forms，它使用传统上执行平台项目中的类型的生存期`Activator.CreateInstance`方法创建的自定义呈现器的效果，实例和[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)实现。 遗憾的是，这就限制了开发人员可以控制创建和生存期这些类型，并将依赖项注入到它们的功能。 通过将依赖项解析方法注入到 Xamarin.Forms，用于控制如何将创建类型 – 通过应用程序的依赖关系注入容器，或通过 Xamarin.Forms，可以更改此行为。 但是，请注意，没有无需将依赖项解析方法注入到 Xamarin.Forms。 Xamarin.Forms 将继续创建和管理在平台项目中的类型的生存期，如果不注入依赖关系解析方法。

> [!NOTE]
> 尽管本文着重介绍将依赖项解析方法注入到 Xamarin.Forms 解析已注册的类型使用依赖关系注入容器，还有可能注入使用工厂方法来解决的依赖关系解析方法已注册的类型。 有关详细信息，请参阅[使用工厂方法的依赖项解析](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo)示例。

## <a name="injecting-a-dependency-resolution-method"></a>注入依赖关系解析方法

[ `DependencyResolver` ](xref:Xamarin.Forms.Internals.DependencyResolver)类提供的功能将依赖项解析方法注入到 Xamarin.Forms 中，使用[ `ResolveUsing` ](Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*)方法。 然后，当 Xamarin.Forms 需要特定类型的实例时，依赖关系解析方法都有机会提供该实例。 如果依赖项解析方法返回`null`的请求的类型，Xamarin.Forms 回退到尝试创建类型实例本身使用`Activator.CreateInstance`方法。

下面的示例演示如何设置具有的依赖关系解析方法[ `ResolveUsing` ](Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*)方法：

```csharp
using Autofac;
using Xamarin.Forms.Internals;
...

public partial class App : Application
{
    // IContainer and ContainerBuilder are provided by Autofac
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();

    public App()
    {
        ...
        DependencyResolver.ResolveUsing(type => container.IsRegistered(type) ? container.Resolve(type) : null);
        ...
    }
    ...
}
```

在此示例中，依赖关系解析方法设置为使用 Autofac 依赖关系注入容器解析已向容器注册的任何类型的 lambda 表达式。 否则为`null`将返回，这将导致在 Xamarin.Forms 中尝试解析的类型。

> [!NOTE]
> 通过依赖关系注入容器使用的 API 是特定于容器。 在本文中的代码示例使用 Autofac 作为依赖关系注入容器，它提供`IContainer`和`ContainerBuilder`类型。 替代依赖关系注入容器同样可用，但不是此处介绍会使用不同的 Api。

请注意，无需在应用程序启动过程中设置依赖关系解析方法。 可以随时设置它。 唯一约束是 Xamarin.Forms 需要了解的有关依赖关系解析方法时，应用程序尝试使用存储在依赖关系注入容器中的类型。 因此，如果应用程序将需要在启动过程的依赖关系注入容器中的服务，将具有依赖关系解析方法要设置应用程序的生命周期中及早。 同样，如果将依赖关系注入容器管理的创建和生存期的特定[ `Effect` ](xref:Xamarin.Forms.Effect)，Xamarin.Forms 将需要尝试创建视图之前了解有关依赖关系解析方法，使用该`Effect`。

> [!WARNING]
> 注册和解析依赖关系注入容器使用的类型具有性能成本由于反射的用于创建每种类型的容器的使用，尤其是依赖项会被重新构造，以便在应用程序中每个页面导航。 如果有许多或深入的依赖项，则创建的成本会显著增加。

## <a name="registering-types"></a>注册类型

它可以通过依赖关系解析方法解析它们之前，必须向依赖关系注入容器注册类型。 下面的代码示例显示了注册方法的示例应用程序公开在`App`类，用于 Autofac 容器：

```csharp
using Autofac;
using Autofac.Core;
...

public partial class App : Application
{
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();
    ...

    public static void RegisterType<T>() where T : class
    {
        builder.RegisterType<T>();
    }

    public static void RegisterType<TInterface, T>() where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>().As<TInterface>();
    }

    public static void RegisterTypeWithParameters<T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where T : class
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        });
    }

    public static void RegisterTypeWithParameters<TInterface, T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        }).As<TInterface>();
    }

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

当应用程序使用依赖关系解析方法来解决从容器的类型时，通常从平台项目中执行类型注册。 这样，平台项目的自定义呈现器的效果，注册类型和[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)实现。

从平台项目，按照类型注册`IContainer`必须生成对象，这通过调用来实现`BuildContainer`方法。 此方法将调用的 Autofac`Build`方法`ContainerBuilder`实例，生成新的依赖关系注入容器，其中包含所做的注册。

以下各节，在`Logger`类，该类实现`ILogger`接口注入到类构造函数。 `Logger`类实现简单的日志记录功能使用`Debug.WriteLine`方法，用于展示如何将服务注入到自定义呈现器的效果，并[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)实现。

### <a name="registering-custom-renderers"></a>注册自定义呈现器

示例应用程序包括播放 web 视频，其 XAML 源显示在下面的示例中的页面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

`VideoPlayer`由每个平台上实现视图`VideoPlayerRenderer`类，提供用于播放视频的功能。 有关这些自定义呈现器类的详细信息，请参阅[实现视频播放器](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)。

在 iOS 和通用 Windows 平台 (UWP) 上`VideoPlayerRenderer`类具有以下构造函数中，这要求`ILogger`参数：

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台上执行与依赖关系注入容器的类型注册`RegisterTypes`方法之前加载的应用程序使用的平台, 调用`LoadApplication(new App())`方法。 下面的示例演示`RegisterTypes`iOS 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

在此示例中，`Logger`具体类型注册通过对其接口类型，映射和`VideoPlayerRenderer`的接口映射不直接注册类型。 当用户导航到包含的页`VideoPlayer`视图中，将调用的依赖关系解析方法要解决`VideoPlayerRenderer`类型从依赖关系注入容器，这还将解决并注入`Logger`键入`VideoPlayerRenderer`构造函数。

`VideoPlayerRenderer` Android 平台上的构造函数是稍微复杂一些，因为它需要`Context`除了参数`ILogger`参数：

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

下面的示例演示`RegisterTypes`Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

在此示例中，`App.RegisterTypeWithParameters`方法注册`VideoPlayerRenderer`与依赖关系注入容器。 此注册方法可确保`MainActivity`实例将作为注入`Context`自变量，并且`Logger`类型将作为注入`ILogger`参数。

### <a name="registering-effects"></a>注册效果

示例应用程序包括使用触摸屏输入跟踪效果拖动的页面[ `BoxView` ](xref:Xamarin.Forms.BoxView)页面周围的实例。 [ `Effect` ](xref:Xamarin.Forms.Effect)添加到`BoxView`使用以下代码：

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

`TouchEffect`类是[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)由每个平台上实现`TouchEffect`类，该类具有`PlatformEffect`。 在平台`TouchEffect`类提供的功能用于拖动`BoxView`围绕页。 有关这些影响类的详细信息，请参阅[调用影响来自事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。

在所有平台`TouchEffect`类具有以下构造函数中，这要求`ILogger`参数：

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台上执行与依赖关系注入容器的类型注册`RegisterTypes`方法之前加载的应用程序使用的平台, 调用`LoadApplication(new App())`方法。 下面的示例演示`RegisterTypes`Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

在此示例中，`Logger`具体类型注册通过对其接口类型，映射和`TouchEffect`的接口映射不直接注册类型。 当用户导航到包含的页[ `BoxView` ](xref:Xamarin.Forms.BoxView)具有实例`TouchEffect`附加到它，依赖关系解析调用该方法将解析平台`TouchEffect`从依赖关系的类型注入容器，它还将解决并注入`Logger`中键入`TouchEffect`构造函数。

### <a name="registering-dependencyservice-implementations"></a>注册 DependencyService 实现

示例应用程序包括使用的页面[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)实现在每个平台上以允许用户从设备的图片库中选取照片。 `IPhotoPicker`接口定义的功能由实现`DependencyService`实现中，并在下面的示例所示：

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

在每个平台项目中，`PhotoPicker`类实现`IPhotoPicker`使用平台 Api 的接口。 有关这些依赖关系服务的详细信息，请参阅[图片库从选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

在 iOS 和 UWP`PhotoPicker`类具有以下构造函数中，这要求`ILogger`参数：

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台上执行与依赖关系注入容器的类型注册`RegisterTypes`方法之前加载的应用程序使用的平台, 调用`LoadApplication(new App())`方法。 下面的示例演示`RegisterTypes`UWP 上的方法：

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

在此示例中，`Logger`具体类型注册通过对其接口类型，映射和`PhotoPicker`类型还注册通过接口映射。

`PhotoPicker` Android 平台上的构造函数是稍微复杂一些，因为它需要`Context`除了参数`ILogger`参数：

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

下面的示例演示`RegisterTypes`Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

在此示例中，`App.RegisterTypeWithParameters`方法注册`PhotoPicker`与依赖关系注入容器。 此注册方法可确保`MainActivity`实例将作为注入`Context`自变量，并且`Logger`类型将作为注入`ILogger`参数。

当用户导航到照片选择页上，并选择以选择照片，`OnSelectPhotoButtonClicked`执行处理程序：

```csharp
async void OnSelectPhotoButtonClicked(object sender, EventArgs e)
{
    ...
    var photoPickerService = DependencyService.Resolve<IPhotoPicker>();
    var stream = await photoPickerService.GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }
    ...
}
```

当[ `DependencyService.Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*)调用方法，将调用的依赖关系解析方法要解决`PhotoPicker`类型从依赖关系注入容器，这还将解决并注入`Logger`类型到`PhotoPicker`构造函数。

> [!NOTE]
> [ `Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*)解析通过应用程序的依赖关系注入容器中的类型时，必须使用方法[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)。

## <a name="related-links"></a>相关链接

- [使用容器 （示例） 的依赖项解析](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [实现视频播放器](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [调用效果中的事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [从图片库中选取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
