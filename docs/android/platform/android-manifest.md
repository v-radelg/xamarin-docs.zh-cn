---
title: 使用 Android 清单
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: f1cc2f4685354687390866c0922a802591c7c054
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757706"
---
# <a name="working-with-the-android-manifest"></a>使用 Android 清单

**Androidmanifest.xml**是 android 平台中的强大文件，用于描述应用程序到 Android 的功能和要求。 但使用它并不容易。 Xamarin 通过允许你向类添加自定义属性来帮助最大程度地降低这一难度，然后，这些属性将用于自动生成清单。 我们的目标是，99% 的用户永远不需要手动修改**androidmanifest.xml**。 

**Androidmanifest.xml**是生成过程的一部分，在**Properties/androidmanifest.xml**中找到的 xml 将与从自定义属性生成的 xml 合并在一起。 生成的合并**androidmanifest.xml**驻留在**obj**子目录中;例如，它驻留在用于调试生成的**obj/Debug/android/androidmanifest.xml。** 合并过程很简单：它使用代码中的自定义属性生成 XML 元素，并将这些元素*插入* **androidmanifest.xml**。 

## <a name="the-basics"></a>基本知识

在编译时，将扫描程序集以查找`abstract`派生自[活动](xref:Android.App.Activity)的非类，并[`[Activity]`](xref:Android.App.ActivityAttribute)在其上声明属性。 然后，它使用这些类和属性生成清单。 例如，考虑以下代码： 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

这会导致不会在**androidmanifest.xml**中生成任何内容。 如果希望`<activity/>`生成元素，则需要使用[`[Activity]`](xref:Android.App.Activity) 
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

此示例将导致将以下 xml 片段添加到**androidmanifest.xml**：

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

`[Activity]`特性对`abstract`类型不起任何作用;`abstract`忽略类型。

### <a name="activity-name"></a>活动名称

从 Xamarin 5.1 开始，活动的类型名称基于要导出的类型的程序集限定名称的 MD5SUM。 这允许从两个不同的程序集提供相同的完全限定名称，而不会出现打包错误。 （在 Xamarin 5.1 之前，活动的默认类型名称是从 lowercased 命名空间和类名称创建的。） 

如果要重写此默认值并显式指定活动的名称，请使用[`Name`](xref:Android.App.ActivityAttribute.Name)属性： 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

此示例生成以下 xml 片段：

```xml
<activity android:name="awesome.demo.activity" />
```

*注意*：只应将`Name`属性用于向后兼容的原因，因为这种重命名可能会导致在运行时的类型查找速度变慢。 如果有需要基于 lowercased 命名空间和类名称的活动的默认类型名称的旧代码，请参阅[Android 可调用包装器命名](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming)，了解有关维护兼容性的提示。 

### <a name="activity-title-bar"></a>活动标题栏

默认情况下，当应用程序运行时，它将为应用程序提供标题栏。 用于此的值为[`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label)。 在大多数情况下，此值将与类名称不同。 若要在标题栏上指定应用的标签，请使用[`Label`](xref:Android.App.ActivityAttribute.Label)属性。
例如: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

此示例生成以下 xml 片段：

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

### <a name="launchable-from-application-chooser"></a>从应用程序选择器可启动

默认情况下，你的活动不会显示在 Android 的 "应用程序启动器" 屏幕中。 这是因为您的应用程序可能有很多活动，并且您不希望每个活动都有一个图标。 若要指定哪个应用程序启动器应可启动，请使用[`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher)属性。 例如： 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

此示例生成以下 xml 片段：

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

默认情况下，将为你的活动提供系统提供的默认启动器图标。 若要使用自定义图标，请先将 **.png**添加到**资源/可绘制**，将其生成操作设置为**AndroidResource**， [`Icon`](xref:Android.App.ActivityAttribute.Icon)然后使用属性指定要使用的图标。 例如: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

此示例生成以下 xml 片段：

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

将权限添加到 Android 清单时（如[将权限添加到 Android 清单](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)中所述），这些权限将记录在**Properties/androidmanifest.xml**中。 例如，如果设置`INTERNET`权限，则会将以下元素添加到**Properties/androidmanifest.xml**： 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

调试版本自动设置某些权限，使调试更简单（如`INTERNET`和`READ_EXTERNAL_STORAGE`） &ndash;这些设置仅在生成的**obj/debug/android/androidmanifest.xml**中进行设置，并且不会在**必需的权限**设置。 

例如，如果在**obj/Debug/android/androidmanifest.xml**上检查生成的清单文件，则可能会看到以下添加的权限元素： 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

在清单的版本生成版本（在**obj/Debug/android/androidmanifest.xml**上），*不*会自动配置这些权限。 如果你发现切换到发布版本导致你的应用程序丢失了调试版本中可用的权限，请验证你是否已在你的应用程序**所需的权限**设置中显式设置此权限（请参阅**生成 > Android**Visual Studio for Mac 中的应用程序;请参阅 Visual Studio 中 **> Android 清单的属性**）。 

## <a name="advanced-features"></a>高级功能

### <a name="intent-actions-and-features"></a>意向操作和功能

Android 清单为您提供了一种描述活动功能的方法。 这是通过[意向](https://developer.android.com/guide/topics/manifest/intent-filter-element.html)完成的，[`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
自定义特性。 可以指定适合于活动的操作[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
构造函数，以及适用于[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
属性。 必须至少提供一个活动（这就是在构造函数中提供活动的原因）。 `[IntentFilter]`可以多次提供，每次使用都会导致中的一个单独`<intent-filter/>`的元素。 `<activity/>` 例如：

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

此示例生成以下 xml 片段：

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

Android 清单还提供了一种为整个应用程序声明属性的方法。 这是通过`<application>`元素及其对应的[应用程序](xref:Android.App.ApplicationAttribute)自定义属性来完成的。 请注意，这些是应用程序范围的设置，而不是每个活动的设置。 通常，你为`<application>`整个应用程序声明属性，然后根据每个活动重写这些设置（根据需要）。 

例如， `Application`将以下属性添加到**AssemblyInfo.cs** ，以指示可调试应用程序，其用户可读名称为 **"我的应用**"，并使用该`Theme.Light`样式作为 "所有" 的默认主题activity 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

此声明会导致在**obj/Debug/android/androidmanifest.xml**中生成以下 XML 片段：

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

在此示例中，应用中的所有活动都将默认`Theme.Light`为样式。 如果将活动`Theme.Dialog`的主题设置为，则只有该活动将`Theme.Dialog`使用样式，而应用中的所有其他活动将默认为`<application>`元素`Theme.Light`中设置的样式。 

元素不是配置`<application>`属性的唯一方法。 `Application` 或者，可以将属性直接插入`<application>` **Properties/androidmanifest.xml**的元素中。 这些设置将合并到驻留在`<application>` **obj/Debug/android/androidmanifest.xml**中的最后一个元素中。 请注意， **Properties/androidmanifest.xml**的内容始终会重写自定义属性提供的数据。 

在`<application>`元素中可以配置许多应用程序范围的属性; 有关这些设置的详细信息，请参阅[ApplicationAttribute](xref:Android.App.ApplicationAttribute)的[公共属性](xref:Android.App.ApplicationAttribute)部分。 

## <a name="list-of-custom-attributes"></a>自定义属性列表

- [ActivityAttribute](xref:Android.App.ActivityAttribute) ：生成[/Manifest/application/activity](https://developer.android.com/guide/topics/manifest/activity-element.html) XML 片段 
- [ApplicationAttribute](xref:Android.App.ApplicationAttribute) ：生成[/Manifest/application](https://developer.android.com/guide/topics/manifest/application-element.html) XML 片段 
- [InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) ：生成[/Manifest/instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) XML 片段 
- [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) ：生成[//Intent-filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) XML 片段 
- [MetaDataAttribute](xref:Android.App.MetaDataAttribute) ：生成[//Meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) XML 片段 
- [PermissionAttribute](xref:Android.App.PermissionAttribute) ：生成[//Permission](https://developer.android.com/guide/topics/manifest/permission-element.html) XML 片段 
- [PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) ：生成[//Permission-group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) XML 片段 
- [PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) ：生成[//Permission-tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) XML 片段 
- [ServiceAttribute](xref:Android.App.ServiceAttribute) ：生成[/Manifest/application/service](https://developer.android.com/guide/topics/manifest/service-element.html) XML 片段 
- [UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) ：生成[/Manifest/application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) XML 片段 
- [UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) ：生成[/Manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) XML 片段 
- [BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) ：生成[/Manifest/application/receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) XML 片段 
- [ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) ：生成[/Manifest/application/provider](https://developer.android.com/guide/topics/manifest/provider-element.html) XML 片段 
- [GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) ：生成[/Manifest/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML 片段
