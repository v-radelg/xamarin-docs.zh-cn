---
title: 你好，穿戴设备
description: 创建第一个 Android Wear 应用并在穿戴设备仿真程序或设备上运行。 本演练提供了创建，用于处理按钮单击事件并单击计数器显示在穿戴设备上的小 Android Wear 项目的分步说明。 其中介绍了如何调试使用穿戴设备仿真程序或在穿戴设备通过蓝牙连接到 Android 手机的应用。 它还提供有关 Android Wear 的一组调试提示。
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/10/2018
ms.openlocfilehash: a8e27063040ff91f72a1cbf932b1b277a5dee63d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277319"
---
# <a name="hello-wear"></a>你好，穿戴设备

_创建第一个 Android Wear 应用并在穿戴设备仿真程序或设备上运行。本演练提供了创建，用于处理按钮单击事件并单击计数器显示在穿戴设备上的小 Android Wear 项目的分步说明。其中介绍了如何调试使用穿戴设备仿真程序或在穿戴设备通过蓝牙连接到 Android 手机的应用。它还提供有关 Android Wear 的一组调试提示。_

![在本教程中完成的 Wear 应用的屏幕截图](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>第一个 Wear 应用

请按照以下步骤创建第一个 Xamarin.Android Wear 应用操作：

### <a name="1-create-a-new-android-project"></a>1.创建新的 Android 项目

创建一个新**Android Wear 应用程序**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![在新项目对话框中创建新的 Android Wear 应用程序](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![在新解决方案对话框创建新的 Android Wear 应用程序](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


此模板会自动包括**Xamarin Android 可穿戴库**NuGet （及其依赖项） 以便将有权访问特定于穿戴设备的小组件。 如果看不到穿戴设备模板，请查看[安装和设置](~/android/wear/get-started/installation.md)指南，请仔细检查已安装受支持的 Android SDK。 

### <a name="2-choose-the-correct-target-framework"></a>2.选择正确**目标框架**

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

絋粄**的最低 Android 目标**设置为**Android 5.0 (Lollipop)** 或更高版本： 

[![将目标框架设置为在 Visual Studio 中的 Android 5.0](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

请确保目标框架设置为**Android 5.0 (Lollipop)** 或更高版本：

[![在 Visual Studio 中的 Android 5.0 for Mac 设置目标框架](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

设置目标框架的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。


### <a name="3-edit-the-mainaxml-layout"></a>3.编辑**Main.axml**布局

配置要包含的布局`TextView`和一个`Button`示例： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4.编辑**MainActivity.cs**源

添加代码以递增计数器并将其显示每次单击按钮时： 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5.仿真器或设备安装程序

下一步是设置要部署并运行应用的仿真程序或设备。 如果你尚不熟悉的部署和运行过程 Xamarin.Android 应用一般情况下，请参阅[Hello，Android 快速入门](~/android/get-started/hello-android/hello-android-quickstart.md)。

如果没有如 Android Wear Smartwatch 的 Android Wear 设备，你可以在仿真器上运行应用。 有关调试的仿真程序上 Wear 应用的信息，请参阅[仿真器上调试 Android Wear](~/android/wear/deploy-test/debug-on-emulator.md)。

如果具有 Android Wear Smartwatch 如 Android Wear 设备，你可以而不是使用仿真程序在设备上运行应用。 有关在穿戴设备上进行调试的详细信息，请参阅[穿戴设备的设备上进行调试](~/android/wear/deploy-test/debug-on-device.md)。


### <a name="6-run-the-android-wear-app"></a>6.运行 Android Wear 应用

Android Wear 设备应出现在设备下拉菜单中。 请确保选择正确的 Android Wear 设备或 AVD 开始调试之前。 选择设备之后, 单击播放按钮以将应用部署到仿真器或设备。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![在 Visual Studio 设备菜单中选择 Wear AVD](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![在 Visual Studio 中为 Mac 设备菜单选择 Wear AVD](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

你可能会看到**只需一分钟的时间...** 在第一个消息 （或某些其他插播式屏幕）： 

![观看仿真程序将显示只需一分钟的时间...](hello-wear-images/please-wait.png)

如果使用的监视仿真程序，可能需要一段时间才能启动该应用程序。 当使用蓝牙时，花费更多时间来部署应用，通过 USB 相比。 （例如，它需要 5 分钟内将此应用部署到蓝牙连接到 Nexus 5 电话 LG G Watch。）

应用可以成功部署后，在穿戴设备的屏幕应显示如下所示的屏幕：

[![Wear 应用初始屏幕](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

点击**单击我 ！** 在穿戴设备并查看每个点击计数增量的表面上的按钮：

[![Wear 屏幕快照 3 次单击后的应用程序](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>后续步骤

请查看[Wear 示例](https://developer.xamarin.com/samples/android/Android%20Wear/)包括辅助电话应用程序使用 Android Wear 应用。

当准备好分发应用，请参阅[使用打包](~/android/wear/deploy-test/packaging.md)。


## <a name="related-links"></a>相关链接

- [单击我的应用程序 （示例）](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
