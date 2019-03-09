---
title: Xamarin.Android API 设计原则
ms.prod: xamarin
ms.assetid: 3E52D815-D95D-5510-0D8F-77DAC7E62EDE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: e762a286069d5ef1db90f3c45808eee0a7a04a7f
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668486"
---
# <a name="xamarinandroid-api-design-principles"></a>Xamarin.Android API 设计原则


## <a name="overview"></a>概述

除了核心是 Mono 的一部分的基类库，Xamarin.Android 提供与各种 Android Api 允许开发人员使用 Mono 创建本机 Android 应用程序的绑定。

有 Xamarin.Android 的核心是互操作引擎与 Java 领域这样桥 C# 的环境和开发人员提供了访问 Java api 从 C# 或其他.NET 语言。


## <a name="design-principles"></a>设计原则

以下是一些 Xamarin.Android 绑定我们设计原则

-  遵守[.NET Framework 设计准则](https://docs.microsoft.com/dotnet/standard/design-guidelines/)。

-  开发人员子类的 Java 类。

-  子类应适用于 C# 标准构造。

-  从现有类派生。

-  链接到调用基构造函数。

-  重写方法应通过 C# 重写系统。

-  使常见的 Java 任务很简单和硬 Java 任务成为可能。

-  将 JavaBean 属性公开为 C# 属性。

-  公开强类型的 API:

    - 提高类型安全性。

    - 最小化运行时错误。

    - 获取 IDE intellisense 上返回类型。

    - 允许 IDE 弹出文档。

-  建议 Api 的 IDE 中探索：

    - 利用最大程度减少 Java Classlib 暴露的 Framework 替代方法。

    - 时适当且适用，而不是单一方法接口公开 C# 委托 (lambda，匿名方法和 System.Delegate)。

    - 提供一种机制来调用任意 Java 库 ( [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/))。


## <a name="assemblies"></a>程序集

Xamarin.Android 包括大量的程序集构成*MonoMobile 配置文件*。 [程序集](~/cross-platform/internals/available-assemblies.md)页提供了更多信息。

为 Android 平台的绑定都包含在`Mono.Android.dll`程序集。 此程序集包含正在使用 Android Api 的整个绑定以及 Android 运行时 VM 与通信。


## <a name="binding-design"></a>绑定设计


### <a name="collections"></a>集合

Android Api 利用 java.util 集合广泛地提供列表、 集和地图。 我们将使用这些元素公开[System.Collections.Generic](xref:System.Collections.Generic)绑定中的接口。 基本映射是：

-   [java.util.Set<E> ](https://developer.android.com/reference/java/util/Set.html)映射到系统类型[ICollection<T>](xref:System.Collections.Generic.ICollection`1)，帮助器类[Android.Runtime.JavaSet<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaSet%601/)。

-   [java.util.List<E> ](https://developer.android.com/reference/java/util/List.html)映射到系统类型[IList<T>](xref:System.Collections.Generic.IList`1)，帮助器类[Android.Runtime.JavaList<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaList%601/)。

-   [< K，V > java.util.Map](https://developer.android.com/reference/java/util/Map.html)映射到系统类型[IDictionary < TKey，TValue >](xref:System.Collections.Generic.IDictionary`2)，帮助器类[Android.Runtime.JavaDictionary < K，V >](https://developer.xamarin.com/api/type/Android.Runtime.JavaDictionary%602/)。

-   [java.util.Collection<E> ](https://developer.android.com/reference/java/util/Collection.html)映射到系统类型[ICollection<T>](xref:System.Collections.Generic.ICollection`1)，帮助器类[Android.Runtime.JavaCollection<T>](https://developer.xamarin.com/api/type/Android.Runtime.JavaCollection%601/)。

我们提供了帮助程序类来帮助更快地 copyless 这些类型的封送处理。 如果可能，我们建议使用这些提供而不是提供框架实现的集合，如[ `List<T>` ](xref:System.Collections.Generic.List`1)或[ `Dictionary<TKey, TValue>` ](xref:System.Collections.Generic.Dictionary`2)。 [Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/)实现利用本机 Java 集合在内部，因此不需要在复制/粘贴到本机集合传递到 Android API 成员时。

可以将任何接口实现传递到接受该接口的 Android 方法，例如传递`List<int>`到[ArrayAdapter&lt;int&gt;(上下文中，int，IList&lt;int&gt;)](https://developer.xamarin.com/api/constructor/Android.Widget.ArrayAdapter%3CT%3E.ArrayAdapter%3CT%3E/p/Android.Content.Context/System.Int32/System.Collections.Generic.IList%7BT%7D/)构造函数。 *但是*，为所有实现*除*对于 Android.Runtime 实现中，这涉及到*复制*Mono Android 运行时 VM 到 VM 的列表。 如果列表为更高版本的 Android 运行时中发生更改 (例如通过调用[ArrayAdapter&lt;T&gt;。Add(T)](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter%3CT%3E.Add/p/T/)方法)，这些更改*将不会*在托管代码中可见。 如果`JavaList<int>`已使用，这些更改都是可见。

措辞不同而已，集合接口的实现代码*不*以上列出**帮助器类**es 仅封送 [In]:

```csharp
// This fails:
var badSource  = new List<int> { 1, 2, 3 };
var badAdapter = new ArrayAdapter<int>(context, textViewResourceId, badSource);
badAdapter.Add (4);
if (badSource.Count != 4) // true
    throw new InvalidOperationException ("this is thrown");

// this works:
var goodSource  = new JavaList<int> { 1, 2, 3 };
var goodAdapter = new ArrayAdapter<int> (context, textViewResourceId, goodSource);
goodAdapter.Add (4);
if (goodSource.Count != 4) // false
    throw new InvalidOperationException ("should not be reached.");
```


### <a name="properties"></a>Properties

Java 方法转换为属性，在适当的时候：

-  Java 方法对`T getFoo()`并`void setFoo(T)`转换为`Foo`属性。 示例:[Activity.Intent](https://developer.xamarin.com/api/property/Android.App.Activity.Intent/)。

-  Java 方法`getFoo()`转换为只读的 Foo 属性。 示例:[Context.PackageName](https://developer.xamarin.com/api/property/Android.Content.Context.PackageName/)。

-  只有组的属性不会生成。

-  属性是*不*如果属性类型将为数组生成。



### <a name="events-and-listeners"></a>事件和侦听器

Android Api 基于 Java 和其组件按照挂接事件侦听器的 Java 模式。 此模式可能会很麻烦，因为它需要用户创建匿名类，并声明要重写的方法，例如，这是如何将在与 Java 配合使用 Android 中执行操作：

```csharp
final android.widget.Button button = new android.widget.Button(context);

button.setText(this.count + " clicks!");
button.setOnClickListener (new View.OnClickListener() {
    public void onClick (View v) {
        button.setText(++this.count + " clicks!");
    }
});
```

将 C# 使用事件中的等效代码：

```csharp
var button = new Android.Widget.Button (context) {
    Text = string.Format ("{0} clicks!", this.count),
};
button.Click += (sender, e) => {
    button.Text = string.Format ("{0} clicks!", ++this.count);
};
```

请注意，这两个以上的机制可使用 Xamarin.Android。 可以实施侦听器接口并将其附加 View.SetOnClickListener，也可以附加创建通过任何常规 C# 模式到 Click 事件的委托。

当侦听器回调方法具有 void 返回时，我们会创建基于 API 元素[EventHandler&lt;TEventArgs&gt; ](xref:System.EventHandler`1)委托。 我们会生成事件如上面的示例中为这些侦听器类型。 但是，如果侦听器回调将返回一个非 void 的和非-**布尔**不使用值、 事件和事件处理。 我们改为生成的回调的签名的特定委托，并添加属性而不是事件。 原因是要处理委托调用排序并返回处理。 此方法反映使用 Xamarin.iOS API 执行的操作。

如果仅自动生成 C# 事件或属性的 Android 事件注册方法：

1. 具有`set`前缀，例如[*设置*OnClickListener](https://developer.xamarin.com/api/member/Android.Views.View.SetOnClickListener/)。

1. 具有`void`返回类型。

1. 只接受一个参数，参数类型是接口、 接口只包含一个方法，并以接口名称结尾`Listener`，例如[View.OnClick*侦听器*](https://developer.xamarin.com/api/type/Android.Views.View+IOnClickListener/)。


此外，如果侦听器接口方法具有返回类型为**布尔**而不是**void**，然后生成*EventArgs*子类将包含*Handled*属性。 值*Handled*作为返回值使用属性*侦听器*方法，并将默认为`true`。

例如，Android [View.setOnKeyListener()](https://developer.xamarin.com/api/member/Android.Views.View.SetOnKeyListener/p/Android.Views.View+IOnKeyListener/)方法接受[View.OnKeyListener](https://developer.xamarin.com/api/type/Android.Views.View+IOnKeyListener)接口，并且[View.OnKeyListener.onKey （视图、 int、 KeyEvent）](https://developer.xamarin.com/api/member/Android.Views.View+IOnKeyListener.OnKey/p/Android.Views.View/Android.Views.Keycode/Android.Views.KeyEvent/)方法具有 boolean 返回类型。 Xamarin.Android 生成相应[View.KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/)事件，即[EventHandler&lt;View.KeyEventArgs&gt;](https://developer.xamarin.com/api/type/Android.Views.View+KeyEventArgs/)。
*KeyEventArgs*类又有[View.KeyEventArgs.Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/)属性，它的返回值作为*View.OnKeyListener.onKey()* 方法。

我们想要添加的其他方法和 ctor 来公开基于委托的连接的重载。 此外，多个回调的侦听器需要一些附加检查，以确定实现单个回调是否合理的因此我们将在发现转换这些。 如果没有相应的事件，侦听器必须使用在 C# 中，但请将任何您认为可能具有委托的使用情况与我们的注意力。 我们还投入了接口而不使用"侦听器"后缀的某些转换时很明显，他们将从一个委托的替代方法获益。

所有侦听器接口实现 [`Android.Runtime.IJavaObject`](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)
接口，因为绑定，以便侦听器类必须实现此接口的实现细节。 这可以通过实现侦听器接口上的一个子类[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)或任何其他包装 Java 对象，如 Android 活动。


### <a name="runnables"></a>可运行对象

Java 利用[java.lang.Runnable](https://developer.xamarin.com/api/type/Java.Lang.Runnable/)接口，以提供一种委派机制。 [Java.lang.Thread](https://developer.xamarin.com/api/type/Java.Lang.Thread/)类是值得注意的此接口使用者。 Android 已经利用了 API 以及中的接口。
[Activity.runOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/p/Java.Lang.IRunnable/)并[View.post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/p/Java.Lang.IRunnable)是值得注意的示例。

`Runnable`接口包含单个 void 方法[run （)](https://developer.xamarin.com/api/member/Java.Lang.Runnable.Run%28%29/)。 它因此适合于 C# 作为中的绑定[System.Action](xref:System.Action)委托。 我们提供了在绑定中的重载，它接受`Action`参数使用的所有 API 成员`Runnable`中的本机 API，例如[Activity.RunOnUiThread()](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action))和[View.Post()](https://developer.xamarin.com/api/member/Android.Views.View.Post/(System.Action)).

我们留下[IRunnable](https://developer.xamarin.com/api/type/Java.Lang.IRunnable/)作为可运行对象直接传递而不是因为多个类型实现接口，因此可以替换它们的位置中的重载。


### <a name="inner-classes"></a>内部类

Java 有两种不同类型的[嵌套类](http://download.oracle.com/javase/tutorial/java/javaOO/nested.html)： 静态嵌套类和非静态类。

Java 静态嵌套的类的 C# 嵌套类型相同。

非静态嵌套类，也称为*内部类*，大不相同。 它们包含对它们的封闭类型的实例的隐式引用，并且不能包含静态成员 （在本概述的范围之外的其他差异）。

谈到绑定并使用 C#，静态嵌套的类被视为普通的嵌套类型。 内部类，同时，具有两个重大差别：

1. 作为构造函数参数，必须显式提供对包含类型的隐式引用。

1. 从内部类，内部类继承时*必须*嵌套在类型的继承自基的内部类，包含类型和派生的类型必须提供与 C# 相同的类型的构造函数包含类型。


例如，考虑[Android.Service.Wallpaper.WallpaperService.Engine](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/)内部类。 由于它是一个内部类，因此[WallpaperService.Engine() 构造函数](https://developer.xamarin.com/api/constructor/Android.Service.Wallpaper.WallpaperService+Engine.Engine/p/Android.Service.Wallpaper.WallpaperService/)将引用[WallpaperService](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService/)实例 (比较和对比到 Java [WallpaperService.Engine (构造函数），](https://developer.xamarin.com/api/type/Android.Service.Wallpaper.WallpaperService+Engine/)这不需要任何参数)。

示例类派生的内部是 CubeWallpaper.CubeEngine:

```csharp
class CubeWallpaper : WallpaperService {
        public override WallpaperService.Engine OnCreateEngine ()
        {
                return new CubeEngine (this);
        }

        class CubeEngine : WallpaperService.Engine {
                public CubeEngine (CubeWallpaper s)
                        : base (s)
                {
                }
        }
}
```

请注意如何`CubeWallpaper.CubeEngine`嵌套在`CubeWallpaper`，`CubeWallpaper`继承自包含类的`WallpaperService.Engine`，并`CubeWallpaper.CubeEngine`具有构造函数采用声明的类型-`CubeWallpaper`都是在这种情况下-上面指定。


### <a name="interfaces"></a>接口

Java 接口可以包含三个集的成员，其中两个从 C# 会导致问题：

1. 方法

1. 类型

1. 字段


Java 接口将转换为两种类型：

1. 一个包含方法声明 （可选） 的接口。 此接口具有相同的名称与 Java 接口，*除*还具有*我*前缀。

1. （可选） 的静态类，其中包含的任何字段内 Java 接口声明。

嵌套的类型是"重定位"是封闭接口而不是嵌套类型，与封闭的接口名称作为前缀的同级。

例如，考虑[android.os.Parcelable](https://developer.xamarin.com/api/type/Android.OS.Parcelable/)接口。
*Parcelable*接口包含方法、 嵌套的类型和常量。 *Parcelable*接口方法放入[Android.OS.IParcelable](https://developer.xamarin.com/api/type/Android.OS.IParcelable/)接口。
*Parcelable*接口常量放入[Android.OS.ParcelableConsts](https://developer.xamarin.com/api/type/Android.OS.ParcelableConsts/)类型。 嵌套[android.os.Parcelable.ClassLoaderCreator <t> </t> ](https://developer.android.com/reference/android/os/Parcelable.ClassLoaderCreator.html)并[android.os.Parcelable.Creator <t> </t> ](https://developer.android.com/reference/android/os/Parcelable.Creator.html)类型目前不由于我们泛型的支持; 中的限制绑定如果它们受支持，它们将会显示作为*Android.OS.IParcelableClassLoaderCreator*并*Android.OS.IParcelableCreator*接口。 例如，嵌套[android.os.IBinder.DeathRecpient](https://developer.android.com/reference/android/os/IBinder.DeathRecipient.html)作为绑定接口[Android.OS.IBinderDeathRecipient](https://developer.xamarin.com/api/type/Android.OS.IBinderDeathRecipient/)接口。


> [!NOTE]
> 从 Xamarin.Android 1.9 开始，Java 接口常量是<em>重复</em>在为了简化将 Java 移植的代码中。 这有助于改善迁移依赖于的 Java 代码[android 提供程序](https://developer.android.com/reference/android/provider/package-summary.html)常量的接口。

除了上述类型，有四个进一步的更改：

1. 生成 Java 接口与同名的类型，以包含常量。

1. 此外包含接口常量的类型包含来自于实现的 Java 接口的所有常量。

1. 实现 Java 接口包含常量的所有类都获取新的嵌套的 InterfaceConsts 类型包含从所有实现的接口的常量。

1. *月成本*类型现已过时。


有关*android.os.Parcelable*接口，这意味着，现在将存在[ *Android.OS.Parcelable* ](https://developer.xamarin.com/api/type/Android.OS.Parcelable/)要包含的常量类型。 例如， [Parcelable.CONTENTS_FILE_DESCRIPTOR](https://developer.android.com/reference/android/os/Parcelable.html#CONTENTS_FILE_DESCRIPTOR)常量将作为绑定[ *Parcelable.ContentsFileDescriptor* ](https://developer.xamarin.com/api/field/Android.OS.Parcelable.ContentsFileDescriptor/)常量，而不是作为*ParcelableConsts.ContentsFileDescriptor*常量。

对于包含常量的实现包含其他接口但多个常量的接口，现在会生成所有常量的并集。 例如， [android.provider.MediaStore.Video.VideoColumns](https://developer.android.com/reference/android/provider/MediaStore.Video.VideoColumns.html)接口实现[android.provider.MediaStore.MediaColumns](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumns/)接口。 1.9，不过之前, [Android.Provider.MediaStore.Video.VideoColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+Video+VideoColumnsConsts/)类型都有无法访问上声明的常量[Android.Provider.MediaStore.MediaColumnsConsts](https://developer.xamarin.com/api/type/Android.Provider.MediaStore+MediaColumnsConsts/)。
因此，Java 表达式*MediaStore.Video.VideoColumns.TITLE*需要绑定到 C# 表达式*MediaStore.Video.MediaColumnsConsts.Title*这很难发现而无需读取很多 Java 文档。 在 1.9，等效的 C# 表达式将是[ *MediaStore.Video.VideoColumns.Title*](https://developer.xamarin.com/api/field/Android.Provider.MediaStore+Video+VideoColumns.Title/)。

此外，考虑[android.os.Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/)类型，实现 Java *Parcelable*接口。 因为它实现了接口，例如该接口上的所有常量都是"通过"绑定类型，可访问*Bundle.CONTENTS_FILE_DESCRIPTOR*是完全有效的 Java 表达式。
以前，要移植到此表达式C#将需要查看所有接口都实现从哪种类型中看到*CONTENTS_FILE_DESCRIPTOR*原来的位置。 从 Xamarin.Android 1.9 开始，实现包含常量的 Java 接口的类将具有嵌套*InterfaceConsts*类型，它将包含继承的接口的所有常量。 这将允许翻译*Bundle.CONTENTS_FILE_DESCRIPTOR*到[ *Bundle.InterfaceConsts.ContentsFileDescriptor*](https://developer.xamarin.com/api/field/Android.OS.Bundle+InterfaceConsts.ContentsFileDescriptor/)。

最后，类型与*月成本*如后缀*Android.OS.ParcelableConsts*现在已过时，新引入 InterfaceConsts 以外嵌套类型。 Xamarin.Android 3.0 中将删除它们。


## <a name="resources"></a>资源

可以作为应用程序中包含图像、 布局说明、 二进制 blob 和字符串字典[资源文件](https://developer.android.com/guide/topics/resources/providing-resources.html)。
各种 Android Api 旨在[操作的资源 Id](https://developer.android.com/guide/topics/resources/accessing-resources.html)而不是图像处理，字符串或二进制 blob 直接。

例如，示例 Android 应用，其中包含用户界面布局 ( `main.axml`)，国际化表字符串 ( `strings.xml`) 和一些图标 ( `drawable-*/icon.png`) 将保持其资源的应用程序的"资源"目录中：

    Resources/
        drawable-hdpi/
            icon.png

        drawable-ldpi/
            icon.png

        drawable-mdpi/
            icon.png

        layout/
            main.axml

        values/
            strings.xml

本机 Android Api 不直接使用文件名，但改为对资源 Id。 在编译时使用的资源的 Android 应用程序，生成系统将包分发的资源，并生成一个名为类`Resource`，其中包含为每个包含的资源的令牌。 例如，对于更高版本的资源布局，这是 R 类将公开：

```csharp
public class Resource {
    public class Drawable {
        public const int icon = 0x123;
    }

    public class Layout {
        public const int main = 0x456;
    }

    public class String {
        public const int first_string = 0xabc;
        public const int second_string = 0xbcd;
    }
}
```

然后，使用`Resource.Drawable.icon`引用`drawable/icon.png`文件，或`Resource.Layout.main`引用`layout/main.xml`文件，或`Resource.String.first_string`引用字典文件中的第一个字符串`values/strings.xml`。


## <a name="constants-and-enumerations"></a>常量和枚举

本机 Android Api 具有很多方法，接受或返回一个整数，它必须映射到常量字段来确定 int 的意义。 使用这些方法，用户不需要查阅文档以查看哪些常量是适当的值，这是不太理想。

例如，考虑[Activity.requestWindowFeature (int featureID)](https://developer.android.com/reference/android/app/Activity.html#requestWindowFeature(int))。

在这些情况下，我们尽力一起分组相关的常量是.NET 的枚举，并将重新映射要改为采用枚举的方法。
通过执行此操作，我们就能够提供 IntelliSense 所选内容的可能的值。

上面的示例中将变为：[Activity.RequestWindowFeature (WindowFeatures featureId)](https://developer.xamarin.com/api/member/Android.App.Activity.RequestWindowFeature/p/Android.Views.WindowFeatures/)。

请注意，这是一个大量的手动过程，以找出哪些常量一起，属于哪些 Api 使用这些常量。 请提交 bug 的会是更好地表示为一个枚举，在 API 中使用的任何常量。
