---
title: Renderscript 简介
description: 本指南介绍 Renderscript 并说明如何使用该目标 API 级别 17 或更高版本的内部函数 Renderscript 中的 API 的 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 8364310d23739c05ff97ea8aa8fa4c56f89ea40c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670722"
---
# <a name="an-introduction-to-renderscript"></a>Renderscript 简介

_本指南介绍 Renderscript 并说明如何使用该目标 API 级别 17 或更高版本的内部函数 Renderscript 中的 API 的 Xamarin.Android 应用程序。_

## <a name="overview"></a>概述

Renderscript 是一个编程框架，由 Google 创建以便改进需要大量计算资源的 Android 应用程序的性能。 它是基于 API 的较低级别、 高性能[C99](https://en.wikipedia.org/wiki/C99)。 因为它是低级别的 Cpu、 Gpu 或 Dsp 将运行的 API，Renderscript 是非常适用于 Android 应用，可能需要执行以下任一项：

* 图形
* 图像处理
* 加密
* 信号处理
* 数学例程

将使用 Renderscript`clang`并编译到 LLVM 字节代码捆绑到 apk 进行签名的脚本。 第一次运行应用时，LLVM 字节代码将编译为机器代码，以在设备上的处理器。 此体系结构允许的 Android 应用程序本身无需编写它为每个处理器在设备上自行利用的机器代码，而无需开发人员的优势。

有两个组件 Renderscript 例程：

1. **Renderscript 运行时**&ndash;这是负责执行 Renderscript 的本机 Api。 这包括任何 Renderscripts 编写的应用程序。

2. **从 Android 框架托管包装**&ndash;托管允许的 Android 应用程序控制并 Renderscript 运行时和脚本与之交互的类。 除了用于控制 Renderscript 运行时提供的框架类，Android 工具链将检查 Renderscript 源代码和生成 Android 应用程序使用的托管的包装类。

下图说明了这些组件之间的关系：

![说明 Android 框架如何与 Renderscript 运行时交互的关系图](renderscript-images/renderscript-01.png)

有三个 Android 应用程序中使用 Renderscripts 的重要概念：

1. **上下文**&ndash;一种托管的 Android SDK，可将资源分配到 Renderscript 并允许 Android 应用程序传递和接收来自 Renderscript 数据提供的 API。

2. **一个_计算内核_** &ndash;也称为_根内核_或者_内核_，则此执行工作的例程。 内核是非常类似于 C 函数;它是将在分配的内存中的所有数据上运行的可并行化例程。

3. **分配的内存**&ndash;数据传递到和从通过内核_[分配](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_。 内核可能有一个输入和/或一个输出分配。

[Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)命名空间包含用来与 Renderscript 运行时交互的类。 具体而言， [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)类将管理的生命周期和 Renderscript 引擎的资源。 一个或多个必须进行初始化的 Android 应用 [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
对象。 分配是托管的 API，它负责分配和访问的 Android 应用和 Renderscript 运行时之间共享的内存。 通常情况下，为输入，创建一个资源分配，并可以选择另一个分配创建用于保存该内核的输出。 Renderscript 运行时引擎和关联的托管的包装类将管理对由分配的内存的访问，则无需执行任何额外的工作应用的 Android 应用程序开发人员。

分配将包含一个或多个[Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/)。
元素是一种特殊的类型的描述中每个分配的数据。
元素类型分配必须与输入元素的类型匹配的输出。 在执行时，将循环并行情况下，输入分配的每个元素并将结果写入到输出 Renderscript 分配。 有两种类型的元素：

- **简单类型**&ndash;从概念上讲这是为 C 数据类型，相同`float`或`char`。

- **复杂类型**&ndash;此类型是类似于 C `struct`。

Renderscript 引擎将执行运行时检查以确保每个分配中的元素与所需的内核兼容。 如果在分配的元素的数据类型与内核的期望的数据类型不匹配，则将引发异常。

所有 Renderscript 内核将都包装的类型，是的后代 [`Android.Renderscripts.Script`](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)
类的新实例。 `Script`类用于为 Renderscript 设置参数，设置相应`Allocations`，并运行 Renderscript。 有两个`Script`Android SDK 中的子类：


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; 一些常见 Renderscript 任务被捆绑在 Android SDK 中并且都可以访问的子类[ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/)类。 开发人员需要任何额外步骤以在其应用程序中使用这些脚本，因为它们已提供，没有必要。

- **`ScriptC_XXXXX`** &ndash; 也称为_用户脚本_，这些是由开发人员编写和打包在 APK 中的脚本。 在编译时，Android 工具链将生成会允许脚本以在 Android 应用中使用的托管的包装类。
  这些生成的类的名称是带有前缀的 Renderscript 文件的名称`ScriptC_`。 编写并结合用户脚本不正式支持 xamarin.android 和超出了本指南的范围。

这两种类型，仅`StringIntrinsic`Xamarin.Android 支持。 本指南介绍了如何在 Xamarin.Android 应用程序中使用内部函数的脚本。

## <a name="requirements"></a>要求

本指南适用于 Xamarin.Android 应用程序的目标 API 级别 17 或更高版本。 利用_用户脚本_本指南中未涉及。

[Xamarin.Android V8 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/)backports 内部 Renderscript API 针对面向较旧版本的 Android SDK 的应用。 将此包添加到 Xamarin.Android 项目应允许应用程序面向旧版本的 Android SDK，可利用内部函数的脚本。

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>在 Xamarin.Android 中使用内部函数 Renderscripts

内部函数的脚本是执行密集型计算任务进行少量的额外代码的好办法。 它们已进行手动经过优化，可提供一大部分的设备上获得最佳性能。
不常见的内部函数的脚本来运行 10 倍快于托管代码和 2-3 倍后比自定义的 C 实现的。 内部函数的脚本的涵盖了许多典型处理方案。 内部函数的脚本此列表描述了在 Xamarin.Android 中的当前脚本：

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash;将 RGB 将转换为 RGBA 使用 3D 查找表。 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh 性能 Renderscript Api 向[BLAS](http://www.netlib.org/blas/)。 BLAS （基本线性代数子程序） 是提供用于执行基本的矢量和矩阵操作的标准构建基块的例程。 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash;将两个分配融合在一起。

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash;适用于分配的高斯模糊。

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash;适用于分配 （即更改色彩调整 hue） 颜色矩阵。

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash;适用于分配的 3 x 3 个颜色矩阵。

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash;适用于分配的 5 x 5 颜色矩阵。

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash;内部直方图筛选器。

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash;缓冲区应用于每个通道查找表。

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash;执行调整大小的 2D 分配的脚本。

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash;将 YUV 缓冲区转换为 RGB。

请有关每个内部函数的脚本的详细信息，参阅 API 文档。

接下来介绍了在 Android 应用程序中使用 Renderscript 的基本步骤。

**创建 Renderscript 上下文** &ndash; [`Renderscript`](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)
类是一个托管的包装 Renderscript 上下文并将控件初始化，资源管理和清理。 使用创建 Renderscript 对象`RenderScript.Create`工厂方法，它使用 Android 上下文 （如活动） 作为参数。 以下代码行演示如何初始化 Renderscript 上下文：

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**创建分配**&ndash;具体取决于内部函数的脚本，可能有必要创建一个或两个`Allocation`s。 的 [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
类具有多个工厂方法，用于帮助进行实例化的内部函数的分配。 例如，下面的代码段演示如何创建位图的分配。

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

通常情况下，它将需要创建`Allocation`以保存脚本的输出数据。 此以下代码片段演示如何使用`Allocation.CreateTyped`帮助器实例化第二个`Allocation`，相同的类型与原始：

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**实例化脚本包装**&ndash;的每个内部函数的脚本包装器类应具有帮助器方法 (通常称为`Create`) 实例化该脚本的包装对象。 以下代码片段示范了如何实例化`ScriptIntrinsicBlur`模糊对象。 `Element.U8_4`帮助器方法将创建一个描述是 8 位无符号整数值，适用于保存的数据的 4 个字段的数据类型的元素`Bitmap`对象：

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**分配 Allocation(s)、 设置参数和运行脚本** &ndash; `Script`类提供了`ForEach`方法以实际运行 Renderscript。 此方法将遍历每个`Element`在`Allocation`保存输入的数据。 在某些情况下，可能有必要提供`Allocation`用于保存输出。
`ForEach` 将覆盖输出的内容分配。 若要执行与前面步骤中的代码段，此示例演示如何分配输入的分配，设置参数，并最后运行脚本 (将结果复制到输出分配):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

你可能想要签出[模糊 Renderscript 映像](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)方案，它是如何在 Xamarin.Android 中使用的内部函数的脚本的完整示例。

## <a name="summary"></a>总结

本指南介绍了 Renderscript 以及如何在 Xamarin.Android 应用程序中使用它。 它简要介绍了 Renderscript 是什么及其工作原理的 Android 应用程序中。 它介绍了一些关键组件，Renderscript 和之间的差异_用户脚本_并_内部脚本_。 最后，本指南介绍使用 Xamarin.Android 应用程序中的内部函数的脚本中的步骤。



## <a name="related-links"></a>相关链接

- [Android.Renderscripts 命名空间](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [模糊 Renderscript 的映像](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [教程：开始使用 Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
