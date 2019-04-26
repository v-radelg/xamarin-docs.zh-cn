---
title: 如何自动化 Android NUnit 测试项目？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: b785ef171d2cb00d4f8f5a17f37d49de17fd3da9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153279"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何自动化 Android NUnit 测试项目？

> [!NOTE]
> 本指南介绍如何自动化 Android NUnit 测试项目，不是 Xamarin.UITest 项目。 找不到 Xamarin.UITest 参考线[此处](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

当您创建**单元测试应用 (Android)** Visual Studio 项目中的 (或**Android 单元测试**Visual Studio for Mac 中的项目)，则此项目将不会自动默认情况下运行测试。
若要在目标设备上运行 NUnit 测试，可以创建[Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/)通过使用以下命令启动的子类： 

```shell
adb shell am instrument 
```

以下步骤介绍了此过程：

1.  创建名为的新文件**TestInstrumentation.cs**: 

    ```cs 
    using System;
    using System.Reflection;
    using Android.App;
    using Android.Content;
    using Android.Runtime;
    using Xamarin.Android.NUnitLite;
     
    namespace App.Tests {
     
        [Instrumentation(Name="app.tests.TestInstrumentation")]
        public class TestInstrumentation : TestSuiteInstrumentation {
     
            public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
            {
            }
     
            protected override void AddTests ()
            {
                AddTest (Assembly.GetExecutingAssembly ());
            }
        }
    }
    ```
    在此文件中， [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (从**Xamarin.Android.NUnitLite.dll**) 来创建子类化`TestInstrumentation`。

2.  实现[TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)构造函数和[AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29)方法。 `AddTests`方法控制实际执行哪些测试。

3.  修改`.csproj`文件以添加**TestInstrumentation.cs**。 例如：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

3.  使用以下命令以运行单元测试。 替换`PACKAGE_NAME`与应用程序的包名称 (可以在应用中找到包名称`/manifest/@package`属性位于**AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  或者，您可以修改`.csproj`文件以添加`RunTests`MSBuild 目标。 这样就可以调用的命令如下所示的单元测试：

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (请注意，使用此新的目标不是必需的; 早期版本`adb`而不是可以使用命令`msbuild`。)

有关使用详细信息`adb shell am instrument`命令以运行单元测试，请参阅 Android 开发人员[使用 ADB 运行测试](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)主题。


> [!NOTE]
> 与[Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) release，默认包名称 Android 可调用包装器将基于要导出的类型的程序集限定名称的 MD5SUM。 这样，相同的完全限定名称，以提供从两个不同的程序集，并且不使打包错误。 因此，请确保你使用`Name`属性上的`Instrumentation`属性生成可读的 ACW/类名称。

_必须在使用 ACW 名称`adb`上述命令_。
重命名重构C#类将因此需要修改`RunTests`命令，以使用正确的 ACW 名称。

