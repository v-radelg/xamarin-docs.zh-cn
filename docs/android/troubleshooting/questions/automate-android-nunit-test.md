---
title: 如何自动化 Android NUnit 测试项目？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: 1246eeac63a0ae232396d4c2fd69d8bf516f5e3e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027002"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何自动化 Android NUnit 测试项目？

> [!NOTE]
> 本指南介绍了如何自动执行 Android NUnit 测试项目，而不是 UITest 项目。 可在[此处](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)找到 UITest 指南。

在 Visual Studio 中创建**单元测试应用（android）** 项目（或 Visual Studio for Mac 中的**Android 单元测试**项目）时，默认情况下，此项目不会自动运行测试。
若要在目标设备上运行 NUnit 测试，你可以创建[一个使用](xref:Android.App.Instrumentation)以下命令启动的子类： 

```shell
adb shell am instrument 
```

以下步骤说明了此过程：

1. 创建名为**TestInstrumentation.cs**的新文件： 

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

    在此文件中，`Xamarin.Android.NUnitLite.TestSuiteInstrumentation` （从**NUnitLite**）创建 `TestInstrumentation`子类。

2. 实现 `TestInstrumentation` 构造函数和 `AddTests` 方法。 `AddTests` 方法控制实际执行的测试。

3. 修改 `.csproj` 文件以添加**TestInstrumentation.cs**。 例如:

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

4. 使用以下命令来运行单元测试。 将 `PACKAGE_NAME` 替换为应用的包名称（包名称可在应用的 `/manifest/@package` 属性（位于**androidmanifest.xml**中）：

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. 或者，您可以修改 `.csproj` 文件，以添加 `RunTests` MSBuild 目标。 这样，就可以使用如下所示的命令调用单元测试：

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    （请注意，不需要使用此新目标; 可以使用前面的 `adb` 命令，而不是 `msbuild`。）

有关使用 `adb shell am instrument` 命令运行单元测试的详细信息，请参阅使用[ADB 运行测试](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)主题的 Android 开发人员。

> [!NOTE]
> 在[Xamarin 5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming)版本中，Android 可调用包装的默认包名称将基于正在导出的类型的程序集限定名称的 MD5SUM。 这允许从两个不同的程序集提供相同的完全限定名称，而不会出现打包错误。 因此，请确保使用 `Instrumentation` 特性的 `Name` 属性生成可读的 ACW/类名称。

_ACW 名称必须在上面 `adb` 命令中使用_。
重命名/重构C#类将需要修改`RunTests`命令才能使用正确的 ACW 名称。
