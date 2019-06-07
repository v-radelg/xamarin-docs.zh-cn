---
title: 面向 Objective-C 开发人员的 Xamarin
description: 本文档为 Objective-C 开发人员介绍 Xamarin.iOS。 它链接到介绍如何从 Objective C 转换到 C#、如何绑定 Objective-C 库以便在 C# 中使用以及如何生成跨平台移动应用程序的指南。
ms.prod: xamarin
ms.assetid: 9F3C86A3-403E-4025-99CA-99FCA86DC828
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: 949958387ab710cb671cd9874bb9062227f3e3b8
ms.sourcegitcommit: dd73477b1bccbd7ca45c1fb4e794da6b36ca163d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394675"
---
# <a name="xamarin-for-objective-c-developers"></a>面向 Objective-C 开发人员的 Xamarin

Xamarin 针对 iOS 为开发人员提供了一个途径，用于将他们的非用户界面代码移至平台不可知的 C# 中，以便其可在 C# 可用的任何位置使用，包括通过 Xamarin.Android 的 Android 和各种风格的 Windows。 但是，仅仅因为你将 C# 与 Xamarin 结合使用并不意味着你无法利用现有技能和 Objective-C 代码。 事实上，了解 Objective-C 可使你成为一个更加优秀的 Xamarin.iOS 开发人员，因为 Xamarin 提供了你熟悉并喜爱的所有本机 iOS 和 OS X 平台 API，例如 UIKit、Core Animation、Core Foundation 和 Core Graphics 等。 同时，你将获得 C# 语言的强大功能，包括 LINQ 和泛型等功能，以及用于本机应用程序的丰富的 .NET 基类库。

此外，Xamarin 允许你通过已知的绑定技术来利用现有的 Objective-C 资产。 只需在 Objective-C 中创建一个静态库并通过绑定将其公开给 C#，如下图所示：

 [![](images/01-bindings.png "Objective-C 中的静态库通过绑定公开给 C#")](images/01-bindings.png#lightbox)

这并不需要限制为非 UI 代码。 绑定还可以公开在 Objective-C 中开发的用户界面代码。

## <a name="transitioning-from-objective-c"></a>从 Objective-C 转换

你将可以在我们的文档站点上找到大量信息，以帮助简化到 Xamarin 的过渡，同时了解如何将 C# 代码与已知的内容集成。 帮助你入门的一些要点包括：

-   [面向 Objective-C 开发人员的 C# 入门](primer.md) - 针对希望转移到 Xamarin 和 C# 语言的 Objective-C 开发人员的快速入门。 
-   [演练：绑定 Objective-C 库](~/ios/platform/binding-objective-c/walkthrough.md) - 在 Xamarin.iOS 应用程序中重复使用现有 Objective-C 代码的逐步演练。 


## <a name="binding-objective-c"></a>绑定 Objective-C

一旦你掌握了 C# 与 Objective-C 的差异，并完成上述绑定演练后，即可顺利过渡到 Xamarin 平台。 作为后续内容，关于 Xamarin.iOS 绑定技术的更详细信息（包括全面的绑定参考），可在[绑定 Objective-C](~/ios/platform/binding-objective-c/index.md) 部分中找到。

## <a name="cross-platform-development"></a>跨平台开发

最后，在移到 Xamarin.iOS 之后，你需要查看我们提供的跨平台指导，包括我们已经开发的参考应用程序的案例研究，以及创建可重用的跨平台代码的最佳做法（包含在[生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)部分中）。
