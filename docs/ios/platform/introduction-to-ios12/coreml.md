---
title: Core ML 2 在 Xamarin.iOS
description: 本文档介绍 iOS 12 的一部分的 Core ML 提供的更新。 具体而言，它会查看新的批次预测 API 与相关联的性能改进。
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/15/2018
ms.openlocfilehash: 50d59f0b6ff2133c5870d84a1d740547768116e0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61398840"
---
# <a name="core-ml-2-in-xamarinios"></a>Core ML 2 在 Xamarin.iOS

Core 机器学习是机器学习技术可在 iOS、 macOS、 tvOS 和 watchOS 上。 它允许应用基于机器学习模型进行预测。

在 iOS 12 中，核心机器学习包括批处理 API。 此 API 使 Core ML 更高效，并提供模型用于生成一系列预测的方案中的性能改进。

## <a name="sample-app-marshabitatcoremltimer"></a>应用程序示例：MarsHabitatCoreMLTimer

为了演示使用 Core ML 批预测，看一看[MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer)示例应用程序。 此示例使用基于 Core ML 训练模型来预测上 Mars，生成 habitat 的成本基于各种输入： solar 面板、 greenhouses，数和占地英亩数的数字。

本文档中的代码段来自于本示例。

## <a name="generate-sample-data"></a>生成示例数据

在中`ViewController`，示例应用`ViewDidLoad`方法调用`LoadMLModel`，这会加载包含的核心机器学习模型：

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

然后，示例应用创建 100,000`MarsHabitatPricerInput`对象作为输入使用针对顺序 Core ML 预测。 每个生成的示例具有的几个 solar 面板、 greenhouses，数和占地英亩数设置的随机值：

```csharp
async void CreateInputs(int num)
{
    // ...
    Random r = new Random();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            double solarPanels = r.NextDouble() * MaxSolarPanels;
            double greenHouses = r.NextDouble() * MaxGreenHouses;
            double acres = r.NextDouble() * MaxAcres;
            inputs[i] = new MarsHabitatPricerInput(solarPanels, greenHouses, acres);
        }
    });
    // ...
}
```

点击应用的三个按钮的任何执行两个序列的预测： 一个使用`for`循环，并使用新的批的另一种`GetPredictions`iOS 12 中引入的方法：

```csharp
async void RunTest(int num)
{
    // ...
    await FetchNonBatchResults(num);
    // ...
    await FetchBatchResults(num);
    // ...
}
```

## <a name="for-loop"></a>for 循环

`for`测试循环版本天真循环访问指定数目的输入，调用[ `GetPrediction` ](xref:CoreML.MLModel.GetPrediction*)为每个和放弃结果。 该方法时间进行预测所需的时间长度：

```csharp
async Task FetchNonBatchResults(int num)
{
    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            IMLFeatureProvider output = model.GetPrediction(inputs[i], out NSError error);
        }
    });
    stopWatch.Stop();
    nonBatchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="getpredictions-new-batch-api"></a>GetPredictions （新批处理 API）

创建测试批次版本`MLArrayBatchProvider`输入数组中的对象 (由于这是所需的输入的参数`GetPredictions`方法)，创建 [`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
对象，可防止预测计算从受限于 CPU，并使用`GetPredictions`API 来提取再次放弃结果的预测：

```csharp
async Task FetchBatchResults(int num)
{
    var batch = new MLArrayBatchProvider(inputs.Take(num).ToArray());
    var options = new MLPredictionOptions()
    {
        UsesCpuOnly = false
    };

    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        model.GetPredictions(batch, options, out NSError error);
    });
    stopWatch.Stop();
    batchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="results"></a>结果

在模拟器和设备，`GetPredictions`比基于循环的核心机器学习预测更快地完成。

## <a name="related-links"></a>相关链接

- [示例应用程序 – MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer)
- [什么是核心机器学习，第 1 部分 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/708/)
- [什么是核心机器学习，第 2 部分 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/709/)
- [在 Xamarin.iOS Core ML 简介](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [Core ML (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [使用 Core ML 模型](https://developer.apple.com/machine-learning/build-run-models/)
