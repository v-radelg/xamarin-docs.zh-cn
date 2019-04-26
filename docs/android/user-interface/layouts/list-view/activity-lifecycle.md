---
title: ListView 和活动生命周期
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b2328759b3158920bc8683ec14c2aebefd7a04ae
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187068"
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView 和活动生命周期

活动通过某些状态转在应用程序运行，如启动、 运行、 暂停和停止。 有关详细信息，以及有关处理状态转换的特定指导原则，请参阅[活动生命周期教程](~/android/app-fundamentals/activity-lifecycle/index.md)。
务必要了解活动生命周期和位置在`ListView`正确的位置中的代码。

在活动的此文档中的示例的所有执行安装任务`OnCreate`方法和 （如果需要） 中执行清除`OnDestroy`。 这些示例通常使用小型数据集不会更改，因此是不必要的更频繁地重新加载数据。

但是，如果数据频繁更改，或者使用大量的内存可能适合使用不同的生命周期方法来填充和刷新你`ListView`。 例如，如果基础数据不断更改 （或可能会受到其他活动上的更新） 然后创建中的适配器`OnStart`或`OnResume`将确保最新的数据显示了每次显示活动。

如果适配器使用资源，如内存、 或托管的游标，请记住需要释放这些资源中其中实例化时所 （例如在补充方法。 中创建的对象`OnStart`可以中释放`OnStop`)。


## <a name="configuration-changes"></a>配置更改

务必记住该配置更改&ndash;尤其是屏幕上旋转和键盘的可见性&ndash;可能会导致销毁并重新创建当前活动 (除非指定其他方式使用`ConfigurationChanges`属性）。 这意味着，正常情况下，旋转设备将导致`ListView`并`Adapter`重新创建和 (除非您编写代码`OnPause`和`OnResume`) 滚动位置和行选择状态都将丢失。

以下属性会阻止将活动从会销毁并重新创建由于配置更改：

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

活动应然后替代`OnConfigurationChanged`适当地响应这些更改。 有关如何处理配置更改的更多详细信息请参阅文档。

