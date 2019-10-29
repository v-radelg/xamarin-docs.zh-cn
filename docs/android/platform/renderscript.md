---
title: Renderscript 简介
description: 本指南介绍了 Renderscript，并说明了如何在面向 API 级别17或更高版本的 Xamarin Android 应用程序中使用内部 Renderscript API。
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019800"
---
# <a name="an-introduction-to-renderscript"></a>Renderscript 简介

_本指南介绍了 Renderscript，并说明了如何在面向 API 级别17或更高版本的 Xamarin Android 应用程序中使用内部 Renderscript API。_

## <a name="overview"></a>概述

Renderscript 是 Google 创建的编程框架，目的在于提高需要大量计算资源的 Android 应用程序的性能。 这是一个基于[C99](https://en.wikipedia.org/wiki/C99)的低级别、高性能 API。 由于它是将在 Cpu、Gpu 或 Dsp 上运行的低级别 API，Renderscript 非常适合用于可能需要执行以下任一操作的 Android 应用：

- 图形
- 图像处理
- 加密
- 信号处理
- 数学例程

Renderscript 将使用 `clang`，并将脚本编译为绑定到 APK 的 LLVM 字节代码。 首次运行应用时，LLVM 字节代码将被编译为设备上的处理器的机器代码。 此体系结构允许 Android 应用程序利用机器代码的优点，而开发人员无需为设备自身的每个处理器编写该代码。

Renderscript 例程有两个组件：

1. **Renderscript 运行时**&ndash; 这是负责执行 Renderscript 的本机 api。 这包括为应用程序编写的任何 Renderscripts。

2. **Android Framework 中的托管包装**&ndash; 托管类，它们允许 Android 应用程序控制 Renderscript 运行时和脚本并与之交互。 除了 framework 提供的用于控制 Renderscript 运行时的类，Android 工具链还会检查 Renderscript 源代码，并生成托管包装器类以供 Android 应用程序使用。

下图说明了这些组件之间的关系：

![阐释 Android Framework 如何与 Renderscript 运行时进行交互的关系图](renderscript-images/renderscript-01.png)

在 Android 应用程序中使用 Renderscripts 有三个重要概念：

1. **上下文**&ndash; 由 Android SDK 提供的托管 API，该 API 将资源分配给 Renderscript，并允许 Android 应用程序通过 Renderscript 传递和接收数据。

2. **_计算内核_** &ndash; 也称为_根内核_或_内核_，这是执行工作的例程。 内核非常类似于 C 函数;它是一个可并行化例程，将对分配的内存中的所有数据运行。

3. **分配的内存**&ndash; 通过 _[分配](xref:Android.Renderscripts.Allocation)_ 将数据传入和向内核传入。 一个内核可以有一个输入和/或一个输出分配。

[Renderscripts](xref:Android.Renderscripts)命名空间包含用于与 Renderscript 运行时进行交互的类。 具体而言， [`Renderscript`](xref:Android.Renderscripts.RenderScript)类将管理 Renderscript 引擎的生命周期和资源。 Android 应用必须初始化一个或多个[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
对象. 分配是一个托管 API，负责分配和访问 Android 应用与 Renderscript 运行时之间共享的内存。 通常，为输入创建一个分配，并根据需要创建另一个分配来保存内核的输出。 Renderscript 运行时引擎和关联的托管包装器类将管理对分配所持有的内存的访问，无需 Android 应用开发人员执行任何额外的工作。

分配将包含一个或多个[Renderscripts](xref:Android.Renderscripts.Element)。
元素是一种专用类型，用于描述每个分配中的数据。
输出分配的元素类型必须匹配输入元素的类型。 执行时，Renderscript 将并行迭代输入分配中的每个元素，并将结果写入到输出分配。 有两种类型的元素：

- **简单类型**&ndash; 从概念上讲，这与 C 数据类型相同，`float` 或 `char`。

- 此类型 &ndash;**复杂类型**类似于 C `struct`。

Renderscript 引擎将执行运行时检查，以确保每个分配中的元素都与内核所需的元素兼容。 如果分配中的元素的数据类型与内核期望的数据类型不匹配，则将引发异常。

所有 Renderscript 内核都将按作为[`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)子代的类型包装
类的新实例。 `Script` 类用于设置 Renderscript 的参数、设置相应的 `Allocations`并运行 Renderscript。 Android SDK 有两个 `Script` 子类：

- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; 在 Android SDK 中捆绑了一些更常见的 Renderscript 任务，这些任务可通过[ScriptIntrinsic](xref:Android.Renderscripts.ScriptIntrinsic)类的子类访问。 开发人员无需执行任何额外的步骤即可在应用程序中使用这些脚本，因为已提供这些脚本。

- **`ScriptC_XXXXX`** &ndash; 也称为_用户脚本_，这些脚本是由开发人员编写并打包在 APK 中的脚本。 在编译时，Android 工具链将生成托管包装器类，这些类将允许在 Android 应用中使用这些脚本。
  这些生成的类的名称是 Renderscript 文件的名称，以 `ScriptC_`为前缀。 Xamarin 和本指南的范围之外，不支持编写和并入用户脚本。

对于这两种类型，Xamarin 仅支持 `StringIntrinsic`。 本指南将讨论如何在 Xamarin Android 应用程序中使用内部脚本。

## <a name="requirements"></a>要求

本指南适用于面向 API 级别17或更高版本的 Xamarin Android 应用程序。 本指南未介绍如何使用_用户脚本_。

[Xamarin V8 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/)Precise-backports 内部 Renderscript API，适用于面向 Android SDK 的旧版本的应用程序。 如果将此包添加到 Xamarin Android 项目，则应允许面向 Android SDK 较早版本的应用程序利用内部脚本。

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>在 Xamarin 中使用内部 Renderscripts

内部脚本是使用最少数量的附加代码执行密集型计算任务的好方法。 它们已经过优化，可以在大量设备上提供最佳性能。
与托管代码相比，内部脚本的运行速度快10倍，而在自定义 C 实现后，这种情况并不常见。 内部脚本涵盖了许多典型处理方案。 此内部脚本列表描述了 Xamarin 中的当前脚本：

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT) &ndash; 使用三维查找表将 RGB 转换为 RGBA。 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh 性能 Renderscript Api 到[BLAS](http://www.netlib.org/blas/)。 BLAS （基本线性代数 Subprograms）是提供用于执行基本矢量和矩阵操作的标准构建基块的例程。 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend) &ndash; 将两个分配组合在一起。

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur) &ndash; 将高斯模糊应用于分配。

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix) &ndash; 将颜色矩阵应用到分配（即更改 colours、调整色调）。

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3) &ndash; 向分配应用3x3 颜色矩阵。

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5) &ndash; 将5x5 颜色矩阵应用到分配。

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram) &ndash; 内部直方图筛选器。

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT) &ndash; 将每通道查找表应用于缓冲区。

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize) &ndash; 脚本来执行二维分配的大小调整。

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB) &ndash; 将 YUV 缓冲区转换为 RGB。

有关每个内部脚本的详细信息，请参阅 API 文档。

下面介绍了如何在 Android 应用程序中使用 Renderscript 的基本步骤。

&ndash; [`Renderscript`](xref:Android.Renderscripts.RenderScript) **创建 Renderscript 上下文**
类是围绕 Renderscript 上下文的托管包装，它将控制初始化、资源管理和清理。 Renderscript 对象是使用 `RenderScript.Create` 工厂方法创建的，该方法采用 Android 上下文（例如活动）作为参数。 下面的代码行演示如何初始化 Renderscript 上下文：

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**创建分配**&ndash; 具体取决于内部脚本，可能需要创建一个或两个 `Allocation`。 [`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
类具有多个工厂方法，可帮助为内部函数实例化分配。 例如，下面的代码段演示如何为位图创建分配。

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

通常，需要创建一个 `Allocation` 来保存脚本的输出数据。 以下代码片段演示了如何使用 `Allocation.CreateTyped` 帮助器实例化与原始类型相同的第二个 `Allocation`：

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**实例化脚本包装**&ndash; 每个内部脚本包装类应包含 helper 方法（通常称为 `Create`），以便为该脚本实例化包装对象。 下面的代码片段举例说明了如何实例化 `ScriptIntrinsicBlur` 模糊对象。 `Element.U8_4` helper 方法将创建一个元素，该元素描述一个数据类型，该数据类型为8位无符号整数值的4个字段，适用于保存 `Bitmap` 对象的数据：

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

`Script` 类中**分配分配（s）、设置参数、& 运行脚本**&ndash; 提供了一种用于实际运行 Renderscript 的 `ForEach` 方法。 此方法将遍历包含输入数据的 `Allocation` 中的每个 `Element`。 在某些情况下，可能需要提供保存输出的 `Allocation`。
`ForEach` 将覆盖输出分配的内容。 为了附带前面步骤中的代码片段，此示例演示了如何分配输入分配、设置参数，最后运行脚本（将结果复制到输出分配）：

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

你可能想要使用 Renderscript 食谱来查看对[图像进行模糊处理](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)，这是如何在 Xamarin 中使用内部脚本的完整示例。

## <a name="summary"></a>总结

本指南介绍了 Renderscript 以及如何在 Xamarin Android 应用程序中使用它。 它简要讨论了什么是 Renderscript 以及它在 Android 应用程序中的工作方式。 它介绍了 Renderscript 中的一些关键组件以及_用户脚本_与_内部脚本_之间的差异。 最后，本指南介绍了在 Xamarin Android 应用程序中使用内部脚本的步骤。

## <a name="related-links"></a>相关链接

- [Renderscripts 命名空间](xref:Android.Renderscripts)
- [使用 Renderscript 对图像进行模糊处理](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [教程：入门与 Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
