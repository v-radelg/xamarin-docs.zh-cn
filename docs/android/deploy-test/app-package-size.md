---
title: 应用程序包大小
description: 本文将讨论 Xamarin.Android 应用程序包的组成部分，以及可用于在开发的调试和发布阶段进行高效包部署的相关策略。
ms.prod: xamarin
ms.assetid: 8D70CDDD-3D3C-9949-8045-AB8F93D18E74
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: ff1bc56ab1cf02e9e5354da94bebd0661da34bc5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028162"
---
# <a name="application-package-size"></a>应用程序包大小

本文将讨论 Xamarin.Android 应用程序包的组成部分，以及可用于在开发的调试和发布阶段进行高效包部署的相关策略。 

## <a name="overview"></a>概述

Xamarin.Android 可使用各种机制来最大程度地减小包的大小，同时保持高效调试和发布部署过程。 在本文中，我们将了解 Xamarin.Android 版本和调试部署工作流，并了解 Xamarin.Android 平台如何确保我们生成和发布小的应用程序包。

## <a name="release-packages"></a>版本包

若要提供完全包含的应用程序，包必须包含应用程序、关联库、内容、Mono 运行时以及所需的基类库 (BCL) 程序集。 例如，如果我们使用默认的“Hello World”模板，则完整的包生成内容将如下所示：

[![链接器之前的包大小](app-package-size-images/hello-world-package-size-before-linker.png)](app-package-size-images/hello-world-package-size-before-linker.png#lightbox)

15.8 MB - 比我们所需的下载大小要大。 问题在于 BCL 库，因为它们包含 mscorlib、系统和 Mono.Android，以此提供大量的必需组件来运行应用程序。 但是，它们还在应用程序中提供了可能不使用的功能，因此最好将这些组件排除在外。

当我们构建用于分发的应用程序时，会执行一个称为“链接”的过程来检查应用，并移除不直接使用的任何代码。 此过程类似于[垃圾回收](~/android/internals/garbage-collection.md)为堆分配内存提供的功能。 但是，与在对象上操作不同，链接将在代码上运行。 例如，System.dll 中有一个完整的命名空间，用于发送和接收电子邮件，但是，如果应用程序不使用此功能，那么该代码只会浪费空间。 在 Hello World 应用程序上运行链接器之后，现在我们的包如下所示：

[![链接器之后的包大小](app-package-size-images/hello-world-package-size-after-linker.png)](app-package-size-images/hello-world-package-size-after-linker.png#lightbox)

正如我们所看到的，这会移除大量未使用的 BCL。 请注意，BCL 的最终大小要取决于实际使用的应用程序。 例如，如果我们查看一个名为 ApiDemo 的更大的示例应用程序，会看到 BCL 组件的大小增加了，因为 ApiDemo 使用的 BCL 数量要大于 Hello，World：

![链接之后的 ApiDemo 包大小](app-package-size-images/api-demo-package-size-after-linker.png)

如此处所示，应用程序包大小通常要比应用程序及其依赖项大约要大 2.9 MB。

## <a name="debug-packages"></a>调试包

针对调试版本的处理操作略有不同。 当重新部署到设备时，应用程序需要尽可能快，因此，我们优化了调试包的部署速度，而不是大小。

Android 在复制和安装包方面相对较慢，因此，我们希望包大小尽可能地小。 如上所述，最大程度降低包大小的一个可行方法就是通过链接器。 但是链接速度比较慢，而且我们通常只想要部署应用程序自上次部署以来有更改的部分。 为实现此目的，我们将应用程序从核心 Xamarin.Android 组件中分离了出来。

第一次在设备上进行调试时，我们复制了两个较大的包：共享运行时  和共享平台  。 共享运行时包含 Mono 运行时和 BCL，而共享平台包含 Android API 级别的特定程序集：

[![共享运行时包大小](app-package-size-images/shared-runtime-package-size.png)](app-package-size-images/shared-runtime-package-size.png#lightbox)

复制这些核心组件只进行一次，因为这需要相当多的时间，但允许任何后续运行的应用程序在调试模式下使用它们。 最后，我们将复制小而快速的实际应用程序：

![实际应用程序很小](app-package-size-images/hello-world-debug-application-no-link.png)

### <a name="fast-assembly-deployment"></a>快速程序集部署

“快速程序集部署”  生成选项可用于进一步减小调试安装包的大小，方法是通过在应用程序包中将程序集排除在外、直接在设备上安装程序集且只进行一次，并仅复制自上次部署以来有修改的文件。

若要启用“快速程序集部署”  ，请执行以下操作：

1. 右键单击“解决方案资源管理器”中的 Android 项目，并选择“选项”  。

2. 在“项目选项”对话框中，选择“Android 版本”  ：  

    ![项目选项 Android 版本](app-package-size-images/fastdev0.png)

3. 选中“使用共享 Mono 运行时”复选框  和“快速程序集部署”  复选框：  

    ![“打包”选项卡下选中的复选框](app-package-size-images/fastdev.png)

4. 单击“确定”  按钮，以保存所做的更改并关闭“项目选项”对话框。

下一次构建应用程序进行调试时，将直接在设备上安装程序集（如果尚未安装），并且会在设备上安装一个较小的应用程序包（不包括程序集）。 这将缩短部署应用程序更改并运行测试所需的时间。

通过持久长时间首次部署共享运行时和共享平台，每次对应用程序进行更改时，都可以快速轻松地部署新版本，以便拥有一个快速更改/部署/运行周期。

## <a name="summary"></a>总结

本文将探讨 Xamarin.Android 版本和调试配置文件打包的各个方面。 此外，我们还讨论了 Android 平台 Mono 用于在开发的调试和发布阶段进行高效包部署的相关策略。
