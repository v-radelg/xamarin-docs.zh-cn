---
title: 部署和测试 Xamarin.iOS 应用
description: 本文档链接到的各种指南介绍了有关部署和测试 Xamarin.iOS 应用程序的主题。 例如，应用分发、.ipa 文件、预配、无线部署、TestFlight 和调试。
ms.prod: xamarin
ms.assetid: 2DBF3BF9-79E7-4E24-AF26-E34C972B0169
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 8ce601bff478cfc75d209b0d3e6ec3f6a48dbeee
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288969"
---
# <a name="deploying-and-testing-xamarinios-apps"></a>部署和测试 Xamarin.iOS 应用

此部分介绍用于测试应用程序以及分发方式的主题。 本部分的主题包括用于调试的工具、如何部署给测试员以及如何将应用程序发布到 App Store 等内容。

## <a name="app-distributioniosdeploy-testapp-distributionindexmd"></a>[应用分发](~/ios/deploy-test/app-distribution/index.md)

本文介绍如何配置、生成以及发布 Xamarin.iOS 应用程序，用于通过各种方式进行发布，其中包括：

- [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [In-House（企业）分发](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Ad Hoc 分发](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)

## <a name="ipa-deploymentiosdeploy-testapp-distributionipa-supportmd"></a>[IPA 部署](~/ios/deploy-test/app-distribution/ipa-support.md)

Ad-Hoc 和企业部署允许开发人员创建可针对测试或向公司内部用户分发的包。 本文档介绍如何创建可使用 iTunes 同步到 iOS 设备的 IPA。

## <a name="provisioningprovisioningindexmd"></a>[预配](provisioning/index.md)

这套指南涵盖了代码签名和预配基础知识（如使用属性列表），以及如何为应用程序服务预配应用。 

## <a name="wireless-deploymentwireless-deploymentmd"></a>[无线部署](wireless-deployment.md)

 Xcode 9 引入了一个通过网络部署到 iOS 设备或 Apple TV 的选项，而不是在每次想部署和调试应用时都必须使用线缆硬连接设备。 此功能当前处于预览状态。

## <a name="testflightiosdeploy-testtestflightmd"></a>[TestFlight](~/ios/deploy-test/testflight.md)

TestFlight 现在归 Apple 所有，是进行 Xamarin.iOS 应用的 Beta 测试的主要方法。 本文将指导你完成 TestFlight 过程的所有步骤，范围包括从上传应用到使用 iTunes Connect。

## <a name="debugging-in-xamariniosiosdeploy-testdebugging-in-xamarin-iosmd"></a>[在 Xamarin.iOS 中调试](~/ios/deploy-test/debugging-in-xamarin-ios.md)

Visual Studio 和 Visual Studio for Mac IDE 都支持在 iOS 模拟器和 iOS 设备中调试 Xamarin.iOS 应用程序。 本文介绍如何使用调试器以及如何配置其支持的各种选项。

## <a name="touchunitiosdeploy-testtouchunitmd"></a>[Touch.Unit](~/ios/deploy-test/touch.unit.md)

本文档介绍如何为 Xamarin.iOS 项目创建单元测试。
可使用 Touch.Unit 框架实现对 Xamarin.iOS 的单元测试，该框架同时包含 iOS 测试运行程序以及 [NUnitLite](http://www.nunitlite.com/) 框架的修改版，可提供一组熟悉的 API 用于编写单元测试。

## <a name="using-instruments-to-detect-native-leaks-using-markheapiosdeploy-testusing-instruments-to-detect-native-leaks-using-markheapmd"></a>[使用 Instruments 检测利用 MarkHeap 的本机泄漏](~/ios/deploy-test/using-instruments-to-detect-native-leaks-using-markheap.md)

本文介绍如何在任何 iOS 设备和 Xamarin.iOS 应用程序中使用 Instruments。 它还介绍了如何在模拟器中分析应用程序。

## <a name="walkthrough---using-apples-instrument-tooliosdeploy-testwalkthrough-apples-instrumentmd"></a>[演练 - 使用 Apple 的 Instruments 工具](~/ios/deploy-test/walkthrough-apples-instrument.md)

本文介绍如何使用 Apple 的 Instruments 工具诊断通过 Xamarin 生成的 iOS 应用程序的内存问题。 它演示了如何启动 Instruments、拍摄堆快照和分析内存增长情况。 此外，还演示了如何使用 Instruments 显示和确定造成内存问题的确切代码行。

## <a name="linking-on-ioslinkermd"></a>[在 iOS 上链接](linker.md)

说明链接器如何工作以确保尽可能地缩小应用程序包，以及如何修改其设置和用法。

## <a name="xamarinios-performanceperformancemd"></a>[Xamarin.iOS 性能](performance.md)

可通过多种方法提高使用 Xamarin.iOS 生成的应用程序的性能。 这些方法共同可以极大地降低由 CPU 执行的工作量和应用程序占用的内存量。

## <a name="mtouchmtouchmd"></a>[mtouch](mtouch.md)

有关 mtouch.exe 的注意事项和信息，该命令行工具用于将项目内置到 iOS 可使用的应用程序中。

## <a name="ios-build-mechanicsios-build-mechanicsmd"></a>[iOS 生成机制](ios-build-mechanics.md)

本指南介绍如何安排应用的时间，以及如何使用可用于所有构建配置的更快构建的方法。
