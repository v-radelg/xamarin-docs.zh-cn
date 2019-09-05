---
title: GDB
ms.prod: xamarin
ms.assetid: CD0BE462-FA38-4881-B481-82AD05B3B8FE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: bc2c1c26bcb3dcf99a5598b6dccb6aad02513a28
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197558"
---
# <a name="gdb"></a>GDB

## <a name="overview"></a>概述

Xamarin.Android 4.10 通过使用 `_Gdb` MSBuild 目标引入了对使用 `gdb` 的部分支持。 

> [!NOTE]
> `gdb` 支持要求安装 Android NDK。

有三种使用 `gdb` 的方法：

1. [启用快速部署的调试版本](#Debug_Builds_with_Fast_Deployment)。
1. [禁用快速部署的调试版本](#Debug_Builds_without_Fast_Deployment)。
1. [发行版本](#Release_Builds)。


出现错误时，请参阅[故障排除](#Troubleshooting)部分。

<a name="Debug_Builds_with_Fast_Deployment" />

### <a name="debug-builds-with-fast-deployment"></a>使用快速部署的调试版本

构建和部署启用快速部署的调试版本时，可以使用 `_Gdb` MSBuild 目标附加 `gdb`。

首先，安装应用。 可以通过 IDE 或命令行完成此操作：

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:Install *.csproj
```

其次，运行 `_Gdb` 目标。 执行结束时，会打印 `gdb` 命令行：

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
...
```

`_Gdb` 目标将启动在你的 `AndroidManifest.xml` 文件中声明的任意启动器活动。 若要显式指定要运行的活动，请使用 `RunActivity` MSBuild 属性。 此时不支持启动服务和其他 Android 构造。

`_Gdb` 目标将创建一个 `gdb-symbols` 目录，并将目标 `/system/lib` 和 `$APPDIR/lib` 目录的内容复制到那里。


> [!NOTE]
> `gdb-symbols` 目录的内容绑定到你部署到的 Android 目标，如果你更改目标，则不会自动替换。 （请注意这个 bug。）如果你更改 Android 目标设备，则需要手动删除此目录。

最后，复制生成的 `gdb` 命令并在你的 shell 中执行它：

```bash
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) bt
#0  0x40082e84 in nanosleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#1  0x4008ffe6 in sleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#2  0x74e46240 in ?? ()
#3  0x74e46240 in ?? ()
(gdb) c
```

<a name="Debug_Builds_without_Fast_Deployment" />

## <a name="debug-builds-without-fast-deployment"></a>不使用快速部署的调试版本

 使用快速部署的调试版本，方法是将 Android NDK 的 `gdbserver` 程序复制到快速部署 `.__override__` 目录。 快速部署禁用时，此目录可能不存在。

有两种解决方法：

- 设置 `debug.mono.log` 系统属性，以便创建 `.__override__` 目录。
- 将 `gdbserver` 包含在你的 `.apk` 中。

### <a name="setting-the-debugmonolog-system-property"></a>设置 `debug.mono.log` 系统属性

要设置 `debug.mono.log` 系统属性，请使用 `adb` 命令：

```bash
$ adb shell setprop debug.mono.log gc
```

系统属性设置后，请执行 `_Gdb` 目标和打印的 `gdb` 命令，就像“使用快速部署的调试版本”配置一样：

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```


### <a name="including-gdbserver-in-your-app"></a>将 `gdbserver` 包含在你的应用中

将 `gdbserver` 包含在你的应用中：

1. 在你的 Android NDK 中找到 `gdbserver`（它应位于 **$ANDROID\_NDK\_PATH/prebuilt/android-arm/gdbserver/gdbserver** 中），并将其复制到你的 Project 目录中。

2. 将 `gdbserver` 重命名为 **libs/armeabi-v7a/libgdbserver.so**。

3. 使用 `AndroidNativeLibrary` 的“生成操作”  将 **libs/armeabi-v7a/libgdbserver.so** 添加到你的项目。

4. 重新生成并重新安装你的应用程序。

重新安装应用后，请执行 `_Gdb` 目标和打印的 `gdb` 命令，就像“使用快速部署的调试版本”配置一样：

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

<a name="Release_Builds" />

## <a name="release-builds"></a>发行版本

`gdb` 支持需要三项内容：

1. `INTERNET` 权限。
2. 应用调试已启用。
3. 可访问的 `gdbserver`。

默认在调试应用中启用 `INTERNET` 权限。 如果它在你的应用程序中尚未出现，可以通过编辑 **Properties/AndroidManifest.xml** 或[“项目属性”](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)来添加它。

应用调试可通过下面的方法启用：将 [ApplicationAttribute.Debugging](xref:Android.App.ApplicationAttribute.Debuggable) 自定义属性设置为 `true`，或编辑 **Properties/AndroidManifest.xml**，并将 `//application/@android:debuggable` 属性设置为 `true`：

```xml
<application android:label="Example.Name.Here" android:debuggable="true">
```

通过遵守[不使用快速部署的调试版本](#Debug_Builds_without_Fast_Deployment)部分的内容，可以提供可访问的 `gdbserver`。

一个问题：`_Gdb` MSBuild 目标将终止以前运行的任何应用实例。 这不适用于 Android v4.0 之前的目标。

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>疑难解答

### <a name="mono_pmip-doesnt-work"></a>`mono_pmip` 不起作用

从 `libmonosgen-2.0.so` 导出 `mono_pmip` 函数（用于[获取托管堆栈帧](https://www.mono-project.com/docs/debug+profile/debug/#debugging-with-gdb)），`_Gdb` 目标目前不会下拉。 （未来版本会修复此问题。）

要启用位于 `libmonosgen-2.0.so` 中的调用功能，请将其从目标设备复制到 `gdb-symbols` 目录中：

```bash
$ adb pull /data/data/Mono.Android.DebugRuntime/lib/libmonosgen-2.0.so Project/gdb-symbols
```

然后重新启动调试会话。

### <a name="bus-error-10-when-running-the-gdb-command"></a>总线错误：运行 `gdb` 命令时为 10

当 `gdb` 命令出错，显示 `"Bus error: 10"` 时，重新启动 Android 设备。

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
Bus error: 10
$
```

### <a name="no-stack-trace-after-attach"></a>连接后没有堆栈跟踪

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
(gdb) bt
No stack.
```

这通常表示 `gdb-symbols` 目录的内容与你的 Android 目标不同步。 （是否更改 Android 目标？）

请删除 `gdb-symbols` 目录并重试。
