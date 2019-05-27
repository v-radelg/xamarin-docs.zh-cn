---
title: 使用跨平台应用中的本机类型
description: 本文介绍如何使用新的 iOS （nint，nuint，nfloat） 的统一 API 本机类型中的跨平台应用程序代码与非 iOS 设备，例如 Android 或 Windows Phone 操作系统的共享位置。
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: 847566feec2069dea924bcd2a18abf2b3ddb250b
ms.sourcegitcommit: b986460787677cf8c2fc7cc8c03f4bc60c592120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213292"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>使用跨平台应用中的本机类型

_本文介绍如何使用新的 iOS （nint，nuint，nfloat） 的统一 API 本机类型中的跨平台应用程序代码与非 iOS 设备，例如 Android 或 Windows Phone 操作系统的共享位置。_


64 类型本机类型适用于 iOS 和 Mac Api。 如果你正在编写也运行于 Android 或 Windows 的共享的代码，您需要管理的统一类型转换为常规的.NET 类型可以共享。

本文档讨论/共享常用代码的统一 api 进行互操作的不同方式。

## <a name="when-to-use-the-native-types"></a>何时使用本机类型

Xamarin.iOS 和 Xamarin.Mac Unified Api 仍包括`int`，`uint`并`float`数据类型，并将`RectangleF`，`SizeF`和`PointF`类型。 这些现有的数据类型应继续任何共享的跨平台代码中使用。 在其中对识别体系结构的类型所需的支持 Mac 或 iOS API 调用时，应仅使用新的本机数据类型。

根据共享代码的性质，可能有时间可能需要处理跨平台代码`nint`，`nuint`和`nfloat`数据类型。 例如： 处理以前使用过的矩形数据的转换库`System.Drawing.RectangleF`要共享的应用的 Xamarin.iOS 和 Xamarin.Android 版本之间的功能将需要更新，以处理在 iOS 上的本机类型。

这些更改的处理方式取决于大小和复杂性的应用程序，并已应用窗体的代码共享，我们将会看到以下各节中。

## <a name="code-sharing-considerations"></a>代码共享的注意事项

如中所述[共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)文档中，有两种主要方法为跨平台项目之间共享代码：共享项目和可移植类库。 这两种类型已被使用，将限制处理跨平台代码中的本机数据类型时，我们的选项。

### <a name="portable-class-library-projects"></a>可移植类库项目

可移植类库 (PCL)，可面向的平台，你想要支持，并使用接口来提供特定于平台的功能。

向下编译 PCL 项目类型由于`.DLL`并且它具有统一的 API 没有任何意义，将强制您要继续使用现有的数据类型 (`int`， `uint`， `float`) 在 pcl 中的源代码和类型转换为 PCL 的调用类和前端应用程序中的方法。 例如：

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>共享项目

共享资产项目类型，您可以在单独的项目，然后获取包含并编译将源代码组织到单个特定于平台的前端应用，并使用`#if`如管理所需的编译器指令特定于平台的要求。

大小和复杂性的前端移动应用程序正在使用的共享的代码，以及大小和共享代码的复杂性，需要考虑到跨平台中选择的本机数据类型支持的方法时共享的资产项目。

根据这些因素，以下类型的解决方案可能会实现使用`if __UNIFIED__ ... #endif`编译器指令来处理 Unified API 特定更改的代码。

#### <a name="using-duplicate-methods"></a>使用重复的方法

需要一个库，它执行上面给出的矩形数据转换的示例。 如果库仅包含一个或两个非常简单的方法，您可以选择创建这些方法的重复版本对于 Xamarin.iOS 和 Xamarin.Android。 例如：

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

在上面的代码，因为`CalculateArea`例程是非常简单，我们已使用条件编译，并创建单独的、 统一的 API 版本的方法。 但是，如果库包含许多例程或多个复杂的例程，此解决方案不会可行的它将演示保持的所有方法的同步的修改或 bug 修复的问题。

#### <a name="using-method-overloads"></a>使用方法重载

在这种情况下，解决方案可能需要创建，以便它们现在使用 32 位数据类型的方法的重载版本`CGRect`作为参数和/或返回值，将转换到该值`RectangleF`(了解从该转换`nfloat`到`float`是有损转换)，并调用执行实际工作的例程的原始版本。 例如：

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Call original routine to calculate area
                return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

同样，这是一个不错的解决方案，只要丢失精度不会影响应用程序的特定需求的结果。

#### <a name="using-alias-directives"></a>Using 别名指令

对于其中的精度损失是问题的区域，另一个可能的解决方案是使用`using`指令来创建包括以下代码到共享的源代码文件的顶部和任何转换的本机功能和 CoreGraphics 数据类型的别名所需`int`，`uint`或`float`值到`nint`，`nuint`或`nfloat`:

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

因此，然后将成为我们的示例代码：

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Map Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Map Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

请注意，此处我们已更改`CalculateArea`方法以返回`nfloat`而不是标准`float`。 执行此操作，以便我们不会收到编译错误尝试_隐式_转换`nfloat`的计算结果 (因为所相乘这两个值都是类型`nfloat`) 到`float`返回值。

如果编译并在非统一 API 设备上，运行代码`using nfloat = global::System.Single;`映射`nfloat`到`Single`这会隐式转换为`float`允许使用的前端应用程序，以调用`CalculateArea`没有方法修改。


#### <a name="using-type-conversions-in-the-front-end-app"></a>在前端应用程序中使用类型转换

在的前端应用程序仅进行少量的调用共享的代码库，另一种解决方案可将保持不变的库，并且执行类型强制转换中的 Xamarin.iOS 或 Xamarin.Mac 应用程序时调用现有的例程。 例如：

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

如果使用方应用程序可以实现数百个调用共享的代码库，这同样，可能不是一个不错的解决方案。

根据我们的应用程序的体系结构，我们最终可能会使用一个或多个以上的解决方案以支持本机数据类型在跨平台代码 （如有需要）。


## <a name="xamarinforms-applications"></a>Xamarin.Forms 应用程序

对于还将与一个统一的 API 应用程序共享的跨平台 Ui 使用 Xamarin.Forms 需要以下：

- 整个解决方案必须使用版本 1.3.1 （或更高版本） 的 Xamarin.Forms NuGet 包。
- 对于任何 Xamarin.iOS 自定义呈现器，使用上面介绍的解决方案的相同类型基于如何将 UI 代码已共享 （共享项目或 PCL）。

如下所示的标准的跨平台应用程序，现有的 32 位数据类型应为大多数情况下使用的任何共享的跨平台代码中。 需要在支持的体系结构注意类型系统的 Mac 或 iOS API 调用时，应仅使用新的本机数据类型。

有关更多详细信息，请参阅我们[更新现有 Xamarin.Forms 应用](https://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/)文档。

## <a name="summary"></a>总结

在本文中，我们已了解何时使用中的统一 API 应用程序和其影响跨平台的本机数据类型。 我们已经展示了几种解决方案，可以在跨平台库中的新的本机数据类型必须使用的情况下使用。 此外，我们已了解 Xamarin.Forms 跨平台应用程序中支持统一 Api 的快速指南。



## <a name="related-links"></a>相关链接

- [Unified API](~/cross-platform/macios/unified/index.md)
- [本机类型](~/cross-platform/macios/nativetypes.md)
- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [共享的代码示例](https://developer.xamarin.com/samples/mobile/SharingCode/)
