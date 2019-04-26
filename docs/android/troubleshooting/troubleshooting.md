---
title: 疑难解答指南
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: b2f11bd09e1b1b3fd7af29a026229494a081ad11
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085031"
---
# <a name="troubleshooting-tips"></a>疑难解答指南


## <a name="getting-diagnostic-information"></a>获取诊断信息

Xamarin.Android 有许多方法可以跟踪各种 bug 时的外观。
这些问题包括：

1.  诊断 MSBuild 输出。
2.  设备部署日志。
3.  Android 调试日志输出。


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>诊断 MSBuild 输出

诊断 MSBuild 可以包含到包构建相关的其他信息，并且可能包含某些包部署信息。

若要在 Visual Studio 中启用诊断 MSBuild 输出：

1.  单击**工具 > 选项...**
2.  在左侧的树视图中，选择**项目和解决方案 > 生成并运行**
3.  在右侧面板中，设置 MSBuild 生成输出详细级别下拉列表中为诊断
4.  单击“确定” 
5.  清除并重新生成包。
6.  诊断输出将显示在输出面板中。


若要为 Mac/OS x： 启用 Visual Studio 中的诊断 MSBuild 输出

1.  单击**Visual Studio for Mac > 首选项...**
2.  在左侧的树视图中，选择**项目 > 生成**
3.  在右侧面板中，设置日志详细级别下拉列表为诊断
4.  单击“确定” 
5.  重启 Visual Studio for Mac
6.  清除并重新生成包。
7.  诊断输出将显示错误板中 (**视图 > 面板 > 错误**)，通过单击生成输出按钮。




## <a name="device-deployment-logs"></a>设备部署日志

若要启用 Visual Studio 中的设备部署日志记录：

1.  **工具 > 选项...**>
2.  在左侧的树视图中，选择**Xamarin > Android 设置**
3.  在右侧面板中，启用 [X]**扩展调试日志记录 （将 monodroid.log 写入桌面）** 复选框。
4.  日志消息都写入到在您的桌面上 monodroid.log 文件。


Visual Studio for Mac 始终会写入设备部署日志。 找到它们会稍有难度;*AndroidUtils*每一天 + 进行部署时，例如创建的日志文件：**AndroidTools-2012-10-24_12-35-45.log**。

-  在 Windows 中，日志文件将写入到`%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`。
-  在 OS X 上的日志文件写入到`$HOME/Library/Logs/XamarinStudio-{VERSION}`。




## <a name="android-debug-log-output"></a>Android 调试日志输出

Android 将多个消息将写入[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)。
Xamarin.Android 使用 Android 系统属性来控制生成的其他消息到 Android 调试日志。 可以通过设置 android 系统属性*setprop*命令内[Android Debug Bridge (adb)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

系统属性进程在启动期间，读取，因此，必须设置之前启动应用程序或应用程序必须重新启动后更改的系统属性。



### <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系统属性

Xamarin.Android 支持以下系统属性：

-   *debug.mono.debug*:如果一个非空字符串，这相当于`*mono-debug*`。

-   *debug.mono.env*:竖线分隔 ('*|*) 的环境变量来导出应用程序启动期间列表*之前*mono 已初始化。 这允许将环境变量设置该控件 mono 日志记录。

    - *说明*：由于值是 *|* 的隔离，该值必须包含额外级别的用引号括起来，作为\`*adb shell*\`命令将删除引号引起来的一组。

    - *说明*：Android 系统属性值不能超过 92 个字符的长度。

    - 示例:

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *debug.mono.log*:以逗号分隔 ('*，*) 应打印到 Android 调试日志的其他消息的组件的列表。 默认情况下，设置执行任何操作。 组件包括：

    -   *所有*:打印所有消息
    -   *gc*:打印与 GC 相关的消息。
    -   *gref*:打印弱 （全局） 引用分配和解除分配消息。
    -   *lref*:打印本地引用分配和解除分配消息。

    *请注意*： 这些是*极*详细。 不要启用，除非确实需要。

-   *debug.mono.trace*:す 砛[mono-跟踪](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`设置。



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android 不能解决 System.ValueTuple

此错误是由于使用 Visual Studio 不兼容。

- **Visual Studio 2017 Update 1** （版本 15.1 或更低版本） 是仅与兼容**System.ValueTuple NuGet 4.3.0** （或更低版本）。

- **Visual Studio 2017 Update 2** （版本 15.2 或更高版本） 是仅与兼容**System.ValueTuple NuGet 4.3.1** （或更高版本）。

请选择对应于 Visual Studio 2017 安装正确 System.ValueTuple NuGet。


## <a name="gc-messages"></a>GC 消息

GC 组件消息可以查看通过 debug.mono.log 系统属性设置为一个包含 gc 的值。

每当 GC 执行，并提供信息了解多少运行 GC 未生成 GC 消息：

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

可以通过设置生成计时信息等的其他 GC 信息`MONO_LOG_LEVEL`环境变量为`debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

这将导致 （很多） 其他的 Mono 消息，包括以下三个后果：

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

在中`GC_BRIDGE`消息，`num-objects`是正在考虑此阶段，桥对象的数目和`num_hash_entries`是此调用桥代码期间处理的对象的数字。

中`GC_MINOR`并`GC_MAJOR`消息，`total`是长时间暂停世界时 （没有线程正在执行），而`bridge`是在桥处理代码 （用来处理 Java VM） 中所用的时间量。 这一领域*不*暂停桥处理发生时。

 *一般情况下*的值越大`num_hash_entries`，则更多时间`bridge`需要集合，和较大`total`收集所用的时间将为。



## <a name="global-reference-messages"></a>全局引用消息

若要启用日志记录，全局引用 loggig (GREF) *debug.mono.log*系统属性必须包含*gref*，例如：

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android 使用 Android 全局引用时调用的 Java 实例，需要为 Java 提供的 Java 方法为提供 Java 实例关联的托管的实例之间的映射。

遗憾的是，Android 仿真程序只允许 2000年全局引用，以同时存在。 硬件具有更高版本限制为 52000 全局引用。 因此，知道在仿真器上运行应用程序时可能存在问题的下限*其中*实例来源可能非常有用。

 *请注意*： 全局引用计数是 Xamarin.Android，内部，不会 （也不能） 包括不再考虑其他本机库加载到进程的全局引用。 使用全局引用计数为估计值。

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

有四个消息的重要性：

-  全局引用创建： 这些是开头的行 *+ g +* ，并将创建的代码路径提供堆栈跟踪。
-  全局引用析构： 这些是开头的行 *-g-* ，并可提供用于处理全局引用的代码路径的堆栈跟踪。 如果 GC 释放的 gref 中，将提供没有堆栈跟踪。
-  全局的弱引用创建： 这些是开头的行 *+ w +* 。
-  全局的弱引用析构： 这些是开头的行 *-w-* 。


中的所有消息， *grefc*值是 Xamarin.Android 已创建，全局引用的计数时*grefwc*值是弱 Xamarin.Android 已创建的全局引用的计数。 *处理*或*obj 句柄*JNI 句柄值，后面的字符值为为 ' */*' 是句柄值的类型： */L*的本地引用 */G*对于全局引用，并 */W*弱全局引用。

GC 过程的一部分，作为全局引用 （+ g +） 转换为全局的弱引用 (导致 a + w + 和-g-) 和 Java 端 GC 将启动，然后检查弱全局引用，以确定是否收集。 如果仍处于活动状态，周围的弱引用创建新 gref (+ g +、-w-)，否则销毁的弱引用 (-w)。

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>创建并由 MCW 包装 Java 实例

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>GC 是正在执行...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>对象是仍保持活动状态，为句柄 ！ = null
## <a name="wref-turned-back-into-a-gref"></a>wref 转回 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>对象已死，句柄作为 = = null
## <a name="wref-is-freed-no-new-gref-created"></a>wref 是已释放，无法将任何新创建的 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

还有一个"有意义"的问题： 在运行 Android 4.0 之前的目标，gref 值是否等于 Android 运行时的内存中的 Java 对象的地址。 （即，GC 是非移动，保守，收集器，并且它正在处理对这些对象的直接引用。）因此之后 + g + + w +、-g-，+ g +、-w 序列，生成 gref 将具有与原始 gref 值相同的值。 这使得通过日志 grepping 非常简单。

Android 4.0 中，但是，已移动的收集器，并且无法再分发对 Android 运行时的直接引用 VM 对象。 因此之后, + g + + w +、-g-，+ g +、-w 序列、 gref 值*将不同*。 如果该对象可以幸存，但多个 Gc，它会通过一些 gref 值，因此更难确定其中一个实例已从实际分配。

### <a name="querying-programmatically"></a>以编程方式查询

您可以通过查询查询 GREF 和 WREF 计数`JniRuntime`对象。

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` 全局引用计数

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -弱引用计数



## <a name="android-debug-logs"></a>Android 调试日志

[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)可能提供有关您要查看的任何运行时错误的更多上下文。



## <a name="floating-point-performance-is-terrible"></a>浮点性能太糟糕了 ！

或者，"我的应用程序运行速度更快 10 倍与调试版本比发布版本 ！"

Xamarin.Android 支持多个设备的 Abi: *armeabi*， *armeabi-v7a*，并*x86*。 在中指定设备 Abi**项目属性 > 应用程序选项卡 > 支持的体系结构**。

调试版本中使用的 Android 程序包，它提供了所有的 Abi，因此将使用最快的 ABI 针对目标设备。

发布版本将仅包括在项目属性选项卡中选择的 Abi。可以选择多个。

*armeabi* ABI，默认值，且是最广泛的设备支持。 *但是*，armeabi 不支持多 CPU 的设备和硬件浮点型 amont 其他操作。 因此，使用 armeabi 版本运行时应用程序将绑定到单核，并且将使用软浮点实现。 这两种可参与您的应用程序的性能明显变慢。

如果你的应用需要不错的浮点性能 （例如游戏），则应启用*armeabi-v7a* ABI。 你可能想要仅支持*armeabi-v7a*运行时，尽管这意味着，仅支持较旧的设备*armeabi*将无法运行你的应用。



## <a name="could-not-locate-android-sdk"></a>找不到 Android SDK

将来自 Google 的 Android SDK 的 Windows 提供了 2 个下载。
如果您选择.exe 安装程序，它将编写告诉 Xamarin.Android 的安装位置的注册表项。 如果您选择的.zip 文件，并将其解压自己，Xamarin.Android 不知道查找 SDK 的位置。 您可以告知 Xamarin.Android 位置 SDK 在 Visual Studio 中通过转到**工具 > 选项 > Xamarin > Android 设置**:

[![Xamarin Android 设置中的 android SDK 位置](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE 不会显示目标设备

有时将尝试应用程序部署到设备，但想要部署到未显示在选择设备对话框中的设备。 这可以去度假 Android Debug Bridge 决定。

若要诊断此问题，查找[adb 程序](~/android/deploy-test/debugging/android-debug-log.md)，然后运行：

```shell
adb devices
```

如果你的设备不存在，然后需要重新启动 Android Debug Bridge 服务器，以便可以找到你的设备：

```shell
adb kill-server
adb start-server
```

HTC 同步软件可能会阻止**adb 开始服务器**无法正常工作。 如果**adb 开始服务器**命令不会打印出其启动的端口，请退出 HTC 同步软件，请尝试重启 adb 服务器。


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>无法运行指定的任务可执行文件"keytool"

这意味着你的路径不包含 Java SDK 的 bin 目录所在的目录。 检查是否遵循这些步骤，可从[安装](~/android/get-started/installation/index.md)指南。


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe 或 aresgen.exe 已退出，代码 1

若要帮助你调试此问题，请转到 Visual Studio 和更改 MSBuild 详细级别，为此，请选择：**工具 > 选项 > 项目**并**解决方案 > 构建**并**运行 > MSBuild 项目生成输出详细信息**并将此值设置为**正常**。

重新生成，并检查 Visual Studio 输出窗格中，其中应包含完整的错误。

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>要将包部署的设备上没有足够的存储空间

在不启动 Visual Studio 中的从仿真程序时，将发生这种情况。 当从 Visual Studio 外部的仿真程序，需要传递`-partition-size 512`选项，例如

```shell
emulator -partition-size 512 -avd MonoDroid
```

请确保使用正确的模拟器名称，即[时配置模拟器使用的名称](~/android/get-started/installation/windows.md#device)。


## <a name="installfailedinvalidapk-when-installing-a-package"></a>安装\_失败\_无效\_APK 安装包时

Android 包名称*必须*包含句点 (*。*)。 编辑你的包名称，以便它包含一个句点。

-   在 Visual Studio:
    -   右键单击你的项目 > 属性
    -   单击左侧的 Android 清单选项卡。
    -   更新包名称字段。
        -   如果看到消息&ldquo;找到任何 AndroidManifest.xml。 单击此项可添加一个。&rdquo;、 单击链接，然后更新包名称字段。
-   在 Visual Studio for Mac:
    -   右键单击你的项目 > 选项。
    -   导航到生成 / Android 应用程序部分。
    -   更改要包含的包名称字段。 '。




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>安装\_失败\_MISSING\_共享\_库安装包时

在此上下文中的"共享的库"，则*不*本机共享的库 (*libfoo.so*) 文件; 而是必须单独安装在目标设备，如 Google 地图的库。

Android 包指定正在使用所需的共享的库`<uses-library/>`元素。 如果*必需*库不存在目标设备上 (例如`//uses-library/@android:required`是*true*，这是默认设置)，则包的安装将失败，与*安装\_失败\_缺少\_共享\_库*。

若要确定哪些共享的库是必需的请查看*生成*
**AndroidManifest.xml**文件 (例如**obj\\调试\\android\\AndroidManifest.xml**)，并查找`<uses-library/>`元素。 `<uses-library/>` 可以在项目中手动添加元素**属性\\AndroidManifest.xml**文件并通过[UsesLibraryAttribute 自定义特性](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/)。

例如，添加的程序集引用*Mono.Android.GoogleMaps.dll*会隐式添加`<uses-library/>`Google Maps 共享库。



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>安装\_失败\_更新\_安装包时不兼容

Android 包具有三个要求：

-   它们必须包含 '。（请参阅以前的条目）
-   它们必须具有唯一的字符串的包名称 (因此在 Android 应用程序名称，例如 com.android.chrome Chrome 应用中看到的反向 tld 约定)
-   升级程序包时，包必须具有相同的签名密钥。

因此，设想以下场景：

1.  生成和部署你的应用作为调试应用
2.  更改签名密钥，例如为用作发布应用程序 （或因为您不喜欢默认提供调试签名密钥）
3.  将应用安装无需首先将其删除，例如： 调试 > 启动但不调试 Visual Studio 中


在此情况下，包的安装将失败，并安装\_失败\_更新\_不兼容错误，因为包名称未发生更改时的签名密钥未。 [Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)还将包含如下消息：

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

若要修复此错误，完全删除应用程序从你的设备然后再重新安装。


## <a name="installfaileduidchanged-when-installing-a-package"></a>安装\_失败\_UID\_安装包时，更改

安装 Android 包时，会分配*用户 id* (UID)。
*有时*，对于当前未知原因，通过已安装的应用程序安装时，安装将失败与`INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

若要解决此问题，请*完全卸载*Android 包，通过从 Android 目标 GUI 安装的应用或使用`adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**不要使用** `adb uninstall -k`，因为这会*保留*应用程序数据，并因此保留在目标设备上发生冲突的 UID。



## <a name="release-apps-fail-to-launch-on-device"></a>发布应用程序无法在设备上启动

Android 调试日志输出将包含如下消息：

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

如果是这样，有的这两个可能的原因：

1.  .Apk 不提供目标设备支持的 ABI。
    例如，.apk 仅包含 armeabi-v7a 二进制文件和目标设备仅支持 armeabi。

2.  [Android bug](http://code.google.com/p/android/issues/detail?id=21670)。 如果这种情况，卸载该应用、 跨您的手指移动，并重新安装该应用。

若要解决问题 (1)，请编辑项目选项/属性和[将对所需的 ABI 的支持添加到支持的 Abi 列表](~/android/app-fundamentals/cpu-architectures.md)。 若要确定您需要添加哪些 ABI，请对你的目标设备运行以下 adb 命令：

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

输出将包含主 （和可选的辅助副本） 的 Abi。

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>OutPath 属性未设置为项目&ldquo;MyApp.csproj&rdquo;

这通常意味着您必须将 HP 的计算机和环境变量&ldquo;平台&rdquo;MCD 或 HPD 等设置为内容。 这与通常设置为 MSBuild 平台属性冲突&ldquo;任何 CPU&rdquo;或&ldquo;x86&rdquo;。 您需要正常 MSBuild 可以从计算机中删除此环境变量：

-   控制面板 > 系统 > 高级 > 环境变量

重启 Visual Studio 或 Visual Studio for Mac，然后尝试重新生成。 功能现在应工作正常。

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject 无法强制转换为...

Xamarin.Android 4.x 不会正确封送嵌套的泛型类型正确。 例如，考虑以下 C\#代码使用[SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


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


问题在于 Xamarin.Android 错误地将嵌套的泛型类型封送。 `List<IDictionary<string, object>>`正在封送到[java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/)，但`ArrayList`包含`mono.android.runtime.JavaObject`实例 (哪个引用`Dictionary<string, object>`实例) 而不是某些实现[java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/)，从而导致出现以下异常：

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

解决方法是使用所提供[Java 集合类型](~/android/internals/api-design.md)而不是`System.Collections.Generic`类型&ldquo;内部&rdquo;类型。 封送处理实例时，这将导致相应的 Java 类型。 （下面的代码是必要的以减少 gref 生存期比要复杂一些。 它可以简化为更改原始代码通过`s/List/JavaList/g`和`s/Dictionary/JavaDictionary/g`如果 gref 生存期不需担心。)

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

[这将在未来版本中修复](https://bugzilla.xamarin.com/show_bug.cgi?id=5401)。


## <a name="unexpected-nullreferenceexceptions"></a>出现意外的 NullReferenceExceptions

偶尔[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)会提及 nullreferenceexception 抛出的&ldquo;不会发生&rdquo;或来自 Mono 前应用就完了 Android 运行时代码：

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

这可能会在 Android 运行时决定中止过程中，其可能会由于多种原因，包括达到目标的 GREF 限制或执行这样的操作&ldquo;错误&rdquo;使用 JNI。

若要查看是否这种情况，请从您的过程类似于消息 Android 调试日志：

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>由于全局引用用尽而导致中止

Android 运行时的 JNI 层仅支持有限的数量的 JNI 对象引用，以在时间上会在任意给定时间有效。 当超过此限制时，事情就会中断。

GREF (*全局引用*) 限制为 2000年引用的仿真程序中，并在硬件上的 ~ 52000 引用。

您知道正在创建太多 GREFs 时看到消息，例如此 Android 调试日志中：

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

时达到 GREF 限制，会输出消息如下所示：

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


在上面的示例 (其中，顺便说一下，来自[bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) 问题是，创建的实例过多 Android.Graphics.Point; 请参阅[注释\#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2)有关的修补程序列表此特定的 bug。

通常情况下，一个有用的解决方案是查找哪种类型具有太多实例分配&ndash;Android.Graphics.Point 上述转储中的&ndash;然后找到其中它们创建在您的源代码和处置它们相应地 (以便其缩短 Java-object 生存期)。 这并不总是适当 (\#685215 是多线程，因此简单的解决方案可避免 Dispose 调用)，但它是第一件事需要考虑。

可以让[GREF 日志记录](~/android/troubleshooting/index.md)若要查看何时创建 GREFs，以及多少存在。


## <a name="abort-due-to-jni-type-mismatch"></a>中止由于 JNI 类型不匹配

如果手动播 JNI 代码，则有可能的类型不会与匹配正确，例如如果你尝试调用`java.lang.Runnable.run`不会实现的类型`java.lang.Runnable`。 此操作时，将有一条消息类似于此 Android 调试日志中：

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>动态代码支持

### <a name="dynamic-code-does-not-compile"></a>动态代码不会进行编译

若要使用的是 C\#动态应用程序或库中，您需要将 system.core.dll 的引用、 Microsoft.CSharp.dll 和 Mono.CSharp.dll 添加到你的项目。

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>在发布版本 MissingMethodException 发生动态代码在运行时。

-   很可能你的应用程序项目不具有对 system.core.dll 的引用、 Microsoft.CSharp.dll 或 Mono.CSharp.dll 的引用。 请确保引用这些程序集。

    -   请注意该动态代码始终成本。 如果你需要高效的代码，请考虑不使用动态代码。

-   在第一个预览中，除非由应用程序代码显式使用每个程序集中的类型，否则排除这些程序集。 请参阅以下一种解决方法： [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>与 AOT + LLVM 崩溃构建在 x86 上的项目的设备

部署使用构建的应用时[AOT + LLVM](~/android/deploy-test/release-prep/index.md)上基于 x86 的设备，可能会看到异常错误消息如下所示：

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

这是一个已知的问题中报告[56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111)。 解决方法是禁用 LLVM。
