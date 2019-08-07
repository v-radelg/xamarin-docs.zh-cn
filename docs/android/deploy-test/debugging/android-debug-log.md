---
title: Android 调试日志
description: 如何使用调试日志调试 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: edd6fc92603783dc9de64b10304e8d48f97bdef3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509214"
---
# <a name="android-debug-log"></a>Android 调试日志

开发人员用于调试应用程序的一个非常常见的技巧是调用 `Console.WriteLine`。 但是，在移动平台（如 Android）上没有控制台。 Android 设备会提供日志，可以在编写应用时使用。 这有时称为“logcat”  ，原因在于为检索它而输入的命令。 使用“调试日志”  工具，可查看记录的数据。

## <a name="android-debug-log-overview"></a>Android 调试日志概述

“调试日志”  工具使你能够在通过 Visual Studio 调试应用时查看日志输出。 调试日志支持以下设备：

-   物理 Android 手机、平板电脑和可穿戴设备。
-   在 Android Emulator 上运行的 Android 虚拟设备。 

> [!NOTE]
> “调试日志”  工具并不适用于 Xamarin Live Player。

“调试日志”  不会显示在设备上独立运行应用时生成的日志消息（即，在从 Visual Studio 断开连接时）。


## <a name="accessing-the-debug-log-from-visual-studio"></a>从 Visual Studio 访问调试日志

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要打开“设备日志”  工具，单击工具栏上的“设备日志 (logcat)”  图标：

[![工具栏上设备日志工具的位置](android-debug-log-images/vswin-01-logcat-sml.png)](android-debug-log-images/vswin-01-logcat.png#lightbox)

或者，也可以从以下菜单选项之一启动“设备日志”  工具：

-   “视图”>“其他窗口”>“设备日志” 
-   “工具”>“Android”>“设备日志” 

下面的屏幕截图演示了“调试工具”  窗口的各个部分：

[![调试工具窗口的各个部分](android-debug-log-images/vswin-03-features-sml.png)](android-debug-log-images/vswin-03-features.png#lightbox)

-   设备选择器  &ndash; 选择要监视哪些物理设备或运行的仿真器。

-   日志条目  &ndash; logcat 中的日志消息表。

-   清除日志条目  &ndash; 清除表中所有当前日志条目。

-   播放/暂停  &ndash; 在更新或暂停显示新日志条目之间切换。

-   停止  &ndash; 暂停显示新日志条目。

-   搜索框  &ndash; 在此框中输入搜索字符串以筛选日志条目子集。


当显示“调试日志”  工具窗口时，使用设备下拉菜单来选择要监视的 Android 设备：

[![设备选择器的位置](android-debug-log-images/vswin-02-devices-combo-sml.png)](android-debug-log-images/vswin-02-devices-combo.png#lightbox)

在选择设备后，“设备日志”工具会从正在运行的应用中自动添加日志条目 &ndash; 这些日志条目显示在日志条目表中  。 在设备之间切换将停止和启动设备日志记录。 请注意，在任何设备显示在设备选择器中之前，必须加载 Android 项目。 如果设备未显示在设备选择器中，请确认它在“启动”  按钮旁边的 Visual Studio 设备下拉列表菜单中可用。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

要打开“设备日志”  ，请单击“视图”>“面板”>“设备日志”  ：

[![设备日志菜单项的位置](android-debug-log-images/vsmac-01-logcat-sml.png)](android-debug-log-images/vsmac-01-logcat.png#lightbox)

下面的屏幕截图演示了“调试工具”  窗口的各个部分：

[![调试工具窗口的功能](android-debug-log-images/vsmac-03-features-sml.png)](android-debug-log-images/vsmac-03-features.png#lightbox)

-   设备选择器  &ndash; 选择要监视哪些物理设备或运行的仿真器。

-   日志条目  &ndash; logcat 中的日志消息表。

-   清除日志条目  &ndash; 清除表中所有当前日志条目。

-   搜索框  &ndash; 在此框中输入搜索字符串以筛选日志条目子集。

-   显示消息  &ndash; 切换显示信息性消息。

-   显示警告  &ndash; 切换显示警告消息（警告消息显示为黄色）。

-   显示错误  &ndash; 切换显示错误消息（警告消息显示为红色）。

-   重新连接  &ndash; 重新连接到设备并刷新日志条目显示。

-   添加标记  &ndash; 在最新日志条目后插入标记消息（如 `--- Marker N ---`），其中 N  是从 1 开始的计数器，并在添加新标记时增加 1。

当显示“调试日志”工具窗口时，使用设备下拉菜单来选择要监视的 Android 设备：

[![设备选择器的位置](android-debug-log-images/vsmac-02-devices-combo-sml.png)](android-debug-log-images/vsmac-02-devices-combo.png#lightbox)

在选择设备后，“设备日志”工具会从正在运行的应用中自动添加日志条目 &ndash; 这些日志条目显示在日志条目表中  。 在设备之间切换将停止和启动设备日志记录。 请注意，在任何设备显示在设备选择器中之前，必须加载 Android 项目。 如果设备未显示在设备选择器中，请确认它在“启动”  按钮旁边的 Visual Studio 设备下拉列表菜单中可用。

-----


## <a name="accessing-from-the-command-line"></a>从命令行访问

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

另一种方法是通过命令行查看调试日志。 打开命令提示符窗口，并导航到 Android SDK 平台工具文件夹（通常情况下，SDK 平台工具文件夹位于 C:\\Program Files (x86)\\Android\\android-sdk\\platform-tools  中）。

如果仅连接了单个设备（物理设备或仿真器），则可以通过输入以下命令查看日志：

```shell
$ adb logcat
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

另一种方法是通过命令行查看调试日志。 打开终端窗口，并导航到 Android SDK 平台工具文件夹（通常情况下，SDK 平台工具文件夹位于 /Users/username/Library/Developer/Xamarin/android-sdk-macosx/platform-tools  中）。

如果仅连接了单个设备（物理设备或仿真器），则可以通过输入以下命令查看日志：

```shell
$ ./adb logcat
```

-----


如果连接了多个设备，则必须对设备进行显式标识。 例如，adb -d logcat  显示已连接的唯一物理设备的日志，而 adb -e logcat  显示运行中的唯一仿真器的日志。

通过输入 adb  和读取帮助消息，可以找到更多命令。


## <a name="writing-to-the-debug-log"></a>写入调试日志

使用 [Android.Util.Log](xref:Android.Util.Log) 类方法可将消息写入“调试日志”  。
例如: 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

这将生成类似下面的输出：

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```

也可使用 `Console.WriteLine` 写入调试日志 &ndash; 这些消息将在 logcat 中以略有不同的输出格式显示（调试 Android 上的 Xamarin.Forms 应用时，此方法非常有用）  ：

```csharp
System.Console.WriteLine ("DEBUG - Button Clicked!");
```

这将在 logcat 中生成类似下面的输出：

```
Info (19543) / mono-stdout: DEBUG - Button Clicked!
```

## <a name="interesting-messages"></a>兴趣消息

在读取日志时（尤其是在向其他用户提供日志代码片段时），浏览完整的日志文件通常非常麻烦。
若要更加轻松地浏览日志消息，首先查找类似于下面的日志条目：

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

具体而言，查找与也包含应用程序包名称的正则表达式匹配的行：

```shell
^I.*ActivityManager.*Starting: Intent
```

这是对应于活动开始的行，大多数  （并非全部）以下消息均应与应用程序相关。

请注意，每条消息均包含生成该消息的进程的进程标识符 (pid)。 在上一 `ActivityManager` 消息中，进程 `12944` 生成了消息。 若要确定哪个进程是正在调试的应用程序进程，请查找 mono.MonoRuntimeProvider  消息： 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

此消息来自已启动的进程。 包含此 pid 的所有后续消息均来自同一进程。
