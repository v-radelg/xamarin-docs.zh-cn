---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: 结合使用 CC++ /库和 Xamarin
description: Visual Studio for Mac 可用于在适用于 Android 和 iOS 的移动应用中C++使用 Xamarin 和C#来构建和集成跨平台 C/代码。 本文介绍如何在 Xamarin 应用程序中设置和C++调试项目。
author: mikeparker104
ms.author: miparker
ms.date: 12/17/2018
ms.openlocfilehash: 685d4f5de197c6b3664d63306fa206bea17409e1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766360"
---
# <a name="use-cc-libraries-with-xamarin"></a>结合使用 CC++ /库和 Xamarin

## <a name="overview"></a>概述

Xamarin 使开发人员能够通过 Visual Studio 创建跨平台的本机移动应用。 通常， C#使用绑定向开发人员公开现有平台组件。 但是，在某些情况下，Xamarin 应用需要使用现有的基本代码。 有时，团队没有时间、预算或资源，无法将大型、经过充分测试且高度优化的代码库移植到C#。

[适用C++于跨平台移动开发的视觉对象](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development)允许将C++ C C# /和代码作为同一解决方案的一部分生成，从而提供了许多优点，包括统一的调试体验。 Microsoft 使用 C/C++和 Xamarin 以这种方式使用了[Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw)和[Pix 摄像机](https://www.microsoft.com/microsoftpix)等应用。

但是，在某些情况下，需要（或要求）保留现有的 C/C++工具和进程，并将库代码与应用程序分离，使库的代码与第三方组件类似。 在这些情况下，质询并不只是公开相关成员， C#而是将库作为依赖项进行管理。 当然，此过程尽可能多地自动执行。  

本文章概述了此方案的高级方法，并演练了一个简单的示例。

## <a name="background"></a>后台

C/C++被视为一种跨平台语言，但必须格外小心，确保源代码确实跨平台，只使用所有目标编译器都支持的 C/C++ ，同时包含很少或没有有条件包含的平台或编译器特定代码。

最终，代码必须在所有目标平台上成功地进行编译和运行，因此，这会归结为所面向的平台（和编译器）的通用性。 问题仍可能是由于编译器之间的细微差异引起的，因此，每个目标平台上的全面测试（最好是自动化）变得越来越重要。  

## <a name="high-level-approach"></a>高级方法

下图表示了用于将 C/C++源代码转换为跨平台 Xamarin 库的四阶段方法，该库通过 NuGet 共享，然后在 Xamarin. Forms 应用中使用。

![使用 C/C++ with Xamarin 的高级方法](images/cpp-steps.jpg)

4个阶段是：

1. 将 C/C++源代码编译为特定于平台的本机库。
2. 使用 Visual Studio 解决方案包装本机库。
3. 为 .NET 包装打包并推送 NuGet 包。
4. 从 Xamarin 应用程序中使用 NuGet 包。

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>阶段1：将 C/C++源代码编译为特定于平台的本机库

此阶段的目标是创建可由C#包装器调用的本机库。 这取决于你的情况。 此常见方案中可以引入的许多工具和过程都超出了本文的讨论范围。 关键注意事项是将 C/C++ codebase 与任何本机包装代码、足够的单元测试和生成自动化保持同步。 

本演练中的库是使用带有附带 shell 脚本的 Visual Studio Code 创建的。 可在[移动 CAT GitHub 存储库](https://github.com/xamarin/mobcat/blob/dev/samples/cpp_with_xamarin/)中找到此演练的扩展版本，其中更深入地讨论了这部分示例。 在这种情况下，本地库将被视为第三方依赖项，但此阶段说明了上下文。

为简单起见，本演练只针对一小部分的体系结构。 对于 iOS，它使用 lipo 实用工具从单独的体系结构特定的二进制文件创建单个 fat 二进制文件。 Android 将使用带有的动态二进制文件。因此，扩展名和 iOS 将使用带有. 扩展名的静态 fat 二进制文件。 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>阶段2：使用 Visual Studio 解决方案包装本机库

下一阶段是包装本机库，以便可以从 .NET 中轻松使用它们。 这是通过包含四个项目的 Visual Studio 解决方案实现的。 共享项目包含通用代码。 面向 Xamarin、Xamarin 和 .NET Standard 的每个项目都允许以与平台无关的方式引用库。

包装器使用 Paul Betts 介绍的 "[bait 和切换技巧](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)"。 这并不是唯一的方法，但使用它可以轻松地引用库，并且无需在使用的应用程序中显式管理特定于平台的实现。 秘诀实质上是确保目标（.NET Standard、Android、iOS）共享相同的命名空间、程序集名称和类结构。 由于 NuGet 始终需要特定于平台的库，因此在运行时从不使用 .NET Standard 版本。

此步骤中的大部分工作将重点介绍如何使用 P/Invoke 调用本机库方法和管理对基础对象的引用。 目标是将库的功能公开给使用者，同时抽象出所有复杂性。 Xamarin 开发人员无需了解有关非托管库的内部工作原理的知识。 它应类似于使用任何其他托管C#库。

最终，此阶段的输出为一组 .NET 库，每个目标一个，以及一个 nuspec 文档，其中包含在下一步中生成包所需的信息。

**阶段3：为 .NET 包装打包并推送 NuGet 包**

第三个阶段是使用上一步中的生成项目创建 NuGet 包。 此步骤的结果是一个 NuGet 包，可从 Xamarin 应用程序中使用。 本演练使用本地目录作为 NuGet 源。 在生产环境中，此步骤应将包发布到公共或专用 NuGet 源，并且应完全自动完成。

**阶段4：从 Xamarin 应用程序使用 NuGet 包**

最后一步是从 Xamarin 应用程序中引用并使用 NuGet 包。 这需要将 Visual Studio 中的 NuGet 源配置为使用上一步中定义的源。

配置源后，需要从跨平台 Xamarin. Forms 应用中的每个项目引用包。 "Bait 墩" 提供相同的接口，因此可以使用在单个位置定义的代码来调用本机库功能。

源代码存储库包含一个[进一步阅读列表](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)，其中包含有关如何在 Azure DevOps 上设置专用 NuGet 源的文章，以及如何将包推送到该源。 尽管需要的设置时间比本地目录要多一些，但这种类型的源在团队开发环境中更好。

## <a name="walk-through"></a>演练

提供的步骤特定于**Visual Studio for Mac**，但结构也适用于**Visual Studio 2017** 。

### <a name="prerequisites"></a>系统必备

为了遵循，开发人员需要：

- [NuGet 命令行（CLI）](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Visual Studio* *for Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> 若要将应用部署到 iPhone，需要使用有效的[**Apple 开发人员帐户**](https://developer.apple.com/)。

## <a name="creating-the-native-libraries-stage-1"></a>创建本机库（阶段1）

本机库功能基于演练中[的示例：创建和使用静态库（C++）。](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017)

本演练将跳过第一个阶段，即生成本机库，因为在此方案中，该库作为第三方依赖项提供。 预编译的本机库随[示例代码](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin)一起提供，也可直接[下载](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts)。

### <a name="working-with-the-native-library"></a>使用本机库

原始*MathFuncsLib*示例包含一个名`MyMathFuncs`为的类，其中包含以下定义：

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

另外一个类定义包装函数，使 .net 使用者能够创建、释放和与基础本机`MyMathFuncs`类交互。

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

它将是在[Xamarin](https://visualstudio.microsoft.com/xamarin/) 端使用的这些包装函数。

## <a name="wrapping-the-native-library-stage-2"></a>包装本机库（阶段2）

此阶段需要[上一节](##creating-the-native-libraries-stage-1)中所述的[预编译库](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts)。

### <a name="creating-the-visual-studio-solution"></a>创建 Visual Studio 解决方案

1. 在**Visual Studio for Mac**中，单击 "**新建项目**" （从 "*欢迎" 页*）或 "**新建解决方案**" （从 "*文件*" 菜单）。
2. 在 "**新建项目**" 窗口中，选择 "**共享项目**" （从多*平台 > 库*中选择），然后单击 "**下一步**"。
3. 更新以下字段，然后单击 "**创建**"：

    - **项目名称：** MathFuncs.Shared  
    - **解决方案名称：** MathFuncs  
    - **位置**使用默认保存位置（或选择备用位置）   
    - **在解决方案目录中创建项目：** 将此设置为选中状态
4. 在**解决方案资源管理器**中，双击 " **MathFuncs** " 项目并导航到 "**主设置**"。
5. 删除 **。共享**自**默认命名空间**，以便仅将其设置为**MathFuncs** ，然后单击 **"确定"** 。
6. 打开**MyClass.cs** （由模板创建），然后将类和文件名重命名为**MyMathFuncsWrapper** ，并将命名空间更改为**MathFuncs**。
7. **CONTROL + 单击**解决方案**MathFuncs**，然后从 "**添加**" 菜单中选择 "**添加新项目 ...** "。
8. 在 "**新建项目**" 窗口中，选择 " **.NET Standard 库**" （从多*平台 > 库*中选择），然后单击 "**下一步**"。
9. 选择 **.NET Standard 2.0** ，然后单击 "**下一步**"。
10. 更新以下字段，然后单击 "**创建**"：

    - **项目名称：** MathFuncs.Standard  
    - **位置**使用与共享项目相同的保存位置   

11. 在**解决方案资源管理器**中，双击 " **MathFuncs** " 项目。
12. 导航到 "**主设置**"，然后将**默认命名空间**更新为**MathFuncs**。
13. 导航到 "**输出**" 设置，然后将**程序集名称**更新为**MathFuncs**。
14. 导航到**编译器**设置，将**配置**更改为 "**发布**"，将 "**调试信息**" 设置为 "**仅限符号**"，然后单击 **"确定"** 。
15. 从项目中删除已**启动的 Class1.cs/Getting** （如果其中一项已作为模板的一部分包含在内）。
16. **控件并单击**"项目**依赖项/引用**" 文件夹，然后选择 "**编辑引用**"。
17. 从 "**项目**" 选项卡中选择 " **MathFuncs** "，然后单击 **"确定"** 。
18. 使用以下配置重复步骤7-17 （忽略步骤9）：

    | **项目名称**  | **模板名称**   | **新建项目菜单**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | 类库       | Android > 库      |
    | MathFuncs.iOS     | 绑定库     | iOS > 库          |

19. 在**解决方案资源管理器**中，双击 " **MathFuncs** " 项目，然后导航到**编译器**设置。

20. 将**配置**设置为 "**调试**" 后，编辑 "定义要包含**Android**的**符号**"。

21. 将**配置**更改为 "**发布**"，然后编辑 "**定义符号**"，同时包含**Android;** 。

22. 对于**MathFuncs**重复步骤19-20，在这两种情况下编辑定义要包含**iOS**的**符号**（而不是**Android** ）。

23. 在**发布**配置（**CONTROL + COMMAND + B**）中生成解决方案，并验证所有三个输出程序集（Android、iOS、.NET Standard）（位于各自的项目箱文件夹中）共享相同的名称**MathFuncs**。

在此阶段，解决方案应有三个目标，一个各适用于 Android、iOS 和 .NET Standard，以及由三个目标中每个目标引用的共享项目。 应将这些配置为使用相同的默认命名空间和具有相同名称的输出程序集。 这对于前面提到的 "bait 和交换机" 方法是必需的。

### <a name="adding-the-native-libraries"></a>添加本机库

将本机库添加到包装解决方案的过程在 Android 和 iOS 之间略有不同。

#### <a name="native-references-for-mathfuncsandroid"></a>MathFuncs 的本机引用

1. **控件并单击**" **MathFuncs** " 项目，然后从 "**添加**" 菜单命名 "库" 中选择 "**新建文件夹** **"。**

2. 对于每个**ABI** （应用程序二进制接口），**控制 + 单击**"库 **" 文件夹，** 然后从 "**添加**" 菜单中选择 "**新建文件夹**"，并在相应的**ABI**之后对其命名。 这种情况下：

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > 有关更详细的概述，请参阅 " [NDK 开发人员指南](https://developer.android.com/ndk/guides/)" 中的 "[体系结构和 cpu](https://developer.android.com/ndk/guides/arch) " 主题，尤其是对[应用包中的本机代码进行](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages)寻址的部分。

3. 验证文件夹结构：  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. 根据以下映射**将相应的**添加到每个**ABI**文件夹中：

    **arm64-arm64-v8a：** Lib/Android/arm64

    **armeabi-armeabi-v7a：** 库/Android/arm  

    **x86：** 库/Android/x86

    **x86_64：** 库/Android/x86_64

    > [!NOTE]
    > 若要添加文件，请**单击控件并单击**代表各自**ABI**的文件夹，然后从 "**添加**" 菜单中选择 "**添加文件 ...** "。 选择相应的库（从**PrecompiledLibs**目录），单击 "**打开**"，然后单击 **"确定**"，将*文件复制到目录*。

5. 对于每**个文件，请按** **ctrl 并单击**，然后从 "**生成操作**" 菜单中选择 " **EmbeddedNativeLibrary** " 选项。

现在，**库文件夹应如下所示**：

```folders
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86
        - libMathFuncs.so
    - x86_64
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>MathFuncs 的本机引用

1. **控件并单击**" **MathFuncs** " 项目，然后从 "**添加**" 菜单中选择 "**添加本机引用**"。 
2. 选择**libMathFuncs** （从库/Ios 到**PrecompiledLibs**目录下），然后单击 "**打开**" 
3. **控件并单击**"**本机引用**" 文件夹中的**libMathFuncs**文件，然后从菜单中选择 "**属性**" 选项  
4. 配置**本机引用**属性，以便在**properties** Pad 中选中（显示勾选标记图标）：

    - 强制加载
    - 未C++
    - 智能链接

    > [!NOTE]
    > 结合使用绑定库项目类型和[本机引用](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references)将嵌入静态库，并使其与引用它的 Xamarin iOS 应用自动链接（即使它是通过 NuGet 包提供的）。

5. 打开**ApiDefinition.cs**，删除模板化注释代码（仅`MathFuncs`保留命名空间），然后对**Structs.cs**执行相同的步骤。 

    > [!NOTE]
    > 绑定库项目需要这些文件（包括*ObjCBindingApiDefinition*和*ObjCBindingCoreSource*生成操作）才能生成。 但是，我们将编写代码，以便以可使用标准 P/Invoke 在 Android 和 iOS 库目标之间共享的方式在这些文件之外调用我们的本机库。

### <a name="writing-the-managed-library-code"></a>编写托管库代码

现在，编写C#代码来调用本机库。 目标是隐藏所有底层复杂性。 使用者不应需要任何有关本机库内部机制或 P/Invoke 概念的工作知识。  

#### <a name="creating-a-safehandle"></a>创建 SafeHandle

1. **控件并单击**" **MathFuncs** " 项目，然后从 "**添加**" 菜单中选择 "**添加文件 ...** "。 
2. 从 "**新建文件**" 窗口中选择 "**空类**"，将其命名为**MyMathFuncsSafeHandle** ，然后单击 "**新建**"
3. 实现**MyMathFuncsSafeHandle**类：

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => this.handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2)是在托管代码中使用非托管资源的首选方法。 这就进一步抽象了许多与关键终结和对象生命周期相关的样板代码。 然后，此句柄的所有者可以像对待任何其他托管资源一样对其进行处理，而无需实现完全可[释放的模式](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)。 

#### <a name="creating-the-internal-wrapper-class"></a>创建内部包装类

1. 打开**MyMathFuncsWrapper.cs**，将其更改为内部静态类

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. 在同一文件中，将以下条件语句添加到类：

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > 这将根据是否为**Android**或**iOS**生成**DllName**常量值。 这是为了解决各自平台使用的不同命名约定，以及在此情况下使用的库类型。 Android 使用动态库，因此需要包含扩展名的文件名。 对于 iOS，" *__Internal*" 是必需的，因为我们使用的是静态库。

3. 在**MyMathFuncsWrapper.cs**文件的顶部添加对**InteropServices**的引用

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. 添加包装方法以处理**MyMathFuncs**类的创建和处置：

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > 我们会将常量**DllName**传递到**DllImport**属性，并将一个**入口点**显式告知 .net 运行时在该库中调用的函数的名称。 从技术上说，如果托管方法名称与非托管方法名称相同，则不需要提供**入口点**值。 如果未提供，则托管方法名称将改为用作**入口点**。 不过，最好是显式的。  

5. 添加包装方法，使我们可以使用以下代码处理**MyMathFuncs**类：

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > 在此示例中，我们使用简单类型作为参数。 由于在这种情况下，封送是按位复制的，因此不需要在我们的部分中进行其他工作。 另请注意，使用**MyMathFuncsSafeHandle**类而不是标准**IntPtr**。 在封送处理过程中， **IntPtr**会自动映射到**SafeHandle** 。

6. 验证完成的**MyMathFuncsWrapper**类是否显示如下：

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>完成 MyMathFuncsSafeHandle 类

1. 打开**MyMathFuncsSafeHandle**类，导航到**ReleaseHandle**方法中的占位符**TODO**注释：

    ```csharp
    // TODO: Release the handle here
    ```

1. 替换**TODO**行：

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(this);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>编写 MyMathFuncs 类

完成包装后，创建一个 MyMathFuncs 类，该类将管理对非托管C++ MyMathFuncs 对象的引用。  

1. **控件并单击**" **MathFuncs** " 项目，然后从 "**添加**" 菜单中选择 "**添加文件 ...** "。 
2. 从 "**新建文件**" 窗口中选择 "**空类**"，将其命名为**MyMathFuncs** ，然后单击 "**新建**"
3. 将以下成员添加到**MyMathFuncs**类：

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. 实现类的构造函数，以便在对类进行实例化时，它将创建一个句柄并将其存储到本机**MyMathFuncs**对象：

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. 使用以下代码实现**IDisposable**接口：

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. 使用**MyMathFuncsWrapper**类实现**MyMathFuncs**方法，通过传入已存储到基础非托管对象的指针，在后台执行实际工作。 代码应如下所示：

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>创建 nuspec

为了通过 NuGet 打包和分发库，解决方案需要**nuspec**文件。 这会确定将为每个受支持的平台包括哪个生成的程序集。

1. **CONTROL + 单击**解决方案**MathFuncs**，然后从 "**添加**" 菜单中选择 " **SolutionItems**"，然后选择 "**添加解决方案文件夹**"。
2. **CONTROL + 单击** **SolutionItems**文件夹，然后从 "**添加**" 菜单中选择 "**新建文件 ...** "。
3. 从 "**新建文件**" 窗口中选择 "**空白 XML 文件**"，将其命名为**MathFuncs. Nuspec** ，然后单击 "**新建**"。
4. 使用要显示到**NuGet**使用者的基本包元数据更新**MathFuncs。** 例如:

    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    > 有关用于此清单的架构的详细信息，请参阅[nuspec 参考](https://docs.microsoft.com/nuget/reference/nuspec)文档。

5. 添加元素作为元素的子元素（ `<package>`如下所示`<metadata>`），并用单独`<file>`的元素标识每个文件： `<files>`

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > 当包安装到项目中，并且有多个程序集由同一名称指定时，NuGet 将有效地选择最特定于给定平台的程序集。

6. 为**Android**程序集添加元素：`<file>`

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. 为**iOS**程序集添加元素：`<file>`

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. 为**netstandard 2.0**程序集添加元素：`<file>`

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. 验证**nuspec**清单：

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>

        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > 此文件指定**发布**版本中的程序集输出路径，因此请确保使用该配置生成解决方案。

此时，该解决方案包含3个 .NET 程序集和一个支持**nuspec**的清单。

## <a name="distributing-the-net-wrapper-with-nuget"></a>通过 NuGet 分发 .NET 包装

下一步是打包和分发 NuGet 包，以便应用程序可以轻松地使用它，并将其作为依赖项进行管理。 包装和使用可以在单个解决方案内完成，但通过将 NuGet 辅助功能分散在一起，使我们能够独立管理这些代码库。

### <a name="preparing-a-local-packages-directory"></a>准备本地包目录

NuGet 源的最简单形式是本地目录：

1. 在**查找**器中，导航到适当的目录。 例如， **/Users**。
2. 从 "**文件**" 菜单中选择 "**新建文件夹**"，提供一个有意义的名称，例如 "**本地-nuget-源**"。

### <a name="creating-the-package"></a>创建包

1. 将**生成配置**设置为 "**发布**"，并使用**命令 + B**执行生成。
2. 打开**终端**并将目录更改为包含**nuspec**文件的文件夹。
3. 在**终端**中，使用[上一步](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed)中创建的文件夹，执行用于 **指定 nuspec 文件、版本（例如1.0.0）和 OutputDirectory 的 nuget 包命令，即本地-nuget**。 例如：

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **确认**已在**本地 nuget 源**目录中创建**MathFuncs nupkg** 。

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>可有可无使用 Azure DevOps 的专用 NuGet 源

[Azure DevOps 中的 NuGet 包入门](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package)中介绍了一种更可靠的技术，其中介绍了如何创建专用源并将包（在上一步中生成）推送到该源。

最好让此工作流完全自动化，例如使用[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)。 有关详细信息，请参阅[Azure Pipelines 入门](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts)。

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>从 Xamarin 应用程序使用 .NET 包装

若要完成本演练，请创建一个**Xamarin**应用程序，以使用刚刚发布到本地**NuGet**源的包。

### <a name="creating-the-xamarinforms-project"></a>创建**Xamarin. Forms**项目

1. 打开**Visual Studio for Mac**的新实例。 这可通过**终端**完成：

    ```bash
    open -n -a "Visual Studio"
    ```

2. 在**Visual Studio for Mac**中，单击 "**新建项目**" （从 "*欢迎" 页*）或 "**新建解决方案**" （从 "*文件*" 菜单）。
3. 在 "**新建项目**" 窗口中，选择 "**空白窗体应用**" （从多*平台 > 应用*中），然后单击 "**下一步**"。
4. 更新以下字段，然后单击 "**下一步**"：

    - **应用名称：** MathFuncsApp.
    - **组织标识符：** 使用反向命名空间，例如， _com. {your_org}_ 。
    - **目标平台：** 使用默认值（Android 和 iOS 目标）。
    - **共享代码：** 将此设置为 ".NET Standard" （可以使用 "共享库" 解决方案，但不能超出本演练的范围）。

5. 更新以下字段，然后单击 "**创建**"：

    - **项目名称：** MathFuncsApp.
    - **解决方案名称：** MathFuncsApp.  
    - **位置**使用默认保存位置（或选择替代项）。

6. 在**解决方案资源管理器**中， **CONTROL + 单击**目标（**MathFuncsApp**或**MathFuncs**）进行初始测试，然后选择 "**设为启动项目**"。
7. 选择首选**设备**或**模拟器**/**模拟器**。 
8. 运行解决方案（**COMMAND + RETURN**）来验证模板化**Xamarin**项目是否生成并运行正常。 

    > [!NOTE]
    > **iOS**（具体而言，模拟器）往往会获得最快的生成/部署时间。

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>将本地 NuGet 源添加到 NuGet 配置

1. 在**Visual studio**中，从**visual studio**菜单中选择 "**首选项**"。
2. 从 " **NuGet** " 部分中选择 "**源**"，然后单击 "**添加**"。
3. 更新以下字段，然后单击 "**添加源**"：

    - **Name：** 提供有意义的名称，例如本地包。  
    - **位置**指定在[上一步](#preparing-a-local-packages-directory)中创建的**本地-nuget 源**文件夹。

    > [!NOTE]
    > 在这种情况下，无需指定**用户名**和**密码**。 

4. 单击 **“确定”** 。

### <a name="referencing-the-package"></a>引用包

为每个项目重复以下步骤（**MathFuncsApp**、 **MathFuncsApp**和**MathFuncsApp**）。

1. **控制 + 单击**该项目，然后从 "**添加**" 菜单中选择 "**添加 NuGet 包 ...** "。
2. 搜索**MathFuncs**。 
3. 验证包的**版本**是否为**1.0.0** ，并按预期显示其他详细信息，例如**标题**和**说明**，即 " *MathFuncs* " 和 "*示例C++包装库*"。 
4. 选择**MathFuncs**包，并单击 "**添加包**"。

### <a name="using-the-library-functions"></a>使用库函数

现在，通过对每个项目中的**MathFuncs**包的引用，这些函数可用于C#代码。

1. 从**MathFuncsApp**公共**Xamarin. Forms**项目中打开**MainPage.xaml.cs** （由**MathFuncsApp**和**MathFuncsApp**同时引用）。
2. 在该**文件的顶部**为 "MathFuncs" 和 " " 添加**using**语句：

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. 在`MainPage`类的顶部声明`MyMathFuncs`类的实例：

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. `OnDisappearing` `OnAppearing` 从`ContentPage`基类重写和方法：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. 更新方法以初始化之前声明`myMathFuncs`的变量： `OnAppearing`

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. 更新方法以对`Dispose`调用方法`myMathFuncs`： `OnDisappearing`

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. 实现名为**TestMathFuncs**的私有方法，如下所示：

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. 最后，在`TestMathFuncs` `OnAppearing`方法的末尾调用：

    ```csharp
    TestMathFuncs();
    ```

9. 在每个目标平台上运行该应用，并在**应用程序输出**板中验证输出，如下所示：

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > 如果在 Android 上进行测试时遇到 "*system.dllnotfoundexception*"，或者 iOS 上出现生成错误，请务必检查正在使用的设备/模拟器/模拟器的 CPU 体系结构是否与我们选择支持的子集兼容。 

## <a name="summary"></a>总结

本文介绍了如何创建使用本机库的 Xamarin 应用程序，该应用通过通过 NuGet 包分发的通用 .NET 包装器来使用。 本演练中提供的示例特意非常简单，以便更轻松地演示该方法。 实际的应用程序必须处理复杂性，如异常处理、回调、更复杂类型的封送处理以及与其他依赖项库的链接。 一个重要的考虑因素是C++代码的演变与包装和客户端应用程序进行协调和同步的过程。 此过程可能会有所不同，具体取决于其中一个或两个问题是否由单个团队负责。 无论采用哪种方式，自动化都是真正的好处。 下面是一些资源，用于进一步了解一些关键概念以及相关的下载内容。 

### <a name="downloads"></a>下载

- [NuGet 命令行（CLI）工具](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>示例

- [Hyperlapse 跨平台移动开发C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix （C++和 Xamarin）](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Mono San 洛杉矶示例端口](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>其他阅读材料

[与此文章内容相关的文章](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)
