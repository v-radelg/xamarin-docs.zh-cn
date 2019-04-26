---
title: 其他 watchOS 3 框架更改
description: 本文档介绍各种 framework 更改引入了 watchOS 3，以及如何在 Xamarin 中使用它们。 讨论了核心数据、 Core 运动、 Foundation、 HealthKit、 HomeKit、 PassKit 和 UIKit。
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: e3eb4e3454aeab08d1333c5dbc3d4808fa4d676c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224508"
---
# <a name="additional-watchos-3-frameworks-changes"></a>其他 watchOS 3 框架更改

_本文介绍如何附加的次要更改或增强功能到现有框架，可用于 watchOS 3。_

除了 iOS 的主要更改，Apple 已 watchOS 3 中进行修改和改进到多个现有框架。


## <a name="core-data"></a>核心数据

具有对监视 OS 3 的核心数据框架进行了以下增强功能：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)对象支持并发故障和提取而无需序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)类维护的 SQLite 数据存储区池。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) SQLite 在 WAL 日志模式下支持新的查询生成的数据存储的对象功能可以将托管对象上下文 (MOC) 固定到将来中提取的特定数据库版本和出错的事务。
- 使用高级`NSPersistenceContainer`引用`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心数据配置资源。
- 已添加到多个新的便捷方法`NSManagedObject`轻松地执行提取操作，并创建子类。

有关详细信息，请参阅 Apple[核心数据框架引用](https://developer.apple.com/reference/coredata)。


## <a name="core-motion"></a>核心运动

已对核心运动框架，用于监视 OS 3 进行以下增强功能：

- 新设备运动事件使用加速感应器和陀螺仪提供动作和方向的更新。 应用可以注册此更新 （在最多 100 Hz 费率）。
- 新的计步器事件可以快速、 实时通知时用户暂停和继续运行。 使用[CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer)注册前景色或背景计步器事件。


## <a name="foundation"></a>Foundation

具有对监视 OS 3 Foundation 框架进行了以下增强功能：

- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)类，以使日期和时间间隔计算，如持续时间，用于比较的时间间隔和测试的时间间隔的交叉点。
- 多个新属性已添加到[NSLocal](https://developer.apple.com/reference/foundation/nslocale)类来获取本地信息和可用的显示格式。
- 使用新[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)类来转换之间不同单位的度量值 (UOM) 或对不同 UOMs 中的值执行计算。
- 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)类设置用于向最终用户显示本地化的度量值的格式。
- 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)并[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)类用于表示特定 UOMs。


## <a name="healthkit"></a>HealthKit

具有对 HealthKit 框架，用于监视 OS 3 进行以下增强功能：

- 使用新[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)类，以指定`ActivityType`和`LocationType`的健身。
- 新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)并`WheelchairUse`方法[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)类中添加了使用轮椅通道相关的运行状况数据。
- 新的元数据密钥已添加的天气类型 (如`HKWeatherConditionClear`并`HKWeatherConditionCloudy`) 和健身类型 (如`HKWorkoutActivityTypeFlexibility`和`HKWorkoutActivityTypeWheelchairRunPace`) 已添加。


## <a name="homekit"></a>HomeKit

具有对监视 OS 3 HomeKit 框架进行了以下增强功能：

- 添加能够查看并与其交互 HomeKit 连接 IP 相机。
- 添加了几个新的服务和特征。
- 添加更多上下文和配置的主服务和链接服务的附件。


## <a name="passkit"></a>PassKit

具有对监视 OS 3 PassKit 框架进行了以下增强功能：

- 扩展框架，以支持安全的应用程序内的支付实体商品和服务的 Apple Watch 上。
- 目前有以下类：[PKPayment](https://developer.apple.com/reference/passkit/pkpayment)， [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod)， [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest)和[PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

具有对监视 OS 3 UIKit 框架进行了以下增强功能：

- 若要在标签中支持动态类型，文本字段和文本框中使用的新`PreferredFontForTextStyle`方法的`UIFont`类。
- `ColorWithDisplayP3`方法为了支持广泛的颜色。


## <a name="related-links"></a>相关链接

- [入门 （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [什么是 watchOS 3 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
