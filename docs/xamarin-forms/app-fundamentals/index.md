---
title: Xamarin.Forms 应用程序基础知识
description: 了解 Xamarin.Forms 应用程序开发的基础知识，从所有必需的核心概念，到最后的辅助功能和本地化等。
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 61d9c5b1c7b81bfc8a20932ce1d188cd0cb0f980
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650432"
---
# <a name="xamarinforms-application-fundamentals"></a>Xamarin.Forms 应用程序基础知识

## <a name="accessibilityaccessibilityindexmd"></a>[辅助功能](accessibility/index.md)

在 Xamarin.Forms 中结合辅助功能（如支持屏幕阅读工具）的提示。

## <a name="app-classapplication-classmd"></a>[应用类](application-class.md)

`Application` 类是 Xamarin.Forms 的起始点，每个应用都需要实现子类 `App` 才能设置初始页面。 此外，它还提供了用于简单数据存储的 `Properties` 集合。 可采用 C# 或 XAML 的形式对其进行定义。

## <a name="app-lifecycleapp-lifecyclemd"></a>[应用生命周期](app-lifecycle.md)

借助 `Application` 类、`OnStart`、`OnSleep` 和 `OnResume` 方法以及模式导航事件，可使用自定义代码处理应用程序生命周期事件。

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[应用程序索引和深层链接](deep-linking.md)

应用程序索引使得用了几次之后可能被忘记的应用程序出现在搜索结果中从而不会被忘。 深层链接使应用程序响应包含应用程序数据的搜索结果，通常是通过导航到引用自深层链接的页面来实现的。

## <a name="behaviorsbehaviorsindexmd"></a>[行为](behaviors/index.md)

通过使用行为来添加功能，无需将控件子类化，即可轻松扩展用户界面控件。

## <a name="custom-rendererscustom-rendererindexmd"></a>[自定义呈现器](custom-renderer/index.md)

通过自定义呈现器，开发人员可以自定义 Xamarin.Forms 控件在各个平台上的外观和行为，并以此“替代”其默认呈现形式（可根据需要使用本机 SDK）。

## <a name="data-bindingdata-bindingindexmd"></a>[数据绑定](data-binding/index.md)

数据绑定将两个对象的属性链接起来，如此，对某一属性的更改将自动反映在另一个属性中。 数据绑定是模型-视图-视图模型 ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) 应用程序体系结构必不可少的一部分。

## <a name="dependency-servicedependency-serviceindexmd"></a>[依赖项服务](dependency-service/index.md)

`DependencyService` 提供了简单的定位符，以便能在共享代码中对界面进行编码，并提供自动解析的特定于平台的实现，从而可以轻松地在 Xamarin.Forms 中引用特定于平台的功能。

## <a name="effectseffectsindexmd"></a>[效果](effects/index.md)

借助效果可对每个平台上的本机控件进行自定义，通常用于细微的样式更改。

## <a name="gesturesgesturesindexmd"></a>[手势](gestures/index.md)

Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) 类支持用户界面控件上的点击、收缩和平移手势。

## <a name="localizationlocalizationindexmd"></a>[本地化](localization/index.md)

内置的 .NET 本地化框架可与 Xamarin.Forms 结合使用，从而生成跨平台的多语言应用程序。

## <a name="messaging-centermessaging-centermd"></a>[消息中心](messaging-center.md)

借助 Xamarin.Forms `MessagingCenter` 只需一个简单的消息协定，而不必知道任何关于彼此的信息，便能实现视图模型和其他组件的相互通信。

## <a name="navigationnavigationindexmd"></a>[导航](navigation/index.md)

Xamarin.Forms 提供多种不同的页面导航体验，具体取决于使用的 `Page` 类型。

## <a name="shellshellindexmd"></a>[shell](shell/index.md)

Xamarin.Forms Shell 简化了移动应用程序开发，方法是提供大多数移动应用程序所需的基本功能。 包括常见的导航用户体验、基于 URI 的导航方案，以及集成的搜索处理程序。

## <a name="templatestemplatesindexmd"></a>[模板](templates/index.md)

控件模板可以在运行时轻松地设计和重新设计应用程序页面主题，而数据模板则可定义受支持控件上的数据表示形式。

## <a name="triggerstriggersmd"></a>[触发器](triggers.md)

通过响应 XAML 中的属性更改和事件来更新控件。
