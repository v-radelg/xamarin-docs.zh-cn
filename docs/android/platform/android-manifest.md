---
title: 使用 Android 清单
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 5e354f8271257ab21a855bdf5d576ce3062fadc7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957384"
---
# <a name="working-with-the-android-manifest"></a>使用 Android 清单


## <a name="overview"></a>概述

**AndroidManifest.xml**是功能强大的文件，在 Android 平台，可用于描述的功能和到 Android 应用程序的要求。 但是，使用它并不容易。 Xamarin.Android 可帮助尽量减少此问题通过允许您将自定义属性添加到你的类，然后将用于自动生成的清单。 我们的目标是 99%的用户应永远不需要手动修改**AndroidManifest.xml**。 

**AndroidManifest.xml**作为生成过程中和中找到的 XML 的一部分生成**properties/Androidmanifest.xml**与从自定义特性生成的 XML 合并。 生成合并**AndroidManifest.xml**驻留在**obj**子目录; 例如，它位于**obj/Debug/android/AndroidManifest.xml**对于调试版本. 合并过程非常简单： 它使用自定义特性的代码中生成 XML 元素和*将插入*到这些元素**AndroidManifest.xml**。 



## <a name="the-basics"></a>基本知识

在编译时，程序集进行扫描的非`abstract`派生的类[活动](https://developer.xamarin.com/api/type/Android.App.Activity/)并且有[ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/)在其上声明属性。 它然后使用这些类和属性生成清单。 例如，考虑以下代码： 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

这会导致执行任何操作中生成**AndroidManifest.xml**。 如果你想`<activity/>`元素生成，你需要使用 [`[Activity]`](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) 
自定义属性： 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

此示例会导致下面的 xml 片段，若要添加到**AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

`[Activity]`属性不起任何作用`abstract`类型;`abstract`类型将被忽略。



### <a name="activity-name"></a>活动名称

从 Xamarin.Android 5.1 开始，活动的类型名称基于要导出的类型的程序集限定名称的 MD5SUM。 这样，相同的完全限定名称，以提供从两个不同的程序集，并且不使打包错误。 （在 Xamarin.Android 5.1 中之前, 活动的默认类型名称已从创建小写的命名空间和类名。） 

如果你想要覆盖此默认值并显式指定您的活动，使用名称[ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)属性： 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

此示例将生成以下 xml 片段：

```xml
<activity android:name="awesome.demo.activity" />
```

*请注意*： 应使用`Name`仅出于向后兼容性原因，这种情况下重命名的属性可能会降低在运行时类型查找。 如果有旧代码预期要基于的小写的命名空间的活动的默认类型名称和类名，请参阅[Android 可调用包装器命名](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming)有关保持兼容性的提示。 


### <a name="activity-title-bar"></a>活动标题栏

默认情况下，Android 提供你的应用程序标题栏将在运行时。 使用此值是[ `/manifest/application/activity/@android:label` ](https://developer.android.com/guide/topics/manifest/activity-element.html#label)。 在大多数情况下，此值将不同于您的类名。 若要指定应用的标签的标题栏上，使用[ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/)属性。
例如： 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

此示例将生成以下 xml 片段：

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>可以从应用程序选择器

默认情况下，您的活动不会出现在 Android 的应用程序启动器屏幕中。 这是因为可能有许多活动应用程序中，并且对于每个不想一个图标。 若要指定哪一列应为可从应用程序启动程序启动，请使用[ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/)属性。 例如： 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

此示例将生成以下 xml 片段：

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```



### <a name="activity-icon"></a>活动图标

默认情况下，您的活动将提供由系统提供的默认启动器图标。 若要使用自定义图标，请首先添加你 **.png**到**资源/drawable**，将其生成操作设置为**AndroidResource**，然后使用[ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/)属性来指定要使用的图标。 例如： 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

此示例将生成以下 xml 片段：

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>权限

当将权限添加到 Android 清单 (如中所述[将权限添加到 Android 清单](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest))，这些权限记录在**properties/Androidmanifest.xml**。 例如，如果您设置`INTERNET`权限，将以下元素添加到**properties/Androidmanifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

调试版本会自动设置某些权限以简化调试 (例如`INTERNET`并`READ_EXTERNAL_STORAGE`)&ndash;配置这些设置仅在生成**obj/Debug/android/AndroidManifest.xml** ，而不显示为已启用**所需的权限**设置。 

例如，如果您检查在生成清单文件**obj/Debug/android/AndroidManifest.xml**，可能会看到以下添加的权限元素： 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

发行版中的生成清单的版本 (在**obj/Debug/android/AndroidManifest.xml**)，这些权限是*不*自动配置。 如果您发现，切换到发布版本会导致应用失去了调试版本中提供的权限，验证是否已显式设置此权限**所需的权限**应用 （请参阅的设置**生成 > Android 应用程序**在 Visual Studio for Mac; 请参阅**属性 > Android 清单**Visual Studio 中)。 




## <a name="advanced-features"></a>高级的功能


### <a name="intent-actions-and-features"></a>意向操作和功能

Android 清单提供了一种方法，以便描述您的活动的功能。 这是通过[意向](https://developer.android.com/guide/topics/manifest/intent-filter-element.html)和 [`[IntentFilter]`](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) 
自定义属性。 可以指定的操作是适用于您使用的活动 [`IntentFilter`](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) 
构造函数，和使用适当的哪些类别 [`Categories`](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) 
属性。 至少一个活动必须提供 （这是在构造函数中提供的活动的原因）。 `[IntentFilter]` 可以提供多个，且每次使用会导致单独`<intent-filter/>`元素内的`<activity/>`。 例如：

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

此示例将生成以下 xml 片段：

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```


### <a name="application-element"></a>应用程序元素

Android 清单还提供了一种方法，以便声明在整个应用程序的属性。 这是通过`<application>`元素和其对应[应用程序](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/)自定义属性。 请注意，这些是应用程序级 （程序集范围） 的设置，而不是每个活动的设置。 通常情况下，声明`<application>`整个应用程序的属性和替代这些设置 （如需要） 在每个活动的基础上。 

例如，以下`Application`属性添加到**AssemblyInfo.cs**以指示可以调试该应用程序，其用户可读名称是**My App**，并使用`Theme.Light`所有活动的默认主题样式： 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

此声明将导致下面的 XML 片段，若要在其中生成**obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
在此示例中，应用程序中的所有活动将都默认为`Theme.Light`样式。 如果活动的主题设置为`Theme.Dialog`，仅该活动将会使用`Theme.Dialog`风格的应用程序中的所有其他活动将默认为`Theme.Light`样式中设置`<application>`元素。 

`Application`元素不是唯一的方法来配置`<application>`属性。 或者，可以插入特性直接插入`<application>`的元素**properties/Androidmanifest.xml**。 这些设置将合并成最终`<application>`驻留在的元素**obj/Debug/android/AndroidManifest.xml**。 请注意的内容**properties/Androidmanifest.xml**一直重写提供自定义特性的数据。 

还有很多应用程序范围属性，可以在中配置`<application>`元素; 有关这些设置的详细信息，请参阅[公共属性](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties)一部分[ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>自定义特性列表

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) :将生成[/manifest/application/activity](https://developer.android.com/guide/topics/manifest/activity-element.html) XML 片段 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) :将生成[/清单/应用程序](https://developer.android.com/guide/topics/manifest/application-element.html)XML 片段 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) :将生成[/清单/检测](https://developer.android.com/guide/topics/manifest/instrumentation-element.html)XML 片段 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) :将生成[//intent-filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) XML 片段 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) :将生成[//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) XML 片段 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) :将生成[//permission](https://developer.android.com/guide/topics/manifest/permission-element.html) XML 片段 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) :将生成[//permission-group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) XML 片段 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) :将生成[//permission-tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) XML 片段 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) :将生成[/manifest/application/service](https://developer.android.com/guide/topics/manifest/service-element.html) XML 片段 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) :将生成[/manifest/application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) XML 片段 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) :将生成[/manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) XML 片段 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) :将生成[/manifest/application/receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) XML 片段 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) :将生成[/manifest/application/provider](https://developer.android.com/guide/topics/manifest/provider-element.html) XML 片段 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) :将生成[/manifest/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML 片段

