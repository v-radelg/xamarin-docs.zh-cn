---
title: 使用 Xamarin.iOS 中的用户默认值
description: 本文介绍如何使用 NSUserDefaults 在 Xamarin iOS 应用程序或扩展中保存默认设置。 其中介绍了使用 NSUserDefaults 在高级别，并讨论了如何读取和写入值。
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 688db534d6c99a8fadb7535f0532f9c1e9564707
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153381"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>使用 Xamarin.iOS 中的用户默认值

_本文介绍如何使用 NSUserDefault Xamarin.iOS 应用程序或扩展中保存默认设置。_


`NSUserDefaults`类提供一种方法适用于 iOS 的应用和扩展来以编程方式与整个系统的默认系统进行交互。 通过使用默认系统，用户可以配置应用程序的行为或样式设计来满足其首选项 （基于应用的设计）。 例如，若要提供英制的指标的 vs 中的数据或选择一个给定的用户界面主题。

与应用组一起使用时`NSUserDefaults`还提供一种应用 （或扩展） 之间进行通信给定组中。

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>有关用户默认值

正如所述的上方，用户默认设置 (`NSUserDefaults`) 可以添加到应用程序 （或扩展），用于提供最终用户可以修改它来调整其外观或在运行时应用的操作的可配置选项。

当您的应用程序首次执行时，`NSUserDefaults`从应用程序的用户默认数据库中读取的键和值并将其缓存到内存中以避免打开和读取数据库每个时间值是必需的。 

> [!IMPORTANT]
> Apple 不再建议开发人员调用`Synchronize`方法直接同步与数据库的内存中缓存。 相反，它将自动调用按周期性间隔，以使内存中缓存与用户的默认数据库同步。

`NSUserDefaults`类包含多个便捷方法，用于读取和写入首选项值为常见数据类型如： 字符串、 整数、 浮点、 布尔值和 Url。 其他类型的数据可以使用存档`NSData`，然后读取或写入到用户默认数据库。 有关详细信息，请参阅 Apple[首选项和设置编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)。

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>访问共享的 NSUserDefaults 实例 

为当前用户的设备的情况下，共享的用户将默认实例提供对用户的默认设置的访问。 如果不存在共享默认值对象，它创建第一次访问和使用以下信息初始化：

- `NSArgumentDomain`包含分析从当前应用程序的默认值。
- 应用的捆绑标识符域。
- `NSGlobalDomain`包含的所有应用都共享的默认值。
- 为每个用户的首选语言单独的域。
- `NSRegistrationDomain`与一组可由应用程序以确保搜索始终成功修改的临时默认值。

若要访问共享的用户将默认实例，请使用以下代码：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>访问应用程序组使用 NSUserDefaults 实例

如上所述，通过使用应用组`NSUserDefaults`可用于应用程序 （或扩展） 之间进行通信给定组中。 首先，需要确保，应用组和所需的应用程序 Id 是否已正确配置中**证书、 标识符和配置文件**部分[iOS 开发人员中心](https://developer.apple.com/devcenter/ios/)并且都已安装在开发环境中。

接下来，需要有一个有效的应用程序 Id 为上面创建的应用程序和/或扩展项目和`Entitlements.plist`文件必须包含在与应用程序组启用了并指定应用程序包。

这一切后，共享应用程序组用户默认设置即可访问使用以下代码：

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

其中`group.com.xamarin.todaysharing`中创建应用程序组**证书、 标识符和配置文件**想要访问。 有关详细信息，请参阅[应用程序组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文档。

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>读取默认值

具有访问所需的用户默认数据库后，您可以使用键/值对和多种便捷方法根据正在读取的数据类型的默认值来读取值：

- `ArrayForKey` -返回的数组`NSObjects`的给定密钥值。
- `BoolForKey` -返回一个布尔值，给定的键。
- `DataForKey` -返回`NSData`给定键的对象。
- `DictionaryForKey` -返回`NSDictionary`给定键。
- `DoubleForKey` -返回给定键的双精度值。
- `FloatForKey` -返回给定键的 float 值。
- `IntForKey` -返回给定键的整数值。
- `StringArrayForKey` -返回的数组`String`对象从给定的密钥值。
- `StringForKey` -返回给定键的字符串值。
- `URLForKey` -返回`NSUrl`给定键的值。

例如，下面的代码将读取从用户默认设置一个布尔值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>编写默认值

就像读取上述值，在访问所需的用户默认的数据库之后, 可以写入值使用键/值对和多种便捷方法根据正在写入的数据类型的默认值：

- `SetBool` -将给定的布尔值写入到给定的键。
- `SetDouble` -将给定的双精度值写入到给定的键。
- `SetFloat` -将给定的浮点值写入给定的键。
- `SetString` -将给定的字符串值写入给定的键。
- `SetURL` -写入给定的 URL (`NSUrl`) 到给定键的值。

例如，下面的代码将写入到用户默认设置一个布尔值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> 当您的应用程序首次执行时，`NSUserDefaults`从应用程序的用户默认数据库中读取的键和值并将其缓存到内存中以避免打开和读取数据库每个时间值是必需的。



<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了`NSUserDefaults`类以及如何使用它来提供一组最终用户可用于配置 Xamarin.iOS 应用程序的选项。 此外，其中包括如何使用应用组扩展其父应用程序之间或在组中的应用之间进行通信。


## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [首选项和设置编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
