---
title: 平台特定信息
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 此文章介绍了如何使用和创建平台特定信息。
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: 04cbdaac50b0ea77659d7c495dcd1a9e6d43335c
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926986"
---
# <a name="platform-specifics"></a>平台特定信息

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

_平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。_

使用特定于平台的通过 XAML，或通过 fluent 代码 API 的过程如下所示：

1. 添加`xmlns`声明或`using`指令[ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration)命名空间。
1. 添加`xmlns`声明或`using`指令包含特定于平台的功能的命名空间：
    1. 在 iOS 上，这是[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间。
    1. 在 Android 上，这是[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间。 这是 Android AppCompat [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)命名空间。
    1. 在通用 Windows 平台上，这是[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间。
1. 将特定于平台的应用从 XAML，或通过从代码`On<T>`fluent API。 值`T`可以是[ `iOS` ](xref:Xamarin.Forms.PlatformConfiguration.iOS)， [ `Android` ](xref:Xamarin.Forms.PlatformConfiguration.Android)，或[ `Windows` ](xref:Xamarin.Forms.PlatformConfiguration.Windows)类型从[ `Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration)命名空间。

> [!NOTE]
> 请注意，尝试使用特定于平台的是不可用的平台上不会导致错误。 相反，该代码将执行而无需特定于平台的应用。

通过使用平台特定信息`On<T>`fluent 代码 API 返回[ `IPlatformElementConfiguration` ](xref:Xamarin.Forms.IPlatformElementConfiguration`2)对象。 这允许多个平台特定信息以与方法级联在同一对象上调用。

Xamarin.Forms 提供的平台特定信息的详细信息，请参阅[iOS 平台特定信息](~/xamarin-forms/platform/ios/index.md)， [Android 平台特定信息](~/xamarin-forms/platform/android/index.md)，和[Windows 平台特定信息](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>创建平台特定信息

供应商可以使用效果创建其自己的平台特定信息。 影响提供特定功能，然后通过特定于平台的公开。 结果是通过 XAML，并通过 fluent 代码 API 可以更轻松地使用的效果。

创建平台特定的过程如下所示：

1. 实现的特定功能的影响方式。 有关详细信息，请参阅[创建一种效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。
1. 创建一个特定于平台的类将公开效果。 有关详细信息，请参阅[创建特定于平台的类](#creating-a-platform-specific-class)。
1. 特定于平台的类中实现以允许特定于平台的使用通过 XAML 附加的属性。 有关详细信息，请参阅[将附加属性添加](#adding-an-attached-property)。
1. 在特定于平台的类中，实现了扩展方法，以允许特定于平台的使用通过 fluent 代码 API。 有关详细信息，请参阅[添加扩展方法](#adding-extension-methods)。
1. 修改效果实现，以便当在已为的效果，在同一平台上调用特定于平台的效果才适用。 有关详细信息，请参阅[创建效果](#creating-the-effect)。

公开平台特定的效果的结果是，效果可以更轻松地使用通过 XAML 和 fluent 代码 API。

> [!NOTE]
> 它是按设想供应商将使用此技术来创建其自己平台特定信息，以便于用户的消耗。 尽管用户可以选择创建自己的平台特定信息，但应该指出，它需要比创建和使用效果更多代码。

[示例应用程序](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)演示`Shadow`添加到显示的文本阴影的平台特定[ `Label` ](xref:Xamarin.Forms.Label)控件：

![](images/screenshots.png "隐藏特定于平台的")

[示例应用程序](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)实现`Shadow`特定于平台的每个平台，为便于理解上。 但是，除了每个特定于平台的效果实现中，卷影类的实现是为每个平台大致相同。 因此，本指南重点介绍卷影类和关联的影响单一平台的实现。

有关效果的详细信息，请参阅[使用效果自定义控件](~/xamarin-forms/app-fundamentals/effects/index.md)。

### <a name="creating-a-platform-specific-class"></a>创建特定于平台的类

作为创建特定于平台的`public static`类：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

以下各节讨论的实现`Shadow`特定于平台和相关联的效果。

#### <a name="adding-an-attached-property"></a>添加附加的属性

必须将附加的属性添加到`Shadow`特定于平台的允许使用通过 XAML:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

`IsShadowed`附加的属性用于添加`MyCompany.LabelShadowEffect`影响，并将其删除从该控件的`Shadow`类附加到。 此附加属性寄存器`OnIsShadowedPropertyChanged`属性的值发生更改时将执行的方法。 反过来，此方法调用`AttachEffect`或`DetachEffect`方法来添加或删除影响值的基础`IsShadowed`附加属性。 添加或从控件中移除通过修改控件的效果[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。

> [!NOTE]
> 请注意，被指定为解析组名称和指定效果实现的唯一标识符的串联的值解析效果。 有关详细信息，请参阅[创建一种效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。

有关附加属性的详细信息，请参阅[附加属性](~/xamarin-forms/xaml/attached-properties.md)。

#### <a name="adding-extension-methods"></a>添加扩展方法

扩展方法必须添加到`Shadow`特定于平台的允许通过 fluent 代码 API 的使用：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

`IsShadowed`并`SetIsShadowed`扩展方法调用 get 和 set 访问器为`IsShadowed`分别的附加属性。 每个扩展方法对`IPlatformElementConfiguration<iOS, FormsElement>`类型，指定可以在特定于平台的上调用[ `Label` ](xref:Xamarin.Forms.Label)从 iOS 的实例。

#### <a name="creating-the-effect"></a>创建效果

`Shadow`特定于平台的添加`MyCompany.LabelShadowEffect`到[ `Label` ](xref:Xamarin.Forms.Label)，并将其删除。 下面的代码示例演示`LabelShadowEffect`实现针对 iOS 项目：

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`UpdateShadow`方法设置`Control.Layer`属性，以创建阴影，提供`IsShadowed`附加的属性设置为`true`，并且假定`Shadow`已在同一平台上调用特定于平台的为实现的效果。 与执行此检查`OnThisPlatform`方法。

如果`Shadow.IsShadowed`附加属性值更改在运行时，需要通过删除卷影响应该效果。 因此，重写的版本`OnElementPropertyChanged`方法用于通过调用响应可绑定的属性更改`UpdateShadow`方法。

有关创建效果的详细信息，请参阅[创建一种效果](~/xamarin-forms/app-fundamentals/effects/creating.md)并[传递作为附加属性的效果参数](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md)。

### <a name="consuming-the-platform-specific"></a>使用特定于平台的

`Shadow`设置在 XAML 中使用特定于平台`Shadow.IsShadowed`附加属性设置为`boolean`值：

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [ShadowPlatformSpecific （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [iOS 平台特定信息](~/xamarin-forms/platform/ios/index.md)
- [Android 平台特定信息](~/xamarin-forms/platform/android/index.md)
- [Windows 平台特定信息](~/xamarin-forms/platform/windows/index.md)
- [自定义控件起的作用](~/xamarin-forms/app-fundamentals/effects/index.md)
- [附加属性](~/xamarin-forms/xaml/attached-properties.md)
- [PlatformConfiguration API](xref:Xamarin.Forms.PlatformConfiguration)
