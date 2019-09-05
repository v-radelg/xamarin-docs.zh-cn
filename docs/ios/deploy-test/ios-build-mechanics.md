---
title: iOS 生成机制
description: 本指南介绍如何对应用进行计时，以及如何使用可用于所有生成配置的更快生成的方法。
ms.prod: xamarin
ms.assetid: 06FD3940-D666-4C9E-BC3E-BBE481EF8012
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: ec510d46bf1b46cf8fd70c8f4d43b3108f46a010
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284777"
---
# <a name="ios-build-mechanics"></a>iOS 生成机制

本指南介绍如何安排应用的时间，以及如何使用可用于所有生成配置的更快生成的方法。 

开发出色的应用程序不只是编写可用的代码。 一个良好编写的应用程序应包含一些优化，用于快速生成更小且运行速度更快的应用。 这些优化不仅可以带来更好的用户体验，还会为处理项目的用户或任何开发人员提供绝佳体验。 请务必确保在处理应用程序的各项操作时，一切都是计时的。 

请记住，默认选项安全且快速，但对于每种情况而言并非都是最佳选项。 此外，许多选项会减缓或加快开发周期，具体要取决于各个项目。 例如，本机去除需要时间，但如果获得的大小很小，则耗时的去除将无法通过更快部署找回。 另一方面，本机去除可以大幅缩小应用，在这种情况下应用将可以更快地部署。 具体要取决于项目，想要了解的唯一方法就是测试。

此外，Xamarin 生成速度还受计算机的不同容量和功能的影响，进而影响性能：处理器功能、总线速度、物理内存量、磁盘速度、网络速度。 这些性能限制不在本文的讨论范围内，并且由开发人员负责管理。


## <a name="timing-apps"></a>计时应用

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中启用诊断 MSBuild 输出：

1. 单击“Visual Studio for Mac”>“首选项…” 
2. 在左侧树视图中，选择“项目”>“生成” 
3. 在右侧面板中，将“日志详细级别”下拉列表设置为“诊断”  ：[![](ios-build-mechanics-images/image2.png "设置日志详细级别")](ios-build-mechanics-images/image2.png#lightbox)
4. 单击“确定” 
5. 重启 Visual Studio for Mac
6. 清除并重新生成包
7. 通过单击“生成输出”按钮，查看“Errors Pad”中的诊断输出（“视图”>“板”>“错误”）


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中启用诊断 MSBuild 输出：

1. 单击“工具”>“选项…” 
2. 在左侧树视图中，选择“项目和解决方案”>“生成并运行” 
3. 在右侧面板中，将“MSBuild 生成输出详细级别”下拉列表设置为“诊断”   ：[![](ios-build-mechanics-images/image2-vs.png "设置 MSBuild 生成输出详细级别")](ios-build-mechanics-images/image2-vs.png#lightbox)
4. 单击“确定” 
5. 清除并重新生成包。
6. 诊断输出将显示在“输出”面板中。

-----

## <a name="timing-mtouch"></a>计时 mtouch

若要显示特定于 mtouch 生成过程的信息，请将 `--time --time` 传递给“项目选项”  中的 mtouch 参数。 可以通过搜索 `MTouch` 任务在“生成输出”中找到结果：

```csharp
Setup: 36 ms
Resolve References: 982 ms
Extracted native link info: 987 ms
...
Total time: 1554 ms
```

## <a name="connecting-from-visual-studio-with-build-host"></a>从 Visual Studio 与生成主机连接

从技术上讲，Xamarin 工具适用于运行 OS X 10.10 Yosemite 或更高版本的任何 Mac。 但是，Mac 性能可能会影响开发人员的体验和生成时间。

在断开连接的状态下，Windows 上的 Visual Studio 仅执行 C# 编译阶段，而不会尝试执行链接或 AOT 编译、将应用打包到“.app”捆绑包或登录应用程序包 __  。 （C# 编译阶段是罕见的性能瓶颈。）通过直接在 Visual Studio for Mac 的 Mac 生成主机上生成，尝试找出生成速度减缓的管道位置。


此外，sluggishness 更为常见的位置之一是 Windows 计算机和 Mac 生成主机之间的网络连接。 这可能是由于网络的物理障碍、使用无线连接，或需要遍历饱和计算机（如 Mac-in-the-cloud 服务）。

## <a name="simulator-tricks"></a>模拟器技巧

 
在开发移动应用程序时，快速部署代码至关重要。 由于速度和缺乏设备预配要求等各种不同的原因，开发人员通常会选择部署到预安装的模拟器或仿真器上。 有关开发人员工具的制造商，提供模拟器或仿真器的决策归结于速度和兼容性之间的权衡。 

Apple 提供用于 iOS 开发的模拟器，通过创建限制较少的代码运行环境，基于兼容性提升速度。 这一限制较少的环境允许 Xamarin 使用模拟器的实时 (JIT) 编译器（而不是设备上的 [AOT](~/ios/internals/architecture.md)），这意味着，生成会在运行时编译为本机代码。 由于 Mac 比设备要快得多，所以它可以提供更好的性能。

模拟器使用共享的应用程序启动器，允许重用启动器，而不是像在设备上那样要求每次生成启动器。

在考虑到上述信息时，下面的列表提供了在模拟器上生成和部署应用以提供最佳性能要采取的一些步骤信息。
 
### <a name="tips"></a>提示

- 对于生成： 
  - 取消选择“项目选项”中的“优化 PNG 图像”  选项。 就模拟器上的生成而言，此优化是不必要的。
  - 将链接器设置为“不链接”  。 禁用链接器速度更快，因为执行链接会占用大量的时间。
  - 通过使用 `--nofastsim` 标志来禁用共享的应用程序启动器会导致模拟器生成速度要慢得多。 不再需要时，请删除此标志。
  - 使用本机库会减缓速度，因为在这种情况下无法重用共享的 simlauncher 主可执行文件，且必须为每次生成编译特定于应用程序的可执行文件。
- 对于部署
  - 在可能的情况下，使模拟器始终保持运行状态。 可能需要 12 秒来冷启动模拟器。
- 其他提示
  - 选择生成而不是重新生成，因为重新生成会在生成之前清除。 清除可能需要很长时间，因为它将删除可能会使用的引用。
  - 利用模拟器不会强制执行沙盒的事实。 每当在模拟器中启动应用时，将大型资源（如视频或其他资产）包含在项目中会产生成本高昂的文件复制操作。 通过将这些文件置于主目录可避免这些成本高昂的操作，并根据完整的文件路径在应用程序中引用它们。  
  - 如有疑问，请使用 `--time --time` 标志衡量所做的更改

下面的屏幕截图演示了如何在 iOS 选项中设置模拟器的这些选项：

[![](ios-build-mechanics-images/image3.png "设置选项")](ios-build-mechanics-images/image3.png#lightbox)

## <a name="device-tricks"></a>设备技巧

部署到设备与部署到模拟器类似，因为模拟器是用于 iOS 设备的一个小子集生成。 设备构建需要执行更多步骤，但有一个优点，即它提供了更多的机会来优化你的应用。

### <a name="build-configurations"></a>生成配置

部署 iOS 应用时提供了多个生成配置。 请务必充分理解每个配置，以知道何时进行优化以及应进行优化的原因。

- 调试
  - 这是在开发应用时应使用的主要配置，因此应尽可能的快。
- Release
  - 发布版本是指那些为用户提供的版本，对性能的关注极其重要。 在使用发布配置时，你可能需要使用 LLVM 优化编译器并优化 PNG 文件。

 
理解生成和部署之间的关系同样至关重要。 部署时间是一个应用程序大小的函数。 应用程序越大部署时间越长。 通过将应用程序大小降至最低，可以缩短部署时间。

此外，最小化应用程序大小还可以缩短生成时间。 这是因为从应用程序删除代码所需的时间比本机编译不使用的代码所需时间要少。 较小的对象文件就意味着更快的链接，这将创建一个更小的可执行文件，并生成较少的符号。 因此，节省空间具有双重收益，这就是为什么“链接 SDK”  是所有设备版本的默认设置。 

> [!NOTE]
> “链接 SDK”  选项可能显示仅适用于链接框架 SDK 或者仅适用于链接 SDK 程序集，具体要取决于正在使用的 IDE。
 

### <a name="tips"></a>提示

- 生成: 
  - 生成单个体系结构（例如 ARM64）的速度要快于 FAT 二进制文件（例如 ARMv7 + ARM64）
  - 避免在调试时优化 PNG 文件
  - 请考虑链接所有程序集。 优化每个程序集 
  - 通过使用  `--dsym=false` 禁用调试符号的创建。 但是，应注意一点，禁用该设置将意味着崩溃报告只能在生成应用的计算机上进行符号化，且前提是该应用未去除。

 
应避免的某些事项：

- Fat 二进制文件（调试） 
- 禁用链接器 `--nolink` 
- 禁用去除 
  - 符号 `--nosymbolstrip` 
  - IL（发行版）`--nostrip`。  
 
其他提示 

- 在模拟器中，首选生成而不是重新生成 
  - 将缓存 AOT 的程序集（对象文件） 
- 由于符号、运行 dsymutil，并且因为它最终会变得更大、需要额外时间来上传至设备，调试生成将需要更长的时间。 
- 默认情况下，发布版本会在程序集上执行 IL 去除。 它只需一点时间，但在将一个更小的应用程序部署到设备时所需的时间可能会远小于去除时间。
- 避免在每个生成（调试）上部署大型静态文件 
  - 使用 UIFileSharingEnabled (info.plist) 
    - 可以一次上传资产 
- 如有疑问，请使用 `--time --time` 标志衡量所做的更改

下面的屏幕截图演示了如何在 iOS 选项中设置模拟器的这些选项：

[![](ios-build-mechanics-images/image4.png "设置选项")](ios-build-mechanics-images/image4.png#lightbox)

## <a name="using-the-linker"></a>使用链接器

在生成应用程序时，mtouch 将链接器用于托管代码，这将删除应用程序不使用的代码。 从理论上讲，这可提供更小且因此更快的生成。 有关链接器的详细信息，请参阅[在 iOS 上链接](~/ios/deploy-test/linker.md)指南。

使用链接器时，请考虑以下选项：

- 对于设备生成选择“不链接”  将会花费更长的时间，而且还会生成更大的应用。 
  - 如果应用大小超过限制，Apple 将会拒绝。 根据 `MinimumOSVersion`，这可以小至 60 MB。 
  - 包含本机可执行文件。 
  - 对模拟器生成使用“不链接”会更快，因为使用 JIT 编译（而不是设备上的 AOT）。
- 链接 SDK 是默认选项。
- 使用“链接所有”可能不安全，特别是当你使用不是自己的代码时，如 NuGets 或部件。 如果选择“不链接程序集”，这些服务中的所有代码都将包含在应用程序中，可能会创建较大的应用。 
  - 但是，如果选择“链接所有”  ，应用程序可能会崩溃，尤其是在使用外部组件的情况下。 这是因为某些组件使用特定类型的反射。
  - 静态分析和反射不能协同工作。 

可以通过使用 [`[Preserve]` 属性](~/ios/deploy-test/linker.md)来指示工具将内容保存在应用程序中。 

如果你对源代码没有访问权限，或者源代码由工具生成并且你不希望更改它，那么仍然可以通过创建一个 XML 文件（该文件描述需要保留的所有类型和成员）来链接它。 然后可以将 `--xml={file.name}.xml` 标志添加到项目选项，它会完全像你在使用属性一样处理代码。


### <a name="partially-linking-applications"></a>部分链接应用程序 

此外，还可以部分链接应用程序，以帮助优化应用程序的生成时间：

- 使用 `Link All` 并跳过某些程序集 
  - 某些应用程序大小优化将丢失。
  - 无需访问源代码。
  - 例如 `--linkall --linkskip=fieldserviceiOS`。
 
- 使用 `Link SDK` 选项并在需要的程序集上使用 `[LinkerSafe]` 属性 
  - 需要访问源代码。
  - 通知系统程序集可以安全链接，并像处理 Xamarin SDK 一样处理程序集。
 
### <a name="objective-c-bindings"></a>Objective-C 绑定 

- 使用绑定中的 `[Assembly: LinkerSafe]` 属性可以节省时间和缩减大小。

- SmartLink 
  - 在本机端完成 
  - 使用 `[LinkWith (SmartLink=true)]` 属性
  - 这有助于本机链接器从你链接的库中消除本机代码。 
  - 请注意，动态查找符号不会使用此属性。 

## <a name="summary"></a>总结

本指南介绍如何对 iOS 应用程序进行计时，以及要考虑的依赖于项目生成配置和选项的选项。 

<!-----
# Benchmarks

## Layer 1: building again after making modifications, but _without_ cleaning should be faster 
 
The app should build a bit more quickly if you have only made changes to a subset of the libraries and you do not clean the build before re-deploying. 
 
 
 
### Clean build time 
178 seconds 
 
 
### Build again (without cleaning) after making _no changes_ 
12.5 seconds 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
3 trials: 45 seconds, 43 seconds, 43 seconds 
 
 
### Build again (without cleaning) after changing 1 line in each of the following files 
 
- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
 
3 trials: 45 seconds, 45 seconds, 45 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in each of the following files 
 
- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
- Sales.Native.Core.IOS.Ext/ServiceInterfaces/AlertDialog/Dialog.cs 
- Sales.Native.Core.Tools.IOS.Ext/BaseViews/BaseNavigationViewController.cs 
- View.Common/Services/DataTransferResult.cs 
 
45 seconds 
 
 
 
 
 
 
## Layer 2: "app thinning" aka "device specific builds" 
 
The idea of "app thinning" is that the IDE will only build the 1 architecture needed for the specific device that you're deploying to (rather than _both_ 32-bit and 64-bit architectures). 
 
As of the latest "Xamarin 4" builds, you can now enable "app thinning" in Visual Studio via the "Project Options -> iOS Build -> Enable device-specific builds" setting. 
 
Or if you prefer you can achieve a similar result by changing the "Project Options -> iOS Build -> Advanced [tab] -> Supported architectures" to select just _one_ architecture (for example ARM64 if you are developing on a 64-bit device). 
 
 
 
(Caveat: I ran the following builds in Visual Studio for Mac on the Mac rather than on the command line.) 
 
### Clean build time without "device specific builds" 
177 seconds 
 
 
 
### Clean build time _with_ "device specific builds"  
2 trials: 106 seconds, 98 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 31 seconds, 31 seconds 
 
 
* * * 
 
 
## Using the same strategy, but explicitly setting "Supported architectures" to select ARM64 _only_ (rather than using "device specific builds") 
 
(These builds were again run on the command line using `xbuild`.) 
 
 
 
### Clean build time with "Supported architectures" set to ARM64 _only_ 
2 trials: 80 seconds, 91 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 26 seconds, 26 seconds 
 
 
 
 
 
[1] Mac system used for testing: MacBookAir5,2 
 
- 2.0 GHz Core i7 (I7-3667U) 
 
2 Cores with hyper-threading 
 
L2 Cache (per Core): 256 KB 
L3 Cache: 4 MB 
 
- Standard MacBook soldered-in solid-state storage 
 
- 8 GB RAM 
---->


## <a name="related-links"></a>相关链接

- [博客文章](https://blog.xamarin.com/xamarin-ios-build-improvements/)
- [在 iOS 上链接](~/ios/deploy-test/linker.md)
- [自定义链接器配置](~/cross-platform/deploy-test/linker.md)
