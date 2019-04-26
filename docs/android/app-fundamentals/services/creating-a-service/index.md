---
title: 创建服务
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 8c2086025ccb5fe41b3ffddc9cd650c1e0c81fbc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013464"
---
# <a name="creating-a-service"></a>创建服务

Xamarin.Android 服务必须遵循 Android 服务的两个侵犯的隔离规则：

* 它们必须扩展[ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/)。
* 它们必须使用修饰[ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)。

Android 服务的另一个要求是必须注册它们**AndroidManifest.xml**和给定的唯一名称。 Xamarin.Android 将自动注册该服务清单中在生成时使用必需的 XML 特性。

此代码段是在 Xamarin.Android 中满足这两个要求创建服务的最简单的示例：  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

在编译时，Xamarin.Android 将注册该服务的注入到下面的 XML 元素**AndroidManifest.xml** （注意，Xamarin.Android 生成服务的随机名称）：

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

可以通过其他 Android 应用程序与共享服务_导出_它。 这可以通过设置`Exported`属性上的`ServiceAttribute`。 一种服务，在导出时`ServiceAttribute.Name`还应设置属性以提供有意义的公共名称的服务。 此代码段演示了如何导出和命名服务：

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

**AndroidManifest.xml**此服务的元素然后如下所示：

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

服务具有其自己使用创建服务时调用的回调方法的生命周期。 完全调用哪个方法取决于服务的类型。 已启动的服务必须实现不同的生命周期方法比绑定的服务，而混合服务必须实现已启动的服务和绑定的服务的回调方法。 这些方法包括的所有成员`Service`类; 该服务已启动将确定哪些生命周期方法将在调用。 将稍后更详细地讨论这些生命周期方法。

默认情况下，服务将作为 Android 应用程序在同一进程中启动。 可以通过设置其自身进程中启动服务`ServiceAttribute.IsolatedProcess`属性设为 true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

下一步是了解如何启动服务，然后继续研究如何实现三个不同类型的服务。

> [!NOTE]
> 服务在运行在 UI 线程，因此，如果任何工作是为执行可阻止将 UI，该服务必须使用线程来执行的工作。

## <a name="starting-a-service"></a>启动服务

若要在 Android 中启动服务的最基本方法是调度`Intent`其中包含元数据来帮助确定哪个服务应已启动。 有两种不同样式的意向，可用于启动服务：

-   **目的在于明确指示** &ndash; _目的在于明确指示_将确定完全什么服务应该用于完成给定的操作。 目的在于明确指示可以看作具有特定的地址; 以字母Android 将路由到显式标识的服务的目的。 此代码段是使用显式意向来启动调用服务的一个示例`DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **隐式 Intent** &ndash;这种类型的意向松散标识操作，用户想要执行，但若要完成该操作的确切服务是未知。 隐式 Intent 可以被视为是以字母解决"To Whom It 可能需考虑..."。
    Android 将检查的目的，内容并确定是否符合要求的现有服务。

    _意向筛选器_用来帮助查找与已注册服务的隐式目的。 意向筛选器是添加到一个 XML 元素**AndroidManifest.xml**其中包含必需元数据来帮助查找具有隐式 intent 的服务。

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

如果 Android 具有隐式 intent 的多个可能的匹配项，它可能会要求用户选择要处理操作的组件：

![一个消除二义性对话框的屏幕截图](images/creating-a-service-01.png "消除二义性对话框的屏幕快照")

> [!IMPORTANT]
> 从 Android 5.0 （AP 级别 21） 开始隐式 intent 不能用于启动服务。

在可能的情况下，应用程序应使用显式意向来启动服务。 隐式 Intent 不要求提供要启动的特定服务&ndash;它是在设备上安装某些服务的请求来处理该请求。 此二义性请求可能导致错误的服务处理请求或不必要地启动 （这会增加设备上的资源的压力） 的另一个应用。

如何调度目的取决于服务的类型，并将更高版本中为每种类型的服务的特定指南的更详细地讨论。


### <a name="creating-an-intent-filter-for-implicit-intents"></a>为隐式意向创建意向筛选器

若要将服务与隐式 Intent 相关联，Android 应用程序必须提供某些元数据来标识该服务的功能。 此元数据由提供_意向筛选器_。 意向筛选器包含一些信息，例如操作或意向来启动服务中必须存在的数据类型。 在 Xamarin.Android 中，意向筛选器中注册**AndroidManifest.xml**修饰具有的服务[ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)。 例如，下面的代码添加相应的操作的意向筛选器`com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

这会导致中包含的条目**AndroidManifest.xml**文件&ndash;打包在一起的方式类似于下面的示例应用程序的条目：

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

使用 Xamarin.Android 服务开的基础知识，让我们检查更多详细信息中的服务不同子的类型。


## <a name="related-links"></a>相关链接

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
