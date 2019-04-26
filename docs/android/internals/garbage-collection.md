---
title: 垃圾回收
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: c5a4247b2e10706014c9f92a487803e4a718c1a6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953719"
---
# <a name="garbage-collection"></a>垃圾回收

Xamarin.Android 使用 Mono[简单分代垃圾回收器](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)。 这是使用这两代标记和清除垃圾回收器和一个*大型对象空间*，两种类型的集合： 

-   次要集合 （收集 Gen0 堆） 
-   （收集 Gen1 和大型对象空间堆） 的主要集合。 

> [!NOTE]
> 通过显式集合没有[GC。Collect （)](xref:System.GC.Collect)的集合是否*按需*、 基于堆分配。 *这不是引用计数系统*; 对象*只要有任何未完成的引用将不会收集*，或当作用域已退出。 GC 将运行时运行次要堆的新分配的内存不足。 如果不存在分配，将不运行。


次要集合是低成本且频繁，并且用于收集最近已分配和死对象。 之后每隔几 MB 的已分配的对象执行了次要的集合。 可以通过调用来手动执行次要集合[GC。收集 (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

主要集合是成本高昂且频率较低，并且用于回收所有死对象。 内存已用尽当前的堆大小 （调整大小之前堆） 后，会执行主要集合。 主要集合可能会通过调用来手动执行[GC。收集 （）](xref:System.GC.Collect)或通过调用[GC。收集 (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_)使用参数[GC。MaxGeneration](xref:System.GC.MaxGeneration)。 



## <a name="cross-vm-object-collections"></a>跨 VM 对象集合

有三个类别的对象类型。

-   **托管对象**： 执行此操作的类型*不*继承自[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) ，例如[System.String](xref:System.String)。 
    这些是正常情况下收集的 GC。 

-   **Java 对象**:这在 Android 运行时的 VM，但不是会公开到 Mono VM 的 Java 类型。 这些是令人乏味，并且不会进一步讨论。 这些是正常情况下收集的 Android 运行时的 VM。 

-   **对等对象**： 类型实现[IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) ，例如所有[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)并[Java.Lang.Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/)子类。 这些类型的实例具有两个"halfs"*托管对等方*和一个*纯对等方*。 托管的对等方是实例的C#类。 本机的对等是 VM，在 Android 运行时中的 Java 类的实例和C# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/)属性包含对本机的对等的 JNI 全局引用。 


有两种类型的本机的对等方：

-   **框架对等方**:知道 nothing Xamarin.Android，例如"normal"Java 类型[android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)。

-   **用户对等方**:[Android 可调用包装器](~/android/platform/java-integration/working-with-jni.md)生成在生成时针对每个应用程序中存在的 Java.Lang.Object 子类。


因为有两个 Vm Xamarin.Android 进程内的，有两种类型的垃圾回收：

-   Android 运行时集合 
-   Mono 集合 

Android 运行时集合的运行正常，但需要特别注意： 的 JNI 全局引用视为 GC 根。 如果 JNI 全局因此，引用虚拟机对象，该对象保存到 Android 运行时*不能*收集一次，即使不符合回收的条件。

Mono 集合是有趣的发生位置。 通常情况下收集托管的对象。 对等对象将收集通过执行以下过程：

1.  Mono 集合的符合条件的所有对等对象具有使用 JNI 弱全局引用替换其 JNI 全局引用。 

2.  Android 运行时 VM GC 将调用。 可能会收集任何本机的对等实例。 

3.  检查在 (1) 中创建的 JNI 弱全局引用。 如果已收集的弱引用，则收集对等对象。 如果具有弱引用*不*已收集，然后弱引用替换为的 JNI 全局引用，并且不收集的对等对象。 注意： 在 API 14 +，这意味着，从返回的值`IJavaObject.Handle`垃圾回收后仍可能会更改。 

最终结果，所有这是对等对象的实例将 live，只要它通过以下任一方式引用的托管代码 (例如存储在中`static`变量) 或所引用的 Java 代码。 此外，将超出否则它们将扩展本机的对等方的生存期 live，如之前的本机的对等方和托管对等都是可回收不可回收的本机的对等方。


## <a name="object-cycles"></a>对象周期

在 Android 运行时和 Mono VM 内以逻辑方式存在对等对象。 例如， [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)托管的对等实例将具有相应[android.app.Activity](https://developer.android.com/reference/android/app/Activity.html) framework 对等 Java 实例。 继承的所有对象[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)需要具有两个 Vm 中的表示形式。 

具有两个 Vm 中表示形式的所有对象都具有与仅在单个 VM 中存在的对象相比进行了扩展的生存期 (如[ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601))。 调用[GC。收集](xref:System.GC.Collect)一定不会收集这些对象，因为 Xamarin.Android GC 需要确保收集它之前不引用由任一 VM 的对象。 

若要缩短对象生存期[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)应调用。 这将会手动"断开"两个 Vm，因为全局引用，从而允许以更快地进行收集的对象之间的对象上的连接。 


## <a name="automatic-collections"></a>自动集合

开头[版本 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0)，Xamarin.Android 会自动执行完整 GC gref 阈值时。 此阈值为 90%的已知的最大 grefs 平台：在仿真程序 (2000 最大) 上的 1800 grefs 和 46800 grefs 硬件 (最大 52000) 上。 *注意：* Xamarin.Android 仅计数通过创建 grefs [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)，并且将无法知道有关此过程中创建的任何其他 grefs。 这是启发式方法*仅*。 

执行自动收集时，类似于以下的消息将被打印到调试日志：

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

此匹配项是不确定的和不适当的时候 （例如中间图形呈现），可能会发生。 如果你看到此消息，可能想要执行的显式集合在其他位置，或者你可能想要尝试[减少对等对象的生存期](#Helping_the_GC)。 

## <a name="gc-bridge-options"></a>GC 桥选项

Xamarin.Android 提供了使用 Android 和 Android 运行时进行透明的内存管理。 实现作为 Mono 的垃圾回收器调用的扩展*GC 桥*。 

GC Bridge 的工作原理在 Mono 垃圾回收和图出哪些对等对象需要使用 Android 运行时堆验证其"实时性"过程。 GC 桥做出该决定，通过执行以下步骤 （按顺序）：

1.  引入到它们所代表的 Java 对象无法访问对等对象的 mono 引用关系图。 

2.  执行 Java GC。

3.  验证为真正死对象的对象。 

此复杂的过程是一种使类的子类`Java.Lang.Object`自由地引用任何对象; 它会删除任何限制的 Java 对象可以绑定到C#。 由于这种复杂性，桥过程可能会耗费大量资源，它可能会导致明显的暂停应用程序中。 如果应用程序很重要的暂停，值得调查以下三个 GC 桥实现之一： 

-   **Tarjan** -GC 桥的全新设计基于[Robert Tarjan 算法和向后引用传播](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm)。
    它有下我们模拟工作负荷的最佳性能，但它还具有更大的实验性代码共享。 

-   **新**-重大革新的原始代码修复的二次行为的两个实例，但保留核心算法 (基于[Kosaraju 的算法](https://en.wikipedia.org/wiki/Kosaraju's_algorithm)强查找连接组件)。 

-   **旧**的原始实现 （被视为最稳定的三个）。 这是应用程序应使用如果桥`GC_BRIDGE`暂停是可接受。 


最好地找出哪些 GC Bridge 的工作原理的唯一方法是通过应用程序中进行试验并分析输出。 有两种方法收集的数据进行基准测试： 

-   **启用日志记录**-启用日志记录 (中所述[配置](~/android/internals/garbage-collection.md)部分) 对于每个 GC 桥选项，然后捕获并比较每个设置的日志输出。 检查`GC`消息的每个选项; 具体而言，`GC_BRIDGE`消息。 暂停最多 150ms年为非交互式应用程序可承受，但上面非常交互式应用程序 （如游戏） 60 毫秒暂停问题。 

-   **启用桥记帐**-桥记帐将显示指向桥过程中涉及的每个对象的对象的平均成本。 按大小排序此信息将提供什么具有多余的对象的最大容量提示。 


若要指定将哪个`GC_BRIDGE`应使用应用程序选项，则传递`bridge-implementation=old`，`bridge-implementation=new`或`bridge-implementation=tarjan`到`MONO_GC_PARAMS`环境变量，例如： 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

默认设置是**Tarjan**。 如果找到一个回归，您可能会发现有必要将此选项设置为**旧**。 此外，您可以选择使用更稳定**旧**选项时如果**Tarjan**不会生成中的性能改进。 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>帮助 GC

有多种方法来帮助 GC 以减少内存使用和收集时间。



### <a name="disposing-of-peer-instances"></a>对等方实例的释放

GC 具有不完整的视图的过程和可能不会运行时内存较少的因为 GC 不知道该内存不足。 

例如，实例[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)类型或派生的类型有至少 20 个字节的大小 (变动，恕不另行通知，等等，等等。)。 
[托管可调用包装器](~/android/internals/architecture.md)不要将添加其他实例成员，因此后[Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/)实例引用的 10MB blob 的内存，Xamarin.Android 的 gc 便不会知道&ndash;GC将看到的 20 字节对象，将不能以确定其与 Android 使 10 MB 的内存保持活动状态的运行时分配的对象。 

很频繁地需要帮助 GC。 遗憾的是， *GC。AddMemoryPressure()* 和*GC。RemoveMemoryPressure()* 不受支持，因此，如果您*知道*您只需释放大型 Java 分配对象关系图可能需要手动调用[GC。Collect （)](xref:System.GC.Collect)提示符 GC 释放 Java 端到内存，也可以显式释放*Java.Lang.Object*子类，重大托管的可调用包装器和 Java 实例之间的映射。 有关示例，请参阅[Bug 1084](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6)。 


> [!NOTE]
> 您必须是*极*释放时请小心`Java.Lang.Object`子类实例。

为了尽量减少内存损坏的可能性，应遵守以下原则调用时`Dispose()`。


#### <a name="sharing-between-multiple-threads"></a>多个线程间共享

如果*Java 或托管*实例可能会共享多个线程间*不应`Dispose()`d*，**曾经**。 例如， [`Typeface.Create()`](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) 
可能会返回*缓存的实例*。 如果多个线程提供相同的参数，他们将获得*同一*实例。 因此，`Dispose()`运算结果`Typeface`实例从一个线程可能会使其他线程，这可能会导致`ArgumentException`s 从`JNIEnv.CallVoidMethod()`（及其他） 因为实例已从另一个线程释放。 


#### <a name="disposing-bound-java-types"></a>释放绑定的 Java 类型

如果绑定的 Java 类型的实例，实例可释放的*只要*实例不能重复使用从托管代码*和*Java 实例不能在线程 （请参阅以前之间共享`Typeface.Create()`讨论)。 （在进行此决定可能比较困难。）下一次 Java 实例进入托管代码中，*新*将为其创建包装器。 

谈到绘图和其他大量资源的实例，这是十分有用：

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

以上是安全因为对等方的[Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) Framework 对等方，将引用返回*不*用户对等。 `Dispose()`调用的末尾`using`块将中断之间的托管关系[Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/)和 framework [Drawable](https://developer.android.com/reference/android/graphics/drawable/Drawable.html)实例，从而使 Java 实例收集只要 Android 运行时需要。 这一点*不*为安全起见，如果用户对等引用对等实例; 此处我们使用"external"信息*知道*的`Drawable`用户对等方，不能引用，因此`Dispose()`调用是安全的。 


#### <a name="disposing-other-types"></a>释放其他类型 

如果该实例所引用的不是 Java 类型的绑定的类型 (如自定义`Activity`)，**不要**调用`Dispose()`除非您*知道*没有 Java 代码将对的调用重写的方法实例。 如果不这样做会导致[ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls)。 

例如，如果您有一个自定义单击侦听器：

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

您*不应*释放此实例中，如 Java 将尝试在将来调用它的方法：

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>使用显式检查，以避免异常

如果已实现[Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/)重载方法，应避免触及涉及 JNI 的对象。 执行此操作可能会创建*双 dispose*就可以将代码移植到 （严重） 的情况下尝试访问已被垃圾回收的基础 Java 对象。 执行此操作将生成类似于以下异常： 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

这种情况通常第一个对象的 dispose 将导致成员，才能成为为 null，且然后此 null 成员上的后续访问尝试会导致引发异常时发生。 具体而言，该对象的`Handle`（该链接的托管的实例到其基础 Java 实例） 会在第一个 dispose 上失效，但仍将托管的代码尝试访问此基础 Java 实例，即使它不再可用 （请参阅[托管可调用包装器](~/android/internals/architecture.md#Managed_Callable_Wrappers)Java 实例和托管的实例之间的映射的详细信息)。 

若要避免此异常的好方法是显式验证在你`Dispose`托管的实例与基础 Java 实例之间的映射是否仍然有效; 这就是方法，检查以查看是否对象的`Handle`为 null (`IntPtr.Zero`)然后才能访问其成员。 例如，以下`Dispose`方法访问`childViews`对象： 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);
        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

如果初始 dispose 通过的原因`childViews`具有无效`Handle`，则`for`循环访问将引发`ArgumentException`。 通过添加显式`Handle`为 null 之前先检查`childViews`访问以下`Dispose`方法可以防止异常抛出： 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);

        // Check for a null handle:
        if (this.childViews.Handle == IntPtr.Zero)
            return;

        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```


### <a name="reduce-referenced-instances"></a>减少被引用的实例

每当的实例`Java.Lang.Object`类型或子类 GC，整个过程中扫描*对象图*必须还扫描实例表示。 在对象图是"根实例"引用的对象实例的组*加上*所有内容根实例引用的引用，以递归方式。 

请考虑以下类：

```csharp
class BadActivity : Activity {

    private List<string> strings;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

时`BadActivity`是构造的在对象图将包含 10004 实例 (1 x `BadActivity`，1 x `strings`，1 x`string[]`持有`strings`，10000 x 字符串实例)，*所有*的将需要将每当扫描`BadActivity`扫描实例。 

这可能会产生不利影响上你收集的时间，从而提高了 GC 暂停时间。 

可帮助通过 GC*减少*这由用户对等实例取得 root 权限的对象图的大小。 在上述示例中，这可以通过移动`BadActivity.strings`到单独的类，其中不从 Java.Lang.Object 继承： 

```csharp
class HiddenReference<T> {

    static Dictionary<int, T> table = new Dictionary<int, T> ();
    static int idgen = 0;

    int id;

    public HiddenReference ()
    {
        lock (table) {
            id = idgen ++;
        }
    }

    ~HiddenReference ()
    {
        lock (table) {
            table.Remove (id);
        }
    }

    public T Value {
        get { lock (table) { return table [id]; } }
        set { lock (table) { table [id] = value; } }
    }
}

class BetterActivity : Activity {

    HiddenReference<List<string>> strings = new HiddenReference<List<string>>();

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```


## <a name="minor-collections"></a>次要集合

可以通过调用来手动执行次要集合[GC。Collect(0)](xref:System.GC.Collect)。 次要集合是低 （相比于主要的集合），但执行具有显著固定成本，因此你不想要触发它们过于频繁，应具有的暂停时间为几毫秒。 

如果你的应用程序已在其中反复完成相同的操作的"任务周期"，可能会建议你手动任务周期结束后执行一次。 示例工作周期包括： 

-  单个游戏帧的呈现循环。
-  与给定的应用对话框 （打开、 填充、 关闭） 的整个交互 
-  一组的网络请求以刷新/同步应用数据。



## <a name="major-collections"></a>主要的集合

主要集合可能会通过调用来手动执行[GC。Collect （)](xref:System.GC.Collect)或`GC.Collect(GC.MaxGeneration)`。 

它们应该很少，执行并可能会暂停时遇到的第二个 Android 风格的设备上收集 512MB 堆时。 

主要集合应仅手动调用，如果有过： 

-   耗时较长的值班末尾周期和较长的暂停时不会向用户出现问题。 

-   中被重写[Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/)方法。 



## <a name="diagnostics"></a>诊断

若要跟踪时创建和销毁全局引用，可以设置[debug.mono.log](~/android/troubleshooting/index.md)系统属性以包含[ *gref* ](~/android/troubleshooting/index.md)和/或[ *gc*](~/android/troubleshooting/index.md)。 



## <a name="configuration"></a>配置

可以通过设置配置 Xamarin.Android 垃圾回收器`MONO_GC_PARAMS`环境变量。 可能的生成操作设置环境变量[AndroidEnvironment](~/android/deploy-test/environment.md)。

`MONO_GC_PARAMS`环境变量是以逗号分隔列表的以下参数： 

-   `nursery-size` = *大小*:设置在小的大小。 大小以字节为单位指定，并且必须是 2 的幂。 后缀`k`，`m`和`g`可用于指定千、 庞大和千兆字节为单位，分别。 小堆是第一代 （两个）。 更大的小堆通常会提高程序的速度，但显然会使用更多的内存。 默认值小堆大小为 512 kb。 

-   `soft-heap-limit` = *大小*:目标最大托管应用程序的内存占用情况。 当内存使用情况低于指定的值时，GC 进行了优化的执行时间 （更少的集合）。 
    超出此限制，GC 进行了优化的内存使用 （更多集合）。 

-   `evacuation-threshold` = *阈值*:以百分比为单位设置疏散阈值。 值必须是 0 到 100 范围内的整数。 默认值为 66。 如果集合的扫描阶段找到的特定堆块类型的占用小于此百分比，它将执行下一步的主要集合中的块类型，从而还原到接近 100%的空间使用量的复制集合。 值为 0 将关闭疏散。 

-   `bridge-implementation` = *桥接实现*:这会设置 GC 桥选项，可帮助解决 GC 性能问题。 有三个可能值：*旧*，*新*， *tarjan*。

-   `bridge-require-precise-merge`：网桥包含一种优化这可能，在少数情况下，会使对象可 Tarjan 后首次将成为垃圾收集一个 GC。 包括此选项会禁用该优化，从而使 Gc 更可预测，但可能会较慢。

例如，若要配置 GC 堆大小限制为 128 MB，添加一个新的文件到你的项目**生成操作**的`AndroidEnvironment`的内容： 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
