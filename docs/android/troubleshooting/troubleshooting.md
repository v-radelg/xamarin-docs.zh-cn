---
title: 疑难解答指南
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: f57736238fc01d865d7655d72da427e2c18dca59
ms.sourcegitcommit: e02b725e48af867eb2c53ac9e17805f778fbbc8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2019
ms.locfileid: "68757247"
---
# <a name="troubleshooting-tips"></a>疑难解答指南


## <a name="getting-diagnostic-information"></a>获取诊断信息

在追踪各种 bug 时, Xamarin 有一些需要注意的地方。
这些问题包括：

1.  诊断 MSBuild 输出。
2.  设备部署日志。
3.  Android 调试日志输出。


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>诊断 MSBuild 输出

诊断 MSBuild 可以包含与包生成相关的其他信息, 还可以包含某些包部署信息。

若要在 Visual Studio 中启用诊断 MSBuild 输出：

1.  单击 "**工具" > 选项 ...**
2.  在左侧树视图中, 选择 "**项目和解决方案" > "生成和运行**"
3.  在右侧面板中, 将 "MSBuild 生成输出详细级别" 下拉列表设置为 "诊断"
4.  单击“确定”
5.  清除并重新生成包。
6.  诊断输出显示在 "输出" 面板中。


在 Visual Studio for Mac/OS X 中启用诊断 MSBuild 输出:

1.  单击**Visual Studio for Mac > 首选项 ...**
2.  在左侧树视图中, 选择 "**项目 > 生成**"
3.  在右侧面板中, 将 "日志详细级别" 下拉箭头设置为 "诊断"
4.  单击“确定”
5.  重启 Visual Studio for Mac
6.  清除并重新生成包。
7.  通过单击 "生成输出" 按钮, 可在 "错误" 面板中查看诊断输出 ( **> > 错误**)。




## <a name="device-deployment-logs"></a>设备部署日志

若要在 Visual Studio 中启用设备部署日志记录:

1.  **工具 > 选项 。** >
2.  在左侧树视图中, 选择 " **Xamarin > Android 设置**"
3.  在右侧面板中, 启用 [X] "**调试日志记录" (将 monodroid 写入桌面)** 复选框。
4.  日志消息将写入桌面上的 monodroid 文件。


Visual Studio for Mac 始终写入设备部署日志。 找到这些方法稍微难些;每日创建一个*AndroidUtils*日志文件, 部署发生的时间为, 例如:**AndroidTools-2012-10-24 _12-35-45. 日志**。

-  在 Windows 上, 将日志文件写入`%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`。
-  在 OS X 上, 将日志文件写入`$HOME/Library/Logs/XamarinStudio-{VERSION}`。




## <a name="android-debug-log-output"></a>Android 调试日志输出

Android 会将多条消息写入[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)。
Xamarin 使用 Android 系统属性来控制向 Android 调试日志生成附加消息。 Android 系统属性可通过[Android Debug Bridge (adb)](https://developer.android.com/guide/developing/tools/adb.html)中的*setprop*命令进行设置:

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

系统属性是在进程启动过程中读取的, 因此必须在启动应用程序之前设置, 或者必须在系统属性更改后重新启动应用程序。



### <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系统属性

Xamarin 支持以下系统属性:

-   *调试 mono*:如果非空字符串, 则它等效于`*mono-debug*`。

-   *debug.exe. env*:在应用程序启动过程中 *|* , mono 初始化*之前*要导出的环境变量 ("") 列表。 这允许设置控制 mono 日志记录的环境变量。

    - *说明*：由于值是 *|* 的隔离，该值必须包含额外级别的用引号括起来，作为\`*adb shell*\`命令将删除引号引起来的一组。

    - *说明*：Android 系统属性值的长度不能超过92个字符。

    - 示例:

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *调试 mono 日志*:用逗号分隔的 (" *,* ") 组件列表, 这些组件应将其他消息打印到 Android 调试日志。 默认情况下, 不设置任何内容。 组件包括:

    -   *全部*:打印所有消息
    -   *gc*:打印 GC 相关的消息。
    -   *gref*:Print (弱、全局) 引用分配和释放消息。
    -   *lref*:打印本地引用分配和释放消息。

    *注意*: 这些内容*非常*详细。 除非确实需要, 否则不要启用。

-   *debug . trace*:允许设置[mono 跟踪](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`设置。

## <a name="deleting-bin-and-obj"></a>删除`bin`和`obj`

在过去的某个情况下, Xamarin 曾遇到过, 如:

- 遇到异常生成或运行时错误。
- 你`Clean` `bin` `obj` 、 `Rebuild`或手动删除你的和目录。
- 问题消失。

由于这些问题会影响开发人员的工作效率, 因此我们非常重视这些问题。

如果出现这样的问题:

1. 作精神说明。 你的项目进入此状态的最后一个操作是什么？
1. 保存当前的生成日志。 再次尝试生成, 并记录[诊断生成日志](#diagnostic-msbuild-output)。
1. 提交[bug 报告][bug]。

删除`bin` 和`obj`目录之前, 请将它们压缩并保存, 以便以后在需要时进行诊断。 您可能只是`Clean`您的 Xamarin Android 应用程序项目, 才能让一切恢复正常工作。

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin 无法解析 ValueTuple

发生此错误的原因是与 Visual Studio 不兼容。

- **Visual Studio 2017 Update 1**(版本15.1 或更低版本) 仅与**ValueTuple NuGet 4.3.0** (或更早版本) 兼容。

- **Visual Studio 2017 Update 2**(版本15.2 或更高版本) 仅与**ValueTuple NuGet 4.3.1** (或更高版本) 兼容。

请选择与你的 Visual Studio 2017 安装相对应的正确 ValueTuple NuGet。


## <a name="gc-messages"></a>GC 消息

可以通过将 "debug.exe" 系统属性设置为包含 gc 的值来查看 GC 组件消息。

Gc 消息是在 GC 执行时生成的, 它提供有关垃圾回收的工作量的信息:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

可以通过将`MONO_LOG_LEVEL`环境变量设置为来`debug`生成其他 GC 信息 (如计时信息):

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

这将导致 (许多) 附加 Mono 消息, 其中包括以下三个结果:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

在消息中, `num-objects`是此`num_hash_entries`传递正在考虑的桥对象的数量, 是此桥代码调用期间处理的对象数。 `GC_BRIDGE`

`total` `bridge`在和消息`GC_MAJOR`中, 是指处于暂停状态的时间量 (没有线程正在执行), 而是桥处理代码 (用于处理 Java VM) 所花费的时间。 `GC_MINOR` 发生桥处理时, 世界*不*会暂停。

 *通常*, 的值`num_hash_entries`越大, 收集所需的时间`bridge`就越长, 收集`total`时间越大, 收集的时间越大。



## <a name="global-reference-messages"></a>全局引用消息

若要启用全局引用 loggig (GREF) 日志记录, *debug.exe*系统属性必须包含*GREF*, 例如:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin 使用 Android 全局引用来提供 Java 实例与关联的托管实例之间的映射, 与调用 Java 方法时需要向 java 提供 Java 实例的情况一样。

遗憾的是, Android 仿真程序一次只允许2000全局引用。 硬件的最大限制为52000全局引用。 在模拟器上运行应用程序时, 下限可能会有问题, 因此 , 知道实例的来源可能会非常有用。

 *注意*: 全局引用计数是 Xamarin 的内部, 并且不 (也不能) 包含已加载到进程中的其他本机库所执行的全局引用。 将全局引用计数用作估算值。

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

有四个结果消息:

-  全局引用创建: 这些是以 *+ g +* 开头的行, 并将为创建代码路径提供堆栈跟踪。
-  全局引用析构: 这些行以 *-g*开头, 可以为全局引用的代码路径释放提供堆栈跟踪。 如果 GC 正在释放 gref, 则不会提供任何堆栈跟踪。
-  弱全局引用创建: 这是以 *+ w +* 开头的行。
-  弱全局引用析构: 这是以 *-w*开头的行。


在所有消息中, *grefc*值是 xamarin 创建的全局引用计数, 而*Grefwc*值是 xamarin 创建的弱全局引用的计数。 *句柄*或*obj 句柄*值为 JNI 句柄值, 而 */* "" 后面的字符是句柄值的类型: */l*用于本地引用, */g*用于全局引用, */w*用于弱全局引用。

作为 GC 过程的一部分, 全局引用 (+ g +) 将转换为弱全局引用 (导致 + w + 和-g-), 然后将启动 Java 端 GC, 然后检查弱全局引用以查看是否收集了该全局引用。 如果它仍处于活动状态, 则将围绕弱引用 (+ g +,-w-) 创建一个新的 gref, 否则弱引用将被销毁 (-w)。

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Java 实例由 MCW 创建和包装

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>正在执行 GC 。

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>对象仍处于活动状态, 作为句柄! = null
## <a name="wref-turned-back-into-a-gref"></a>wref 恢复为 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>对象死, 因为 handle = = null
## <a name="wref-is-freed-no-new-gref-created"></a>wref 已释放, 未创建任何新 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

此处有一项 "有趣" 的建议: 在运行 Android 的4.0 之前, gref 值等于 Android 运行时的内存中 Java 对象的地址。 (也就是说, GC 是非移动、保守、收集器, 它会直接对这些对象进行引用。)因此, 在 + g +, + w +,-g-, + g +,-w 顺序后面, 生成的 gref 将具有与原始 gref 值相同的值。 这使得 grepping 日志变得非常简单。

但是, android 4.0 具有移动收集器, 不再直接对 Android 运行时 VM 对象进行直接引用。 因此, 在 + g +, + w +,-g-, + g +,-w 顺序后, gref 值*将不同*。 如果对象置多个 Gc, 则会通过几个 gref 值, 从而更难确定实例的实际分配位置。

### <a name="querying-programmatically"></a>以编程方式查询

可以通过查询`JniRuntime`对象来查询 GREF 和 WREF 计数。

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`-全局引用计数

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`-弱引用计数



## <a name="android-debug-logs"></a>Android 调试日志

[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)可能会提供有关你所看到的任何运行时错误的其他上下文。



## <a name="floating-point-performance-is-terrible"></a>浮点性能非常糟糕!

另外, "我的应用程序的运行速度比发布版本更快10倍!"

Xamarin 支持多个设备 Abi: *armeabi*、 *armeabi*和*x86*。 可以在**项目属性 > 应用程序选项卡**中指定设备 Abi > 支持的体系结构。

调试版本使用提供所有 Abi 的 Android 包, 因此将为目标设备使用最快的 ABI。

发布版本将仅包括 "项目属性" 选项卡中选择的 Abi。可以选择多个。

*armeabi*是默认 ABI, 具有最广泛的设备支持。 *但是*, armeabi 不支持多 CPU 设备和硬件浮点, amont 其他东西。 因此, 使用 armeabi Release 运行时的应用程序将绑定到单个核心, 并将使用软浮动实现。 这两种方法都会导致应用程序性能明显降低。

如果你的应用需要相当不错的浮点性能 (例如游戏), 则应启用*armeabi-Armeabi-v7a* ABI。 你可能只想支持*armeabi-armeabi-v7a*运行时, 但这意味着, 仅支持*armeabi*的旧设备将无法运行你的应用程序。



## <a name="could-not-locate-android-sdk"></a>找不到 Android SDK

Google 提供了2个适用于 Windows 的 Android SDK 下载。
如果选择 .exe 安装程序, 它将写入注册表项, 告诉 Xamarin 安装它的位置。 如果选择 .zip 文件并自行解压缩, 则 Xamarin 不知道在何处查找 SDK。 可以通过转到 "**工具" > 选项 "> xamarin > Android" 设置**, 告诉 Xamarin Visual Studio 中的 SDK:

[![Xamarin Android 设置中的 Android SDK 位置](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE 不显示目标设备

有时, 你将尝试将你的应用程序部署到设备, 但你想要部署到的设备未显示在 "选择设备" 对话框中。 当 Android Debug Bridge 决定休假时, 会发生这种情况。

若要诊断此问题, 请找到[adb 程序](~/android/deploy-test/debugging/android-debug-log.md), 然后运行:

```shell
adb devices
```

如果设备不存在, 则需要重新启动 Android Debug Bridge 服务器, 以便可以找到设备:

```shell
adb kill-server
adb start-server
```

HTC 同步软件可能会阻止**adb 启动服务器**正常工作。 如果**adb**命令没有打印出它所启动的端口, 请退出 HTC 同步软件, 并尝试重新启动 adb 服务器。


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>无法运行指定的任务可执行文件 "keytool"

这意味着, 路径不包含 Java SDK bin 目录所在的目录。 检查是否遵循了[安装](~/android/get-started/installation/index.md)指南中的这些步骤。


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid 或 aresgen 已退出, 代码为1

若要帮助调试此问题, 请转到 Visual Studio 并更改 MSBuild 详细级别。为此, 请选择:**工具 > > 项目**和解决方案的选项 **> 生成**和**运行 > MSBuild 项目生成输出详细级别**, 并将此值设置为 "**正常**"。

重新生成并检查 Visual Studio 的输出窗格, 该窗格应该包含完整的错误。

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>设备上没有足够的存储空间来部署包

如果未从 Visual Studio 中启动模拟器, 则会发生这种情况。 在 Visual Studio 外部启动仿真程序时, 需要传递`-partition-size 512`选项, 例如

```shell
emulator -partition-size 512 -avd MonoDroid
```

请确保使用正确的模拟器名称, 即[配置模拟器时使用的名称](~/android/get-started/installation/windows.md#device)。


## <a name="install_failed_invalid_apk-when-installing-a-package"></a>安装\_包\_时\_安装失败的 APK 无效

Android 包名称*必须*包含一个句点 (" *.* ")。 编辑包名称, 使其包含句点。

-   在 Visual Studio 中:
    -   右键单击项目 > 属性
    -   单击左侧的 "Android 清单" 选项卡。
    -   更新 "包名称" 字段。
        -   如果看到消息&ldquo;"未找到 androidmanifest.xml。 单击以添加一个。&rdquo;, 单击该链接, 然后更新 "包名称" 字段。
-   在 Visual Studio for Mac 中:
    -   右键单击项目 > 选项。
    -   导航到 "生成/Android 应用程序" 部分。
    -   更改包名称字段以包含 "."。




## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>安装\_包\_时安装\_失败缺少\_共享库

此上下文中的 "共享库"*不*是本机共享库 (*libfoo.so*) 文件;它是必须单独安装在目标设备上的库, 如 Google Maps。

Android 包指定`<uses-library/>`元素需要哪些共享库。 如果目标设备上没有*必需*的库 (例如`//uses-library/@android:required` , 为*true*, 这是默认值), 则包安装将失败, 并显示 "*缺少\_\_共享\_的安装失败"\_库*。

若要确定所需的共享库, 请查看*生成*
的**androidmanifest.xml**文件 (如 **.obj\\调试\\android\\androidmanifest.xml**) 并查找`<uses-library/>`元素。 `<uses-library/>`可以在项目的 **\\属性 androidmanifest.xml**文件中手动添加元素, 并通过[UsesLibraryAttribute 自定义特性](xref:Android.App.UsesLibraryAttribute)添加元素。

例如, 将程序集引用添加到*GoogleMaps*将为 Google Maps 共享库隐式添加一个`<uses-library/>` 。



## <a name="install_failed_update_incompatible-when-installing-a-package"></a>安装\_包\_时\_安装失败的更新不兼容

Android 包有三个要求:

-   它们必须包含 "."(请参阅上一项)
-   它们必须具有唯一的字符串包名称 (因此, 在 Android 应用程序名称中会出现反向 tld 约定, 如 Chrome 应用的 .com)。
-   升级包时, 包必须具有相同的签名密钥。

因此, 请设想以下情况:

1.  构建 & 将应用部署为调试应用
2.  更改签名密钥, 例如用作发布应用 (或因为你不喜欢默认提供的调试签名密钥)
3.  安装应用程序时无需先将其删除, 例如调试 > 在 Visual Studio 中启动而不调试


发生这种情况时, 程序包安装将失败,\_并\_出现\_"安装失败的更新不兼容错误", 因为在签名密钥执行时包名称未更改。 [Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)还将包含类似于以下内容的消息:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

若要修复此错误, 请在重新安装之前从设备中完全删除应用程序。


## <a name="install_failed_uid_changed-when-installing-a-package"></a>安装\_包\_时\_安装失败的 UID 已更改

安装 Android 包时, 会向其分配*用户 id* (UID)。
*有时*, 出于目前未知的原因, 在安装已安装的应用程序时, 安装将失败`INSTALL_FAILED_UID_CHANGED`, 并出现以下错误:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

若要解决此问题, 请通过从 Android 目标的 GUI 安装应用或使用`adb`以下内容来*完全卸载*Android 包:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**不要使用**, 因为这将保留应用程序数据, 从而在目标设备上保留冲突的 UID。 `adb uninstall -k`



## <a name="release-apps-fail-to-launch-on-device"></a>无法在设备上启动发布应用

Android 调试日志输出是否包含类似于以下内容的消息:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

如果是这样, 则有两个可能的原因:

1.  Apk 不提供目标设备支持的 ABI。
    例如, apk 仅包含 armeabi-armeabi-v7a 二进制文件, 而目标设备仅支持 armeabi。

2.  [Android bug](http://code.google.com/p/android/issues/detail?id=21670)。 如果是这种情况, 请卸载该应用程序, 将其置于手指上, 然后重新安装该应用程序。

若要修复此问题, 请编辑项目选项/属性, 并[将所需 ABI 的支持添加到支持的 abi 列表](~/android/app-fundamentals/cpu-architectures.md)中。 若要确定需要添加的 ABI, 请针对目标设备运行以下 adb 命令:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

输出将包含主节点 (和可选的辅助副本) Abi。

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>没有为项目&ldquo;MyApp. .csproj 设置 OutPath 属性&rdquo;

这通常意味着您具有 HP 计算机, 环境变量&ldquo;平台&rdquo;已设置为类似于 MCD 或 HPD 的内容。 这与&ldquo;通常设置为任何 CPU&rdquo;或&ldquo;x86&rdquo;的 MSBuild 平台属性冲突。 您将需要从您的计算机中删除此环境变量, 然后 MSBuild 才能工作:

-   控制面板 > 系统 > 高级 > 环境变量

重新启动 Visual Studio 或 Visual Studio for Mac 并尝试重新生成。 应该会按预期方式工作。

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>ClassCastException: JavaObject 不能强制转换为 ... 的类型。

Xamarin 4.x 未能正确地正确封送嵌套的泛型类型。 例如, 请考虑以下 C\#代码, 使用[SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):


```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```


问题在于, Xamarin 未能正确地封送嵌套的泛型类型。 `List<IDictionary<string, object>>` 将被封送到  [java.lang.ArrrayList](xref:Java.Util.ArrayList), 但`ArrayList`包含`mono.android.runtime.JavaObject`实例 (引用`Dictionary<string, object>`实例), 而不是实现 [java.util.Map](xref:Java.Util.IMap) 的内容。 导致以下异常:

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

解决方法是使用提供的[Java 集合类型](~/android/internals/api-design.md), 而不`System.Collections.Generic`是&ldquo;内部&rdquo;类型的类型。 这会在封送实例时产生相应的 Java 类型。 (下面的代码比需要的代码更复杂, 目的是减少 gref 的生存期。 如果不担心 gref 的生存期, `s/List/JavaList/g` `s/Dictionary/JavaDictionary/g`则可以将其简化为通过更改原始代码。

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[这将在将来的版本中修复](https://bugzilla.xamarin.com/show_bug.cgi?id=5401)。


## <a name="unexpected-nullreferenceexceptions"></a>意外的 NullReferenceExceptions

偶尔, [android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)会提到 NullReferenceExceptions 不&ldquo;能发生的&rdquo;问题, 也不会在应用程序停止前立即从 Mono for Android 运行时代码发出:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

或

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

当 Android 运行时决定中止进程时, 可能会发生这种情况, 这种情况可能发生在多种原因中, 包括达到目标的 GREF 限制&ldquo;或&rdquo; JNI 的操作。

若要查看是否属于这种情况, 请查看 Android 调试日志, 查找来自进程的消息, 如下所示:

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>由于全局引用耗尽, 导致中止

Android 运行时的 JNI 层仅支持在任意给定时间点有效的有限数量的 JNI 对象引用。 超过此限制时, 会中断。

GREF (*全局引用*) 限制在模拟器中是2000引用, 在硬件上为 ~ 52000 引用。

当你在 Android 调试日志中看到如下消息时, 知道你开始创建太多的 GREFs:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

达到 GREF 限制时, 会打印如下消息:

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```


在上面的示例中 (顺便说一下, 它来自[bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)), 问题在于 Android 太多。正在创建点实例;有关此特定错误的修复的列表, 请参阅[注释\#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) 。

通常, 一种有用的解决方案是查找哪个类型的实例分配&ndash;的实例太多。在上述转储&ndash;中, 找到它们在源代码中的创建位置, 并相应地处置它们 (以便Java-对象生存期已缩短。 这并不总是合适 (\#685215 是多线程的, 因此, 普通解决方案可避免 Dispose 调用), 但这是第一个需要考虑的事项。

可以启用[GREF 日志记录](~/android/troubleshooting/index.md), 以查看 GREFs 的创建时间和存在的时间。


## <a name="abort-due-to-jni-type-mismatch"></a>由于 JNI 类型不匹配而中止

如果你手动滚动 JNI 代码, 则类型可能无法正确匹配, 例如, 如果你尝试对不实现`java.lang.Runnable.run` `java.lang.Runnable`的类型调用, 则可能不正确。 发生这种情况时, Android 调试日志中将出现类似于以下内容的消息:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>动态代码支持

### <a name="dynamic-code-does-not-compile"></a>动态代码不编译

若要在\#你的应用程序或库中使用 C 动态, 你必须将 system.web、node.js 和 Mono 添加到你的项目中。

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>在发布版本中, 在运行时动态代码会发生 MissingMethodException。

-   你的应用程序项目可能不具有对 System.web、node.js 或 Mono. .dll 或 Mono 的引用。 请确保引用这些程序集。

    -   请记住, 动态代码始终会产生费用。 如果需要有效的代码, 请考虑不要使用动态代码。

-   在第一个预览版中, 除非应用程序代码显式使用每个程序集中的类型, 否则会排除这些程序集。 有关解决方法, 请参阅以下内容:[http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>X86 设备上通过 AOT + LLVM 崩溃生成的项目

在基于 x86 的设备上部署使用[AOT + LLVM](~/android/deploy-test/release-prep/index.md)生成的应用时, 可能会看到类似于以下内容的异常错误消息:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

这是[56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111)中报告的已知问题。 解决方法是禁用 LLVM。
