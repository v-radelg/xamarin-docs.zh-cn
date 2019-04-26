---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: 使用 C /C++使用 Xamarin 的库
description: Visual Studio for Mac 可用于生成和集成跨平台 C /C++适用于 Android 和 iOS，使用 Xamarin 移动应用代码和C#。 此文章介绍了如何设置和调试C++Xamarin 应用中的项目。
author: mikeparker104
ms.author: miparker
ms.date: 12/17/2018
ms.openlocfilehash: a235a24d544e938d4bf29e6569564aface2f6972
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275562"
---
# <a name="use-cc-libraries-with-xamarin"></a>使用 C /C++使用 Xamarin 的库

## <a name="overview"></a>概述

Xamarin 开发人员可以使用 Visual Studio 创建跨平台本机移动应用。 通常情况下，C#使用绑定来公开给开发人员的现有平台组件。 但是，有些的时候当 Xamarin 应用程序需要使用现有代码库。 有时团队只是没有到基本代码的时间、 预算或一个大型的、 经过全面测试，并且高度优化的端口的资源C#。

[VisualC++适用于跨平台移动开发](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development)启用 C /C++和C#作为同一个解决方案，提供很多优点，包括获得统一调试体验的一部分生成代码。 Microsoft 使用 C /C++和以这种方式，如交付应用的 Xamarin [Hyperlapse 移动版](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw)并[Pix 照相机](https://www.microsoft.com/microsoftpix)。

但是，在某些情况下没有一个愿望 （或要求） 以保留现有 C /C++的工具和过程中的位置和保持分离的应用程序，将库作为它类似于第三方组件的库代码。 在这些情况下，面临的挑战不只公开到相关的成员C#，但管理作为依赖项的库。 和，当然，作为此过程的大部分尽可能自动执行。  

本文概述了此方案的高级方法，并指导完成一个简单的示例。

## <a name="background"></a>背景

C /C++被视为一种跨平台语言，但必须格外谨慎以确保源代码是确实跨平台的使用仅 C /C++支持的所有目标编译器和包含很少或没有有条件地包括平台或特定于编译器的代码。

最终代码必须编译并在所有目标平台，因此这归结为通用性跨目标平台 （和编译器） 上成功运行。 问题可能仍起源于编译器的细微差别，因此全面测试 （最好是自动进行） 上的每个目标平台变得日益重要。  

## <a name="high-level-approach"></a>高级方法

下图表示四阶段方法，用于转换 C /C++到通过 NuGet 共享，然后使用 Xamarin.Forms 应用中的跨平台 Xamarin 库源代码。
 

![使用 C 的高级方法 /C++使用 Xamarin](images/cpp-steps.jpg)

4 阶段包括：

1.  编译 C /C++到特定于平台的本机库的源代码。
2.  包装本机库与 Visual Studio 解决方案。
3.  打包和推送.NET 包装的 NuGet 包。
4.  使用 Xamarin 应用程序中的 NuGet 包。

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>第 1 阶段：编译 C /C++到特定于平台的本机库源代码

此阶段的目标是创建可由调用的本机库C#包装器。 这可能会也可能不是相关具体取决于你的情况。 许多工具和可以用来解决这种常见方案中的进程已超出本文的讨论范围。 重要注意事项： 保持 C /C++基本代码的任何本机打包程序代码，足够单元测试、 与同步和生成自动化。 

在预排中的库创建 Visual Studio Code 中使用随附的 shell 脚本。 本演练中的扩展的版本可在[移动 CAT GitHub 存储库](https://github.com/xamarin/mobcat/blob/dev/samples/cppwithxamarin/README.md)，其中介绍了此部分中更深入地示例。 本机库都被视为第三方依赖项在此情况下但是针对上下文进行了说明此阶段。


为简单起见，本演练面向体系结构的一个子集。 对于 iOS，它使用 lipo 实用程序来创建单个 fat 二进制从单个特定于体系结构的二进制文件。 Android 将.so 扩展名为使用动态二进制文件，iOS 将.a 扩展名为使用静态 fat 二进制。 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>阶段 2:包装本机库与 Visual Studio 解决方案

下一阶段是包装本机库，以便轻松地从.NET 使用它们。 这是通过具有四个项目的 Visual Studio 解决方案。 共享的项目包含通用代码。 面向每个 Xamarin.Android、 Xamarin.iOS、 和.NET Standard 项目允许以平台未知的方式引用的库。

包装器使用[bait 和 switch 技巧](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)，由 Paul Betts 所述。 这不是唯一的方法，但它可以轻松引用的库和它，就无需显式管理中使用应用程序本身的特定于平台的实现。 技巧就实质上确保目标 (.NET Standard 的 Android、 iOS) 共享相同的命名空间、 程序集名称和类结构。 由于 NuGet 将始终优先使用特定于平台的库，则永远不会在运行时使用.NET Standard 版本。

在此步骤中的工作的大部分将重点介绍使用 P/Invoke 调用的本机库方法和管理对基础对象的引用。 目标是在复杂结构中分离出来的同时将库的功能提供给使用者。 Xamarin.Forms 开发人员不需要具有知识上的非托管库的内部工作机制。 它们使用任何其他托管觉得它C#库。

从根本上讲，此阶段的输出是一组.NET 库，每个目标，以及包含为了构建下一步中的包所需的信息的 nuspec 文档之一。

**第 3 阶段：打包和推送.NET 包装的 NuGet 包**

第三个阶段创建使用从上一步骤生成项目的 NuGet 包。 此步骤中的结果是一个 NuGet 包，可从 Xamarin 应用程序使用。 本演练使用本地目录作为 NuGet 源。 在生产中，此步骤应将包发布到公共或私有 NuGet 源，并应完全自动化。

**阶段 4:使用 Xamarin.Forms 应用中的 NuGet 包**

最后一步是引用和使用 Xamarin.Forms 应用中的 NuGet 包。 这需要在 Visual Studio 以使用在上一步中定义的源中配置 NuGet 源。

配置源后，包将需要从跨平台 Xamarin.Forms 应用中的每个项目引用。 Bait 和交换机技巧提供的相同接口，因此可以使用单个位置中定义的代码调用本机库功能。

源代码存储库包含[的更多信息，请阅读列表](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)包括有关如何设置私有 NuGet 源上 Azure DevOps 和如何将包推送到源中的文章。 同时需要与本地目录的更多安装时，此类型是源的团队开发环境中更好。

## <a name="walk-through"></a>演练

提供的步骤是特定于**Visual Studio for Mac**，但结构适用于**Visual Studio 2017**也。

### <a name="prerequisites"></a>系统必备

若要跟着介绍一起操作，将需要开发人员：

-   [NuGet 命令行 (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

-   [*Visual Studio* *for Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> 一个有效[ **Apple 开发人员帐户**](https://developer.apple.com/)才能将应用部署到 iPhone。

## <a name="creating-the-native-libraries-stage-1"></a>创建本机库 (阶段 1)

本机库功能基于从示例[演练：创建和使用静态库 (C++)](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017)。

本演练将跳过第一阶段，因为库提供为在此方案中的第三方依赖项生成本机库。 预编译的本机库随附[示例代码](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin)也可以是[下载](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts)直接。

### <a name="working-with-the-native-library"></a>使用本机库

原始*MathFuncsLib*示例包含以下定义名为 MyMathFuncs 的单个类： 

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

其他类定义允许.NET 使用者创建、 释放，并与基础本机 MyMathFuncs 类进行交互的包装器函数。

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

它将是在使用这些包装器函数[Xamarin](https://visualstudio.microsoft.com/xamarin/) 端。

## <a name="wrapping-the-native-library-stage-2"></a>包装本机库 (阶段 2)

此阶段需要[预编译库](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts)中所述[上一节](https://docs.microsoft.com/xamarin/cross-platform/cpp/index)。

### <a name="creating-the-visual-studio-solution"></a>创建 Visual Studio 解决方案

1. 在中**Visual Studio for Mac**，单击**新项目**(从*欢迎页*) 或**新解决方案**(从*文件*菜单上)。
2. 从**新的项目**窗口中，选择**共享项目**(从*多平台 > 库*)，然后单击**下一步**。
3. 更新的以下字段，然后单击**创建**:

    - **项目名称：** MathFuncs.Shared  
    - **解决方案名称：** MathFuncs  
    - **位置：** 使用默认的保存位置 （或选择一种替代方法）   
    - **创建解决方案目录中的项目：** 将此项检查
4. 从**解决方案资源管理器**，双击**MathFuncs.Shared**项目，然后导航到**Main 设置**。
5. 删除 **。共享**从**Default Namespace**因此设置为**MathFuncs** ，并单击**确定**。
6. 打开**MyClass.cs** （由模板创建），然后重命名类和文件名**MyMathFuncsWrapper**并更改到命名空间**MathFuncs**。
7. **控件的同时单击**解决方案**MathFuncs**，然后选择**添加新项目...** 从**添加**菜单。
8. 从**新的项目**窗口中，选择 **.NET 标准库**(从*多平台 > 库*)，然后单击**下一步**。
9. 选择 **.NET Standard 2.0** ，然后单击**下一步**。
10. 更新的以下字段，然后单击**创建**:

    - **项目名称：** MathFuncs.Standard  
    - **位置：** 保存与共享项目的位置使用相同   

11. 从**解决方案资源管理器**，双击**MathFuncs.Standard**项目。
12. 导航到**Main 设置**，然后更新**Default Namespace**到**MathFuncs**。
13. 导航到**输出**设置，然后更新**程序集名称**到**MathFuncs**。
14. 导航到**编译器**设置，更改**配置**到**版本**，并设置**调试信息**到**仅符号**然后单击**确定**。
15. 删除**Class1.cs/Getting 启动**从项目 （如果其中一种已作为模板的一部分）。
16. **控件的同时单击**项目**依赖项 / 引用**文件夹，然后选择**编辑引用**。
17. 选择**MathFuncs.Shared**从**项目**选项卡，然后单击**确定**。
18. 重复步骤 7-17 （忽略步骤 9 中） 使用以下配置：

    | **项目名称**  | **TEMPLATE NAME**   | **新建项目菜单**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | 类库       | Android > 库      |
    | MathFuncs.iOS     | 绑定库     | iOS > 库          |

19. 从**解决方案资源管理器**，双击**MathFuncs.Android**项目，然后导航到**编译器**设置。

20. 与**配置**设置为**调试**，编辑**定义符号**包括**Android;**。

21. 更改**配置**到**发行**，然后编辑**定义符号**还包括**Android;**。

22. 重复步骤 19-20，对应**MathFuncs.iOS**，编辑**定义符号**包括**iOS;** 而不是**Android;** 这两种情况。

23. 生成解决方案**发行**配置 (**控制 + COMMAND + B**) 和验证 （在相应项目的 bin 文件夹） 的所有三个输出程序集 (Android、 iOS、.NET Standard) 共用同一个名称**MathFuncs.dll**。

在此阶段，解决方案应具有三个目标，另一个各用于 Android、 iOS 和.NET Standard 和共享的项目引用的每个三个目标。 这些应配置为使用相同的默认命名空间和输出程序集具有相同的名称。 这是必要的前面提到的 bait 和 switch 方法。

### <a name="adding-the-native-libraries"></a>添加本机库

Android 和 iOS 之间，将本机库添加到包装器解决方案的过程稍有不同。

#### <a name="native-references-for-mathfuncsandroid"></a>有关 MathFuncs.Android 本机引用

1. **控件的同时单击**上**MathFuncs.Android**项目，然后选择**新文件夹**从**添加**菜单其命名为**libs**.

2. 每个**ABI** （应用程序二进制接口），**控件的同时单击**上**libs**文件夹，然后选择**新文件夹**从**添加**菜单上，在此之后将其命名各自**ABI**。 这种情况下：

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > 有关更详细的概述，请参阅[体系结构和 Cpu](https://developer.android.com/ndk/guides/arch)从主题[NDK 开发人员指南](https://developer.android.com/ndk/guides/)，特别是对寻址的部分[应用程序包中的本机代码](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. 验证文件夹结构：  

    ```
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. 添加相应的 **.so**到每个库**ABI**文件夹基于以下映射：

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/arm  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > 若要将文件，添加**控制的同时单击**上表示相应的文件夹**ABI**，然后选择**添加文件...** 从**添加**菜单。 选择适当的库 (从**PrecompiledLibs**目录) 然后单击**打开**，然后单击**确定**保留默认选项为*复制文件的目录*。

5. 为每个 **.so**文件，**控件的同时单击**然后选择**EmbeddedNativeLibrary**选项**生成操作**菜单。

现在**libs**文件夹应出现，如下所示：

```bash
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

#### <a name="native-references-for-mathfuncsios"></a>有关 MathFuncs.iOS 本机引用

1. **控件的同时单击**上**MathFuncs.iOS**项目，然后选择**添加本机引用**从**添加**菜单。 
2. 选择**libMathFuncs.a**库 (lib/ios 下从**PrecompiledLibs**目录) 然后单击**打开** 
3. **控件的同时单击**上**libMathFuncs**文件 (内**本机引用**文件夹，然后选择**属性**从菜单选项  
4. 配置**本机引用**属性，让他们检查 （显示勾图标） 中**属性**板：
        
    - 强制加载
    - 是C++
    - 智能链接 

    > [!NOTE]
    > 使用绑定库的项目类型，连同[本机引用](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references)嵌入静态库，并使它能够自动链接 （即使它是包含通过 NuGet 包） 引用它的 Xamarin.iOS 应用。 

5. 打开**ApiDefinition.cs**，删除的模板化注释代码 (并仅留下**MathFuncs**命名空间)，然后执行同样的步骤**Structs.cs** 

    > [!NOTE]
    > 绑定库项目需要这些文件 (与*ObjCBindingApiDefinition*并*ObjCBindingCoreSource*生成操作) 才能生成。 但是，我们将编写代码，以调用我们的本机库，以一种可以使用标准的 P/Invoke 的 Android 和 iOS 库目标之间共享这些文件之外。

### <a name="writing-the-managed-library-code"></a>编写托管的库代码

现在，编写C#代码调用本机库。 目标是隐藏底层的复杂性。 使用者无需任何本机库内部信息或 P/Invoke 概念的应用知识。  

#### <a name="creating-a-safehandle"></a>创建 SafeHandle

1. **控件的同时单击**上**MathFuncs.Shared**项目，然后选择**添加文件...** 从**添加**菜单。 
2. 选择**的空类**从**新的文件**窗口中，其命名为**MyMathFuncsSafeHandle** ，然后单击**新建**
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
    > 一个[SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2)是首选的方法来处理托管代码中的非托管资源。 此抽象出大量的样板代码与相关的关键终结和对象生命周期。 此句柄的所有者可以随后将它视为像任何其他托管资源和将无需实现完整[Disposable 模式](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)。 

#### <a name="creating-the-internal-wrapper-class"></a>创建内部包装类

1. 打开**MyMathFuncsWrapper.cs**，更改为静态的内部类

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
    > 这将设置**DllName**库为生成的还是基于常数值**Android**或**iOS**。 这是库的为了解决不同的命名约定使用的每个各自的平台，但也在这种情况下使用的类型。 Android 使用动态库，并因此需要包括扩展名的文件名。 对于 iOS，'*__Internal*是必需的因为我们要使用静态库。

3. 添加对的引用**System.Runtime.InteropServices**顶部**MyMathFuncsWrapper.cs**文件

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. 添加包装器方法，以处理创建和处置**MyMathFuncs**类：

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > 我们传入的常量**DllName**到**DllImport**属性连同**EntryPoint**的显式告知.NET 运行时要调用的函数的名称中的库。 从技术上讲，我们不需要提供**入口点**值如果我们的托管的方法名称等同于非托管代码。 如果未提供，托管的方法名称将用作**入口点**相反。 但是，它是更好的做法是显式的。  

5. 添加包装器方法，以使我们能够使用**MyMathFuncs**类使用以下代码：

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
    > 我们在此示例中的参数使用简单类型。 由于封送处理是按位复制在这种情况下它需要我们任何额外工作。 此外请注意，使用**MyMathFuncsSafeHandle**类而不是标准**IntPtr**。 **IntPtr**自动映射到**SafeHandle**封送处理过程的一部分。

6. 验证是否已完成**MyMathFuncsWrapper**类显示为如下：

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
1. 打开**MyMathFuncsSafeHandle**类中，导航到占位符**TODO**注释内**ReleaseHandle**方法：
    ```csharp
    // TODO: Release the handle here
    ```
2. 替换**TODO**行：

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(this);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>编写 MyMathFuncs 类

包装过程完成之后，创建将管理对非托管引用一个 MyMathFuncs 类C++MyMathFuncs 对象。  

1. **控件的同时单击**上**MathFuncs.Shared**项目，然后选择**添加文件...** 从**添加**菜单。 
2. 选择**的空类**从**新的文件**窗口中，其命名为**MyMathFuncs** ，然后单击**新建**
3. 添加到以下成员**MyMathFuncs**类：

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. 实现类的构造函数，因此它会创建并存储的本机句柄**MyMathFuncs**对象实例化类时：

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. 实现**IDisposable**接口使用以下代码：

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

6. 实现**MyMathFuncs**方法使用**MyMathFuncsWrapper**类来执行实际工作实质上通过传入我们存储的指针的基础的非托管对象。 代码应如下所示：

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

若要将库一起打包并通过 NuGet 分发，需求的解决方案**nuspec**文件。 这将确定哪些生成的程序集将包含有关每个受支持的平台。

1.  **控件的同时单击**解决方案**MathFuncs**，然后选择**添加解决方案文件夹**从**添加**菜单其命名为**SolutionItems**.
2.  **控件的同时单击**上**SolutionItems**文件夹，然后选择**新文件...** 从**添加**菜单。
3.  选择**空 XML 文件**从**新的文件**窗口中，其命名为**MathFuncs.nuspec** ，然后单击**新建**。
4.  更新**MathFuncs.nuspec**与要向显示的基本包元数据**NuGet**使用者。 例如：


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
    >  请参阅[nuspec 引用](https://docs.microsoft.com/nuget/reference/nuspec)进一步详细信息用于进行此清单的架构的文档。

5. 添加`<files>`的子元素`<package>`元素 (正下方`<metadata>`)，标识每个文件具有单独`<file>`元素：

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->        

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > 当包安装到项目，以及其中有多个指定的相同名称的程序集，NuGet 将有效地选择最具体到给定平台的程序集。

6. 添加`<file>`的元素**Android**程序集：

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. 添加`<file>`的元素**iOS**程序集：

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. 添加`<file>`的元素**netstandard2.0**程序集：

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. 验证是否**nuspec**清单：

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
    > 此文件指定的程序集输出路径从**版本**构建，因此请确保使用该配置生成解决方案。

此时，该解决方案包含 3 个.NET 程序集和支持**nuspec**清单。

## <a name="distributing-the-net-wrapper-with-nuget"></a>分发具有 NuGet 的.NET 包装

下一步是打包并分发的 NuGet 包，以便它可以轻松地应用使用的和作为依赖项管理。 换行和消耗所有可以在单个解决方案中但的形式分发类库中分离，并启用 NuGet 工具通过我们来管理这些基本代码独立。

### <a name="preparing-a-local-packages-directory"></a>准备本地包目录

NuGet 源的最简单形式是本地目录：

1.  在中**Finder**，导航到一个方便访问的目录。 例如， **/用户**。
2.  选择**新文件夹**从**文件**菜单上，提供有意义的名称，如**本地 nuget 源**。

### <a name="creating-the-package"></a>创建包

1.  设置**生成配置**到**发行**，并执行生成中使用**COMMAND + B**。
2.  打开**终端**并将目录更改到此文件夹包含**nuspec**文件。
3.  在中**终端**，执行**nuget 包**命令指定**nuspec**文件中，**版本**(例如，1.0.0) 和**OutputDirectory**使用的文件夹中创建[上一步](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed)，即，**本地 nuget 源**。 例如：

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **确认**该**MathFuncs.1.0.0.nupkg**中创建了**本地 nuget 源**目录。

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[可选]使用 Azure DevOps 私有 NuGet 源

一种更可靠的技术是中所述[开始使用 Azure DevOps 中的 NuGet 包](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package)，其中说明了如何创建私有源和推送包 （在上一步中生成） 到该数据源。

它是具有完全自动化，例如使用此工作流的理想之选[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)。 有关详细信息，请参阅[开始使用 Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts)。

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>使用 Xamarin.Forms 应用从.NET 包装
若要完成本演练，创建**Xamarin.Forms**应用程序只需使用包发布到本地**NuGet**源。

### <a name="creating-the-xamarinforms-project"></a>创建**Xamarin.Forms**项目

1. 打开新的实例**Visual Studio for Mac**。 这可以从**终端**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. 在中**Visual Studio for Mac**，单击**新项目**(从*欢迎页*) 或**新解决方案**(从*文件*菜单上)。
3. 从**新的项目**窗口中，选择**空白窗体应用程序**(从*多平台 > 应用*)，然后单击**下一步**。
4. 更新的以下字段，然后单击**下一步**:

    - **应用名称：** MathFuncsApp.
    - **组织标识符：** 例如，使用反向命名空间_com。 {your_org}_。
    - **目标平台：** 使用默认值 （Android 和 iOS 目标）。
    - **共享的代码：** 将此设置为.NET Standard （"共享库"解决方案是可行的但超出了本演练的范围）。

5. 更新的以下字段，然后单击**创建**:

    - **项目名称：** MathFuncsApp.
    - **解决方案名称：** MathFuncsApp.  
    - **位置：** 使用默认的保存位置 （或选择一种替代方法）。

6. 在中**解决方案资源管理器**，**控件的同时单击**目标系统上 (**MathFuncsApp.Android**或**MathFuncs.iOS**) 对于初始测试，然后选择**设为启动项目**。
7. 选择首选**设备**或**模拟器**/**模拟器**。 
8. 运行该解决方案 (**命令 + RETURN**) 以验证的模板化**Xamarin.Forms**项目将生成并运行正常。 

    > [!NOTE]
    > **iOS** （特别是模拟器） 往往具有最快的生成/部署时间。

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>添加本地 NuGet 源到 NuGet 配置

1. 在中**Visual Studio**，选择**首选项**(从**Visual Studio**菜单)。
2. 选择**源**从下**NuGet**部分，然后单击**添加**。
3. 更新的以下字段，然后单击**添加源**:

    - **名称：** 提供有意义的名称，例如，本地包。  
    - **位置：** 指定**本地 nuget 源**文件夹中创建[上一步](#preparing-a-local-packages-directory)。

    > [!NOTE]
    > 在这种情况下没有无需指定**用户名**并**密码**。 

4. 单击 **“确定”**。

### <a name="referencing-the-package"></a>引用包

每个项目重复以下步骤 (**MathFuncsApp**， **MathFuncsApp.Android**，并**MathFuncsApp.iOS**)。

1. **控件的同时单击**项目，然后选择**添加 NuGet 包...** 从**添加**菜单。
2. 搜索**MathFuncs**。 
3. 验证是否**版本**包的是**1.0.0**和其他详细信息将显示按预期方式如**标题**并**说明**，即*MathFuncs*并*示例C++包装器库*。 
4. 选择**MathFuncs**包，然后单击**添加包**。

### <a name="using-the-library-functions"></a>使用库函数

现在，借助对的引用**MathFuncs**包中每个项目，这些函数可供C#代码。

1.  打开**MainPage.xaml.cs**内**MathFuncsApp**常见**Xamarin.Forms**项目 (由已引用**MathFuncsApp.Android**并**MathFuncsApp.iOS**)。
2.  添加**使用**语句**System.Diagnostics**并**MathFuncs**在文件顶部：

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. 声明的实例`MyMathFuncs`类的顶部`MainPage`类：

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. 重写`OnAppearing`并`OnDisappearing`方法从`ContentPage`基类：

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

5. 更新`OnAppearing`方法以初始化`myMathFuncs`以前声明的变量：

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. 更新`OnDisappearing`方法来调用`Dispose`方法`myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. 实现一个名为私有方法**TestMathFuncs** ，如下所示：

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

8. 最后，调用`TestMathFuncs`末尾的`OnAppearing`方法：

    ```csharp
    TestMathFuncs();
    ```

9. 每个目标平台上运行应用并验证中的输出**应用程序输出**板显示，如下所示：

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > 如果遇到 '*DLLNotFoundException*请务必检查设备/仿真器/模拟器正在使用的 CPU 体系结构适用于我们已选择的子集上 Android 或 iOS 上的生成错误测试支持。 

## <a name="summary"></a>总结

本文介绍了如何创建使用通过常见.NET 包装器通过 NuGet 包分发的本机库的 Xamarin.Forms 应用。 本演练中提供的示例是有意来更轻松地演示了的方法非常简单。 实际的应用程序必须处理的异常处理等的复杂性、 回调、 封送处理的更复杂的类型，以及与其他依赖库链接。 关键的考虑因素是依据过程的演变C++协调和同步使用的包装器和客户端应用程序代码。 根据一个或两个这些关注点是单一团队的职责，此过程可能会有所不同。 无论哪种方式，自动化是一个实际优点。 下面是一些资源，提供围绕的一些关键概念以及相关下载的其他参考资料。 

### <a name="downloads"></a>下载

- [NuGet 命令行 (CLI) 工具](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>示例

- [使用 Hyperlapse 跨平台移动开发C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C++和 Xamarin)](https://blog.xamarin.com/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Mono San Angeles 示例端口](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)

### <a name="further-reading"></a>其他阅读材料

[与此帖子的内容相关的文章](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)
