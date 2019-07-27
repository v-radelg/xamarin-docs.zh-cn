---
title: 创建服务
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: c5348ab754139dbd4012f6bfe9d22068ac16d12b
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509248"
---
# <a name="creating-a-service"></a>创建服务

Xamarin Android 服务必须遵守 Android 服务的两个 inviolable 规则:

* 它们必须扩展[`Android.App.Service`](xref:Android.App.Service)。
* 它们必须用[`Android.App.ServiceAttribute`](xref:Android.App.ServiceAttribute)来修饰。

Android 服务的另一要求是必须在**androidmanifest.xml**中注册, 并为其指定唯一名称。 Xamarin 会自动在生成时将清单中的服务注册到必需的 XML 属性。

此代码片段是在 Xamarin 中创建满足以下两个要求的服务的最简单示例:  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

在编译时, Xamarin 会通过将以下 XML 元素注入到**androidmanifest.xml**来注册服务 (请注意, xamarin 生成了服务的随机名称):

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

可以通过_导出_服务与其他 Android 应用程序共享该服务。 这是通过在上设置`Exported`属性来完成`ServiceAttribute`的。 导出服务时, `ServiceAttribute.Name`还应将属性设置为为服务提供有意义的公共名称。 此代码段演示如何导出和命名服务:

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

此服务的**androidmanifest.xml**元素将如下所示:

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

服务具有自己的生命周期, 其中包含在创建服务时调用的回调方法。 所调用的具体方法取决于服务的类型。 已启动的服务必须实现不同于绑定服务的生命周期方法, 而混合服务必须为已启动的服务和绑定的服务实现回调方法。 这些方法都是`Service`类的所有成员; 服务的启动方式将确定将调用的生命周期方法。 稍后将更详细地讨论这些生命周期方法。

默认情况下, 服务将以与 Android 应用程序相同的进程启动。 可以通过将`ServiceAttribute.IsolatedProcess`属性设置为 true, 在其自己的进程中启动服务:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

下一步是检查如何启动服务, 然后继续检查如何实现三种不同类型的服务。

> [!NOTE]
> 服务在 UI 线程上运行, 因此, 如果要执行的任何工作将阻止 UI, 则服务必须使用线程来执行工作。

## <a name="starting-a-service"></a>启动服务

在 Android 中启动服务的最基本方法是调度`Intent` , 其中包含元数据, 以帮助标识应启动的服务。 可以使用两种不同的方法来启动服务:

-   **显式意图**明确的目的将确定应该使用哪种服务来完成给定的操作。  &ndash; 可以将显式意图视为具有特定地址的字母;Android 会将意向路由到显式标识的服务。 此代码片段是使用显式意图启动名`DownloadService`为的服务的一个示例:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **隐式意向**&ndash;此类意向松散标识用户希望执行的操作, 但完成该操作的确切服务是未知的。 可以将隐式意向看作是 "可能关注的人员 ..." 的字母。
    Android 将检查意图的内容, 并确定是否存在与意向匹配的现有服务。

    _意向筛选器_用于帮助与已注册的服务匹配隐式目的。 意向筛选器是一个 XML 元素, 它将添加到**androidmanifest.xml**中, 该元素包含必要的元数据以帮助与具有隐式目的的服务匹配。

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

如果某个隐式意向有多个可能的匹配项, 则它可能会要求用户选择该组件来处理操作:

![消除歧义对话框的屏幕截图](images/creating-a-service-01.png "消除歧义对话框的屏幕截图")

> [!IMPORTANT]
> 从 Android 5.0 (AP 级别 21) 开始, 无法使用隐式意向来启动服务。

在可能的情况下, 应用程序应使用显式意向来启动服务。 隐式意向不要求特定服务启动&ndash; , 而是在设备上安装了某个服务的请求来处理请求。 这种不明确的请求会导致错误的服务处理请求, 或者不必要地启动其他应用 (这会增加设备上资源的压力)。

如何分派意向取决于服务类型, 稍后将在特定于每种服务类型的指南中更详细地讨论。


### <a name="creating-an-intent-filter-for-implicit-intents"></a>为隐式意向创建意向过滤器

若要将服务与隐式意向相关联, Android 应用必须提供一些元数据来标识服务的功能。 此元数据由_意向筛选器_提供。 意向筛选器包含某些信息, 如操作或数据类型, 这些信息必须在意向中提供以启动服务。 在 Xamarin 中, 目的筛选器通过使用[`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute)修饰服务在**androidmanifest.xml**中注册。 例如, 下面的代码添加了一个具有相关操作的`com.xamarin.DemoService`意向筛选器:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

这会导致**androidmanifest.xml**文件&ndash;中包含一个条目, 该条目以与以下示例类似的方式打包在应用程序中:

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

借助 Xamarin 服务的基础知识, 让我们更详细地查看服务的不同子类型。


## <a name="related-links"></a>相关链接

- [Android.App.Service](xref:Android.App.Service)
- [Android.App.ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Android.App.Intent](xref:Android.Content.Intent)
- [Android.App.IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)
