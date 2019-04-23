---
ms.openlocfilehash: 8bee6d7145cdc9bf22c01c12a4fb34cfb31cd9f4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187440"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 启动 Visual Studio，新建名为 AppLifecycleTutorial 的 Xamarin.Forms 空白应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 AppLifecycleTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“解决方案资源管理器”的 AppLifecycleTutorial 项目中，展开 App.xaml，然后双击 App.xaml.cs 将其打开。 然后在 App.xaml.cs 中，更新 `OnStart`、`OnSleep` 和 `OnResume` 重写，如下所示：

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    此代码使用 `Console.WriteLine` 语句更新应用程序生命周期方法重写，这些语句指示曾调用每个方法的时间：

    - 应用程序启动时调用 `OnStart` 方法。
    - 应用程序转到后台时调用 `OnSleep` 方法。
    - 应用程序从后台恢复时调用 `OnResume` 方法。

    > [!NOTE]
    > 没有终止应用程序的方法。 正常情况下，应用程序由 `OnSleep` 方法终止。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 应用程序启动时，会调用 `OnStart` 方法，并且 OnStart 会输出到 Visual Studio“输出”窗口：

    ```
    [Mono] Found as 'java_interop_jnienv_get_object_array_element'.
    OnStart
    [OpenGLRenderer] HWUI GL Pipeline
    ```

    应用程序在后台运行时（点击 iOS 或 Android 上的主页按钮），会调用 `OnSleep` 方法：

    ```
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    OnSleep
    [Mono] Image addref System.Runtime.Serialization[0x83ee19c0] -> System.Runtime.Serialization.dll[0x83f57b00]: 2
    ```

    然后，应用程序从后台恢复时（点击 iOS 上的应用程序图标或点击 Android 上的“概述”按钮，并选择 AppLifecycleTutorial 应用程序）调用 `OnResume` 方法：

    ```
    Thread finished: <Thread Pool> #5
    OnResume
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    ```

    > [!NOTE]
    > 这些代码块显示了在 Android 上运行应用程序时的示例输出。

    有关 Xamarin.Forms 应用生命周期的详细信息，请参阅 [Xamarin.Forms 应用生命周期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 Visual Studio for Mac，新建名为 AppLifecycleTutorial 的 Xamarin.Forms 空白应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 AppLifecycleTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“Solution Pad”中的 AppLifecycleTutorial 项目中，展开 App.xaml 并双击 App.xaml.cs 以将其打开。 然后在 App.xaml.cs 中，更新 `OnStart`、`OnSleep` 和 `OnResume` 重写，如下所示：

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    此代码使用 `Console.WriteLine` 语句更新应用程序生命周期方法重写，这些语句指示曾调用每个方法的时间：

    - 应用程序启动时调用 `OnStart` 方法。
    - 应用程序转到后台时调用 `OnSleep` 方法。
    - 应用程序从后台恢复时调用 `OnResume` 方法。

    > [!NOTE]
    > 没有终止应用程序的方法。 正常情况下，应用程序由 `OnSleep` 方法终止。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 应用程序启动时，会调用 `OnStart` 方法，并且 OnStart 会输出到 Visual Studio for Mac“应用程序输出”窗口：

    ```
    2019-02-11 12:05:23.164761+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:05:23.165297+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    2019-02-11 12:05:23.685430+0000 AppLifecycleTutorial.iOS[4089:361037] OnStart
    ```

    应用程序在后台运行时（点击 iOS 或 Android 上的主页按钮），会调用 `OnSleep` 方法：

    ```
    2019-02-11 12:06:12.394301+0000 AppLifecycleTutorial.iOS[4089:361037] OnSleep
    Thread started: <Thread Pool> #3
    Thread started: <Thread Pool> #4
    Thread started: <Thread Pool> #5
    Thread started: <Thread Pool> #6
    2019-02-11 12:06:13.056968+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:06:13.057264+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    ```

    然后，应用程序从后台恢复时（点击 iOS 上的应用程序图标或点击 Android 上的“概述”按钮，并选择 AppLifecycleTutorial 应用程序）调用 `OnResume` 方法：

    ```
    2019-02-11 12:07:10.321905+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4130
    2019-02-11 12:07:10.322557+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskCopyDebugDescription: AppLifecycleTuto[4130]/0#-1 LF=0
    2019-02-11 12:07:17.110938+0000 AppLifecycleTutorial.iOS[4130:365695] OnResume
    ```

    > [!NOTE]
    > 这些代码块显示了在 iOS 上运行应用程序时的示例输出。

    有关 Xamarin.Forms 应用生命周期的详细信息，请参阅 [Xamarin.Forms 应用生命周期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。
