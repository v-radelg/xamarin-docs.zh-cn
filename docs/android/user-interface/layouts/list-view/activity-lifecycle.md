---
title: Xamarin ListView ListView 和活动生命周期
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: a4ebda89ad3bdcb663dc9d7cd513e45760163c34
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028936"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>Xamarin ListView ListView 和活动生命周期

当应用程序运行时，活动会经历某些状态，例如启动、运行、暂停和停止。 有关详细信息以及有关如何处理状态转换的特定准则，请参阅[活动生命周期教程](~/android/app-fundamentals/activity-lifecycle/index.md)。
了解活动生命周期并将 `ListView` 代码放在正确的位置，这一点很重要。

本文档中的所有示例都在活动的 `OnCreate` 方法中执行 "设置任务"，（如果需要）在 `OnDestroy`中执行 "拆卸"。 这些示例通常使用不会更改的小型数据集，因此不需要更频繁地重新加载数据。

但是，如果数据频繁更改或使用大量内存，则可能适合使用不同的生命周期方法来填充和刷新 `ListView`。 例如，如果基础数据不断变化（或可能会受到其他活动的更新的影响），则在 `OnStart` 或 `OnResume` 中创建适配器将确保每次显示活动时显示最新的数据。

如果适配器使用内存等资源或托管游标，请记得在互补方法中释放这些资源，以将其实例化到何处（例如 在 `OnStart` 中创建的对象可以在 `OnStop`中释放。

## <a name="configuration-changes"></a>配置更改

请务必记住，配置更改 &ndash; 尤其是屏幕旋转和键盘可见性 &ndash; 会导致当前活动被销毁并重新创建（除非你使用 `ConfigurationChanges` 特性指定了其他指定）。 这意味着在正常情况下，旋转设备将导致重新创建 `ListView` 和 `Adapter`，除非你已在 `OnPause` 和 `OnResume`中编写代码，否则滚动位置和行选择状态将会丢失。

以下属性将阻止由于配置更改而导致活动被销毁并重新创建：

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

然后，该活动应覆盖 `OnConfigurationChanged` 以相应地响应这些更改。 有关如何处理配置更改的更多详细信息，请参阅文档。
