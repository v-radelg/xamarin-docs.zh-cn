---
title: 垃圾回收
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 62560d97a2e85a6045e419f0c0602a375f5a2a75
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027887"
---
# <a name="garbage-collection"></a>垃圾回收

Xamarin 使用 Mono 的[简单代垃圾回收器](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)。 这是一个带有两个代和*大型对象空间*的标记和扫描垃圾回收器，具有两种类型的集合： 

- 次要集合（收集 Gen0 堆） 
- 主要集合（收集 Gen1 和大型对象空间堆）。 

> [!NOTE]
> 如果没有通过 GC 进行显式收集[。](xref:System.GC.Collect)根据堆分配，*按需*收集（）集合。 *这不是引用计数系统*;如果没有未完成的引用或某个范围已退出，*则不会立即收集*对象。 当次堆内存不足以用于新分配时，GC 将运行。 如果没有分配，则不会运行。

次要集合开销较低，并且用于收集最近分配和死的对象。 次要集合在每隔几 MB 的分配对象之后执行。 次要集合可能是通过调用 GC 手动执行的[。Collect （0）](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

主要集合开销较低，并且用于回收所有死对象。 为当前堆大小（调整堆大小之前）耗尽内存后，将执行主要集合。 主要集合可以通过调用 GC 手动执行[。收集（）](xref:System.GC.Collect)或通过调用[GC。用参数 GC 收集（int）](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) [。MaxGeneration](xref:System.GC.MaxGeneration)。 

## <a name="cross-vm-object-collections"></a>跨 VM 对象集合

有三种类别的对象类型。

- **托管对象**：*不*从[.java](xref:Java.Lang.Object)派生的类型，例如[system.object](xref:System.String)。 
    它们通常由 GC 收集。 

- **Java 对象**：位于 Android 运行时 VM 内但不会向 Mono VM 公开的 Java 类型。 这些都是镗孔的，不会进一步讨论。 它们通常由 Android 运行时 VM 收集。 

- **对等对象**：实现[IJavaObject](xref:Android.Runtime.IJavaObject) [的类型（如所有](xref:Java.Lang.Object) [java.lang.throwable](xref:Java.Lang.Throwable)子类）。 这些类型的实例具有两个 "halfs"*托管对等*方和*本机对等节点*。 托管对等是C#类的实例。 本机对等是 Android 运行时 VM 内 Java 类的实例， C# [IJavaObject](xref:Android.Runtime.IJavaObject.Handle)属性包含对本机对等方的 JNI 全局引用。 

本机对等机有两种类型：

- **框架对等**："正常" Java 类型，不知道 Xamarin 的任何内容，例如  [android. Context](xref:Android.Content.Context)。

- **用户对等**： 为应用程序中出现的每个 .Java 子类生成时生成的[Android 可调用包装](~/android/platform/java-integration/working-with-jni.md)。

Xamarin Android 进程中有两个虚拟机，有两种类型的垃圾回收：

- Android 运行时集合 
- Mono 集合 

Android 运行时集合正常运行，但有一个注意事项： JNI 全局引用被视为 GC 根。 因此，如果有一个 JNI 的全局引用持有 Android 运行时 VM 对象，则*无法*收集该对象，即使该对象有资格进行收集也是如此。

Mono 集合就是有趣之处。 通常收集托管的对象。 通过执行以下过程收集对等对象：

1. 符合 Mono 收集条件的所有对等对象的 JNI 全局引用均替换为 JNI 弱全局引用。 

2. 调用 Android 运行时 VM GC。 可以收集任何本机对等实例。 

3. 检查在（1）中创建的 JNI 弱全局引用。 如果已收集弱引用，则将收集对等对象。 如果*尚未*收集弱引用，则弱引用将替换为 JNI 全局引用，并且不会收集对等对象。 注意：在 API 14 + 上，这意味着从 `IJavaObject.Handle` 返回的值可能会在 GC 后发生更改。 

所有此类结果的最终结果是对等对象的实例将在其被托管代码（例如，存储在 `static` 变量中）引用或由 Java 代码引用的情况下被实时。 此外，本机对等方的生存期将会超出它们的实际生存期，因为在本机对等节点和托管对等节点都可回收之前，不会对其进行可回收。

## <a name="object-cycles"></a>对象循环

对等对象在公共上出现在 Android 运行时和 Mono VM 的中。 例如，一个[android. 活动](xref:Android.App.Activity)托管的对等实例将具有相应的 " [android](https://developer.android.com/reference/android/app/Activity.html) "。 从[.java](xref:Java.Lang.Object)继承的所有对象都可以在这两个 vm 中具有表示形式。 

在两个 Vm 中都具有表示形式的所有对象都具有与仅在单个 VM （如[`System.Collections.Generic.List<int>`](xref:System.Collections.Generic.List%601)）中存在的对象相比的生存期。 调用[GC。收集](xref:System.GC.Collect)并不需要收集这些对象，因为在收集对象之前，Xamarin 需要确保两个 VM 未引用该对象。 

若要缩短对象生存期，应调用[.java （）](xref:Java.Lang.Object.Dispose) 。 这将通过释放全局引用来在两个 Vm 之间手动 "服务器" 连接，从而使对象更快收集。 

## <a name="automatic-collections"></a>自动集合

从[Release 4.1.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/mono_for_android_4/mono_for_android_4.1.0/index.md)开始，当超过 gref 阈值时，Xamarin 会自动执行完全 GC。 此阈值为该平台的已知最大 grefs 的90%：1800 grefs （2000 max）和 46800 grefs on 硬件（最大52000）。 *注意：* Xamarin 只计算[JNIEnv](xref:Android.Runtime.JNIEnv)创建的 grefs，并不知道在该过程中创建的任何其他 grefs。 这*只*是一种试探法。 

当执行自动收集时，会将类似于以下内容的消息输出到调试日志：

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

出现这种情况是不确定的，并且可能会在不合时宜（例如，在图形呈现的中间）发生。 如果看到此消息，则可能需要在其他位置执行显式集合，或者可能想要[减少对等对象的生存期](#Helping_the_GC)。 

## <a name="gc-bridge-options"></a>GC Bridge 选项

Xamarin 通过 Android 和 Android 运行时提供透明的内存管理。 它实现为对 Mono 垃圾回收器（称为*GC Bridge*）的扩展。 

GC 桥在 Mono 垃圾回收过程中起作用，并确定哪些对等对象需要使用 Android 运行时堆来验证其 "活动"。 GC Bridge 通过执行以下步骤来做出此决定（按顺序）：

1. 将不可访问的对等对象的 mono 引用图引入它们所表示的 Java 对象。 

2. 执行 Java GC。

3. 验证哪些对象确实处于死锁。 

此复杂过程可让 `Java.Lang.Object` 的子类自由引用任何对象;它删除了可以绑定到C#的 Java 对象的任何限制。 由于这种复杂性，桥过程可能非常昂贵，并且可能会导致应用程序中出现明显的暂停。 如果应用程序遇到重大暂停，则有必要调查以下三个 GC Bridge 实现之一： 

- **Tarjan** -基于[Robert Tarjan 算法和向后引用传播](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm)的 GC Bridge 的全新设计。
    它在我们的模拟工作负载下具有最佳性能，但它也具有更大的实验性代码份额。 

- **新增**-对原始代码的主要修正，修复二次行为的两个实例，但保留核心算法（基于[Kosaraju 的算法](https://en.wikipedia.org/wiki/Kosaraju's_algorithm)来寻找强连接的组件）。 

- **旧**-原始实现（被视为最稳定的三个）。 这是在可接受 `GC_BRIDGE` 暂停的情况下，应用程序应使用的桥。 

确定哪一个 GC 桥的唯一方法是通过试验应用程序并分析输出。 可以通过两种方法来收集用于基准测试的数据： 

- 为每个 GC Bridge 选项**启用日志**记录-启用日志记录（如 "[配置](~/android/internals/garbage-collection.md)" 部分中的说明），然后捕获并比较每个设置的日志输出。 检查每个选项的 `GC` 消息;具体而言，`GC_BRIDGE` 消息。 不能为非交互式应用程序暂停最多150ms，但对于非常交互式的应用程序（如游戏），会暂停上述60毫秒。 

- **启用桥记帐**-桥核算将显示桥接过程中涉及的每个对象所指向的对象的平均成本。 如果按大小对此信息进行排序，则会提供有关保留最大数量的额外对象的提示。 

若要指定应用程序应使用哪个 `GC_BRIDGE` 选项，请将 `bridge-implementation=old`、`bridge-implementation=new` 或 `bridge-implementation=tarjan` 传递到 `MONO_GC_PARAMS` 环境变量，例如： 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

默认设置为**Tarjan**。 如果发现回归，可能会发现需要将此选项设置为 "**旧**"。 此外，如果**Tarjan**不能提高性能，则可以选择使用更稳定的**旧**选项。 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>帮助 GC

有多种方法可帮助 GC 减少内存使用和收集时间。

### <a name="disposing-of-peer-instances"></a>释放对等实例

GC 有一个不完整的进程视图，当内存不足时可能不会运行，因为 GC 不知道内存不足。 

例如， [.java](xref:Java.Lang.Object)类型或派生类型的实例的大小至少为20个字节（如有更改，恕不另行通知，等等）。 
[托管的可调用包装](~/android/internals/architecture.md)器不会添加其他实例成员，因此，当你具有引用内存的 10mb Blob 的[android. Bitmap](xref:Android.Graphics.Bitmap)实例时，Xamarin 的 gc 将无法知道 &ndash; GC 将看到一个20字节的对象，并将无法确定它是否链接到了 Android 运行时分配的对象，这些对象的内存保持为10MB。 

经常需要帮助 GC。 遗憾的是， *GC。AddMemoryPressure （）* 和*GC。不支持 RemoveMemoryPressure （）* ，因此如果你*知道*刚刚释放了一个大型 Java 分配的对象图，则可能需要手动调用[GC。收集（）](xref:System.GC.Collect)以提示 GC 释放 Java 端内存，或者可以显式释放 *.java*子类，从而中断托管可调用包装器和 Java 实例之间的映射。 例如，请参阅[Bug 1084](https://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6)。 

> [!NOTE]
> 释放 `Java.Lang.Object` 子类实例时 *，必须格外小心。*

若要最大程度地减少内存损坏的可能性，请在调用 `Dispose()`时遵循以下准则。

#### <a name="sharing-between-multiple-threads"></a>在多个线程之间共享

如果可以在多个线程之间共享*Java 或托管*实例，*则不应将其 `Dispose()`d* **。** 例如， [`Typeface.Create()`](xref:Android.Graphics.Typeface.Create*) 
可能返回*缓存的实例*。 如果多个线程提供相同的参数，则它们将获得*相同*的实例。 因此，从一个线程 `Dispose()`的 `Typeface` 实例的 ing 可能会使其他线程失效，这可能会导致 `JNIEnv.CallVoidMethod()` 中的 `ArgumentException`秒，因为该实例是从另一个线程释放的。 

#### <a name="disposing-bound-java-types"></a>释放绑定的 Java 类型

如果该实例为绑定 Java 类型，则*只要*实例不会从托管代码中重复使用，*并且*不能在线程间共享 Java 实例（请参阅上一 `Typeface.Create()` 讨论），就可以释放该实例。 （做出此决定可能会很困难。）下次 Java 实例进入托管代码时，将为其创建*新*的包装器。 

当涉及到绘图和其他资源繁重的实例时，这通常很有用：

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

上述是安全的，因为[CreateFromPath （）](xref:Android.Graphics.Drawables.Drawable.CreateFromPath*)返回的对等将引用框架对等方，*而不*是用户对等方。 `using` 块末尾的 `Dispose()` 调用将中断托管的[可绘制](xref:Android.Graphics.Drawables.Drawable)和框架[可绘制](https://developer.android.com/reference/android/graphics/drawable/Drawable.html)实例之间的关系, 从而允许在 Android 运行时需要时立即收集 Java 实例。 如果对等实例引用用户对等方实例，这将*不*安全;这里，我们使用 "外部" 信息来*了解*`Drawable` 无法引用用户对等方，因此 `Dispose()` 调用是安全的。 

#### <a name="disposing-other-types"></a>释放其他类型 

如果该实例引用了不是 Java 类型（如自定义 `Activity`）的绑定的类型，除非您*知道*没有 java 代码调用该实例上的重写方法，否则**不**会调用 `Dispose()`。 否则，会导致[`NotSupportedException`](~/android/internals/architecture.md#Premature_Dispose_Calls)。 

例如，如果你有一个自定义单击侦听器：

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

*不应*释放此实例，因为 Java 会在将来尝试调用它的方法：

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```

#### <a name="using-explicit-checks-to-avoid-exceptions"></a>使用显式检查来避免异常

如果已实现了 JNI[重载方法](xref:Java.Lang.Object.Dispose*)，请避免触及涉及到的对象。 这样做可能会产生*双重释放*的情况，使您的代码（严重）尝试访问已经过垃圾回收的基础 Java 对象。 这样做会产生类似于下面的异常： 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

这种情况通常在第一次释放对象导致成员变为 null 时出现，然后在此 null 成员上进行后续访问尝试会导致引发异常。 具体而言，对象的 `Handle` （它将托管实例链接到其基础 Java 实例）在第一个 dispose 上失效，但托管代码仍尝试访问此基础 Java 实例，即使该实例不再可用（请参阅[托管的可调用包装](~/android/internals/architecture.md#Managed_Callable_Wrappers)器，详细了解 Java 实例与托管实例之间的映射。 

避免此异常的一个好方法是在 `Dispose` 方法中显式验证托管实例和基础 Java 实例之间的映射是否仍然有效;也就是说，查看对象的 `Handle` 在访问其成员之前是否为 null （`IntPtr.Zero`）。 例如，下面的 `Dispose` 方法访问 `childViews` 对象： 

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

如果初始 dispose pass 导致 `childViews` 具有无效 `Handle`，则 `for` 循环访问将引发 `ArgumentException`。 通过在第一次 `childViews` 访问之前添加显式 `Handle` null 检查，以下 `Dispose` 方法可防止发生此异常： 

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

### <a name="reduce-referenced-instances"></a>减少引用的实例

每当在 GC 期间扫描 `Java.Lang.Object` 类型或子类的实例时，还必须扫描该实例所引用的整个*对象关系图*。 对象图是 "根实例" 引用的对象实例的集合，*以及*由根实例引用的内容所引用的所有内容。 

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

构造 `BadActivity` 时，对象图将包含10004实例（1x `BadActivity`、1x `strings`、1x `string[]` 由 `strings`、10000x 字符串实例保存），只要扫描 `BadActivity` 实例，*所有*这些实例就都需要扫描。 

这可能会对收集时间造成不利影响，导致 GC 暂停时间增加。 

可以通过*减少*对象图的大小，该对象图以用户对等实例为根来帮助 GC。 在上面的示例中，可以通过将 `BadActivity.strings` 移到不从 .Java 继承的单独类中来完成此操作： 

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

次要集合可能是通过调用 GC 手动执行的[。Collect （0）](xref:System.GC.Collect)。 次要集合的开销较低（与主要集合相比），但却有很大的固定开销，因此您不会过于频繁地触发这些集合，而且会有几毫秒的暂停时间。 

如果你的应用程序有一个 "责任周期"，在这种情况下，将同时执行相同的操作，因此，在工作周期结束后，最好手动执行次要收集。 例如，责任周期包括： 

- 单个游戏帧的呈现循环。
- 与给定应用对话框（打开、填充、关闭）的整体交互 
- 一组用于刷新/同步应用数据的网络请求。

## <a name="major-collections"></a>主要集合

主要集合可以通过调用 GC 手动执行[。Collect （）](xref:System.GC.Collect)或 `GC.Collect(GC.MaxGeneration)`。 

它们应该很少执行，并且在收集512MB 堆时，Android 样式设备上可能会有一秒的暂停时间。 

如果需要，只能手动调用主要集合： 

- 在漫长的工作周期结束时，如果时间较长，则不会给用户带来问题。 

- 在重写的[OnLowMemory （）](xref:Android.App.Activity.OnLowMemory)方法内。 

## <a name="diagnostics"></a>诊断

若要跟踪创建和销毁全局引用的时间, 可以将 [debug.mono.log](~/android/troubleshooting/index.md) 系统属性设置为包含 [*gref*](~/android/troubleshooting/index.md) 和/或 [*gc*](~/android/troubleshooting/index.md) 

## <a name="configuration"></a>配置

可以通过设置 `MONO_GC_PARAMS` 环境变量来配置 Xamarin 垃圾回收器。 可以使用[AndroidEnvironment](~/android/deploy-test/environment.md)的生成操作来设置环境变量。

`MONO_GC_PARAMS` 环境变量是以下参数的逗号分隔列表： 

- `nursery-size` = *大小*：设置小堆的大小。 大小以字节为单位指定，并且必须是2的幂。 可以使用 `k`、`m` 和 `g` 的后缀分别指定每个千兆字节和兆字节。 小堆是第一代（共2个）。 较大的小堆通常会提高程序的速度，但会明显地使用更多的内存。 默认小堆大小 512 kb。 

- `soft-heap-limit` = *大小*：应用的目标最大托管内存消耗。 当内存使用小于指定值时，GC 将针对执行时间进行优化（回收量更少）。 
    此限制高于此限制，为内存使用量（更多集合）优化 GC。 

- `evacuation-threshold` = *阈值*：设置疏散阈值（以百分比表示）。 该值必须是0到100范围内的整数。 默认值为66。 如果集合的扫描阶段发现特定堆块类型的占用量小于此百分比，它将在下一个主要集合中为该块类型执行复制集合，从而将占用量降低到100%。 值0会关闭疏散。 

-  = *桥实现*`bridge-implementation`：这将设置 GC Bridge 选项以帮助解决 GC 性能问题。 有三个可能的值： *old* 、 *new* 、 *tarjan*。

- `bridge-require-precise-merge`：Tarjan 桥包含一种优化，在极少数情况下，它可能会导致对象在第一次变为垃圾后被回收。 如果包括此选项，则将禁用此优化，从而使 Gc 更可预测但可能更慢。

例如，若要将 GC 配置为使堆大小限制为128MB，请将一个新文件添加到项目中，并将 `AndroidEnvironment` 的**生成操作**添加到内容： 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```
