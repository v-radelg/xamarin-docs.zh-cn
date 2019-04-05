---
title: 做好应用程序发布准备
ms.prod: xamarin
ms.assetid: 9C8145B3-FCF1-4649-8C6A-49672DDA4159
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/21/2018
ms.openlocfilehash: 605db1c2e0dc0cf41288f9d6c9252582ce91d525
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58071056"
---
# <a name="preparing-an-application-for-release"></a>做好应用程序发布准备

应用程序经编码和测试后，必须准备一个包进行分发。 准备此包的第一个任务是生成供发布的应用程序，其中主要涉及到设置应用程序的一些属性。

使用以下步骤生成供发布的应用：

-   **[指定应用程序图标](#Specify_the_Application_Icon)** &ndash; 每个 Xamarin.Android 应用程序应指定一个应用程序图标。 虽然在技术层面并不需要这么做；但是，某些应用商店（例如 Google Play）对此提出了要求。

-   **[应用程序版本控制](#Versioning)** &ndash; 此步骤涉及初始化或更新版本信息。 这对应用程序将来的更新以及确保用户知道安装的应用程序版本非常重要。

-   **[压缩 APK](#shrink_apk)** &ndash; 通过托管代码上的 Xamarin.Android 链接器和 Java 字节码上的 ProGuard，可大幅压缩最终 APK。

-   **[保护应用程序](#protect_app)** &ndash; 通过禁用调试、模糊处理托管代码、添加防调试和防篡改，并使用本机编译来阻止用户或攻击者对应用程序进行调试、篡改或反向工程。

-   **[设置打包属性](#Set_Packaging_Properties)** &ndash; 打包属性控制 Android 应用程序包 (APK) 的创建。 此步骤会优化 APK，保护其资产并根据需要模块化打包。

-   **[编译](#Compile)**&ndash; 此步骤编译代码和资产，以确认按发布模式生成。

-   **[存档以供发布](#archive)** &ndash; 此步骤生成应用，并将其放置在存档中以供签名和发布。

下面详细说明了上述各步骤。

<a name="Specify_the_Application_Icon" />

## <a name="specify-the-application-icon"></a>指定应用程序图标

强烈建议每个 Xamarin.Android 应用程序都指定一个应用程序图标。 某些应用程序市场将不允许发布没有图标的 Android 应用程序。 `Application` 特性的 `Icon` 属性用于指定 Xamarin.Android 项目的应用程序图标。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 2017 及更高版本中，可通过项目“属性”的“Android 清单”部分指定应用程序图标，如以下屏幕截图所示：

[![设置应用程序图标](images/vs/01-application-icon-sml.png)](images/vs/01-application-icon.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，还可通过“项目选项”的“Android 应用程序”部分指定应用程序图标，如下面的屏幕截图所示：

[![设置应用程序图标](images/xs/01-application-icon-sml.png)](images/xs/01-application-icon.png#lightbox)

-----

在这些示例中，`@drawable/icon` 指位于 Resources/drawable/icon.png（请注意，.png 扩展名不包含在资源名称中）中的一个图标文件。 另外，此属性也可在文件 Properties\AssemblyInfo.cs 中声明，如以下示例代码片段所示：

```csharp
[assembly: Application(Icon = "@drawable/icon")]
```

通常情况下，`using Android.App` 在 **AssemblyInfo.cs**（`Application` 属性的命名空间为 `Android.App`）顶部声明；不过，可能需要添加此 `using` 语句（如果尚不存在）。

<a name="Versioning" />

## <a name="version-the-application"></a>应用程序版本控制

对于 Android 应用程序维护和分发而言，版本控制很重要。 如果没有版本控制，则很难确定应用程序是否应更新或如何更新。 为了辅助版本控制，Android 可识别两种不同类型的信息： 

-   **版本号** &ndash; 表示应用程版本的整数值，供 Android 和应用程序内部使用。 对大多数应用程序而言，此值的初始设置为 1，之后随每个内部版本递增。 此值与版本名称属性（见下文）没有关系或关联。 应用程序和发布服务不应向用户显示此值。 此值在 **AndroidManifest.xml** 文件中存储为 `android:versionCode`。 

-   **版本名称** &ndash; 仅用于向用户传递应用程序（如安装在特定设备上）的版本相关信息的字符串。 版本名称将向用户显示，或在 Google Play 中显示。 此字符串不供 Android 内部使用。 版本名称可以是任何字符串值，它能帮助用户了解其设备上安装的版本。 此值在 **AndroidManifest.xml** 文件中存储为 `android:versionName`。 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，可在项目“属性”的“Android 清单”部分设置这些值，如以下屏幕截图所示：

[![设置版本号](images/vs/02-versioning-sml.png)](images/vs/02-versioning.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

可通过“项目选项”的“生成”>“Android 应用程序”部分设置这些值，如以下屏幕截图所示：

[![设置版本号](images/xs/02-versioning-sml.png)](images/xs/02-versioning.png#lightbox)

-----

<a name="shrink_apk" />

## <a name="shrink-the-apk"></a>缩小 APK

可通过结合使用 Xamarin.Android 链接器（删除不必要的托管代码）和 Android SDK 中的 ProGuard 工具（删除未使用的 Java 字节码）缩小 Xamarin.Android APK。 生成过程首先使用 Xamarin.Android 链接器以托管代码 (C#) 级别优化应用，然后使用 ProGuard（如已启用）以 Java 字节码级别优化 APK。


### <a name="configure-the-linker"></a>配置链接器

发布模式会关闭共享运行时并打开链接，使应用程序只提供运行时需要的 Xamarin.Android 部分。 Xamarin.Android 中的*链接器*使用静态分析来确定 Xamarin.Android 应用程序所使用或引用的程序集、类型和类型成员。 然后，链接器将放弃所有未使用（或引用）的程序集、类型和成员。 这可显著减小包的大小。 例如，[HelloWorld](~/android/deploy-test/linker.md) 示例，其 APK 的最终大小减少了 83%： 

-   配置：无 &ndash; Xamarin.Android 4.2.5 大小 = 17.4 MB。

-   配置：仅 SDK 程序集 &ndash; Xamarin.Android 4.2.5 大小 = 3.0 MB。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

通过项目“属性”的“Android 选项”部分设置链接器选项：

[![链接器选项](images/vs/03-linking-sml.png)](images/vs/03-linking.png#lightbox)

“链接”下拉菜单提供以下选项，用于控制链接器：

-   **无** &ndash; 这将关闭链接器；不会执行任何链接。

-   **仅 SDK 程序集** &ndash; 这会仅链接 [Xamarin.Android](~/cross-platform/internals/available-assemblies.md) 所需的程序集。 
    不会链接其他程序集。

-   **SDK 和用户程序集** &ndash; 这会链接应用程序所需的所有程序集，而不是仅链接 Xamarin.Android 所需的程序集。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

通过“项目选项”的“Android 生成”部分中的“链接器”选项卡设置链接器选项，如以下屏幕截图所示：

[![链接器选项](images/xs/03-linking-sml.png)](images/xs/03-linking.png#lightbox)

控制链接器的选项如下所示：

-   **不链接** &ndash; 这将关闭链接器；不会执行任何链接。

-   **仅链接 SDK 程序集** &ndash; 这会仅链接 [Xamarin.Android](~/cross-platform/internals/available-assemblies.md) 所需的程序集。 不会链接其他程序集。

-   **链接所有程序集** &ndash; 这会链接应用程序所需的所有程序集，而不是仅链接 Xamarin.Android 所需的程序集。

-----

链接可能产生一些意外的副作用，因此必须在物理设备上的发布模式下重新测试应用程序。

### <a name="proguard"></a>ProGuard

ProGuard 是一种链接和模糊处理 Java 代码的 Android SDK 工具。 ProGuard 通常用于创建小型应用程序，工作原理是减少 APK 中包含的大型库的内存占用。 ProGuard 将删除未使用的 Java 字节码，使生成的应用变得更小。 例如，在小型 Xamarin.Android 应用中使用 ProGuard 通常可减少约 24% 大小&ndash; 在具有多个库依赖关系的大型应用中使用 ProGuard 通常可实现更大幅度的大小缩减。 

ProGuard 不是 Xamarin.Android 链接器的替代工具。 Xamarin.Android 链接器链接托管代码，而 ProGuard 链接 Java 字节码。 生成过程首先在应用中使用 Xamarin.Android 链接器优化托管的 (C#) 代码，然后在 Java 字节码级别使用 ProGuard（若已启用）优化 APK。 

选择“启用 ProGuard”**P**时，Xamarin.Android 将在生成的 APK 中运行 ProGuard 工具。 ProGuard 配置文件由 ProGuard 在生成时生成和使用。 Xamarin.Android 还支持自定义 ProguardConfiguration 生成操作。 可以将自定义 ProGuard 配置文件添加到项目中，右键单击并选中该文件作为生成操作，如此示例中所示： 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![ProGuard 生成操作](images/vs/05-proguard-build-action-sml.png)](images/vs/05-proguard-build-action.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![ProGuard 生成操作](images/xs/05-proguard-build-action-sml.png)](images/xs/05-proguard-build-action.png#lightbox)

-----

默认情况下，禁用 ProGuard。 仅在项目设置为“发布”模式时才能使用“启用 ProGuard”选项。 除非选中“启用 ProGuard”，否则会忽略所有 ProGuard 生成操作。 Xamarin.Android ProGuard 配置不会模糊处理 APK，且不能启用模糊处理，即使处理自定义配置文件也不例外。 如果想要模糊处理，请参阅[使用 Dotfuscator 保护应用程序](~/android/deploy-test/release-prep/index.md#dotfuscator)。 

有关 ProGuard 工具用法的详细信息，请参阅 [ProGuard](~/android/deploy-test/release-prep/proguard.md)。

<a name="protect_app" />

## <a name="protect-the-application"></a>保护应用程序

<a name="Disable_Debugging" />

### <a name="disable-debugging"></a>禁用调试

在 Android 应用程序开发期间，将使用 Java 调试线路协议 (JDWP) 执行调试。 这是一种技术，它允许 **adb** 等工具出于调试目的与 JVM 通信。 默认对 Xamarin.Android 应用程序的调试版本启用 JDWP。 虽然 JDWP 在开发过程中很重要，但它会对已发布的应用程序造成安全问题。 

> [!IMPORTANT]
> 请始终禁用已发布应用程序中的调试状态，因为如果不禁用此状态，则可能（通过 JDWP）获得 Java 进程的完全访问权限并在应用程序的上下文中执行任意代码。

Android 清单包含 `android:debuggable` 属性，该属性控制是否可以调试应用程序。 将 `android:debuggable` 属性设置为 `false` 被视为一种很好的做法。 执行此操作最简单的方法是在 **AssemblyInfo.cs** 中添加条件编译语句： 

```csharp
#if DEBUG
[assembly: Application(Debuggable=true)]
#else
[assembly: Application(Debuggable=false)]
#endif
```

注意，调试版本会自动设置某些权限以简化调试（如 **Internet** 和 **ReadExternalStorage**）。 但是，发布版本只使用显式配置的权限。 若发现切换到发布版本会导致应用失去可在调试版本中使用的权限，请验证是否已在“所需权限”列表中显式启用了此权限，如[权限](~/android/app-fundamentals/permissions.md)中所述。 

<a name="dotfuscator" id="dotfuscator" />

### <a name="application-protection-with-dotfuscator"></a>使用 Dotfuscator 保护应用程序

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

即使[已禁用调试](#Disable_Debugging)，攻击者仍可能重新打包应用程序，从而添加或删除配置选项或权限。 这可使他们对应用程序进行反向工程、调试或篡改。
[Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) 可用于混淆托管代码，并在生成时向 Xamarin.Android 应用插入运行时安全状态检测代码，对应用是否在根设备上运行进行检测和响应。

Visual Studio 2017 附带了 Dotfuscator CE。
若要使用 Dotfuscator，请单击“工具”>“PreEmptive Protection - Dotfuscator”。

若要配置 Dotfuscator CE，请参阅 [Using Dotfuscator Community Edition with Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator)（结合使用 Dotfuscator Community Edition 和 Xamarin）。
完成配置后，Dotfuscator CE 将自动保护创建的每个生成。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

即使[已禁用调试](#Disable_Debugging)，攻击者仍可能重新打包应用程序，从而添加或删除配置选项或权限。 这可使他们对应用程序进行反向工程、调试或篡改。
虽然它不支持 Visual Studio for Mac，但可结合使用 [Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) 和 Visual Studio 来混淆托管代码，并在生成时向 Xamarin.Android 应用插入运行时安全状态检测代码，对应用是否在根设备上运行进行检测和响应。

若要配置 Dotfuscator CE，请参阅 [Using Dotfuscator Community Edition with Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator)（结合使用 Dotfuscator Community Edition 和 Xamarin）。
完成配置后，Dotfuscator CE 将自动保护创建的每个生成。

-----

<a name="bundle" />

### <a name="bundle-assemblies-into-native-code"></a>将程序集捆绑到本机代码

此选项启用时，程序集会捆绑到本机共享库中。 此选项使代码保持安全；它通过在本机二进制文件中嵌入这些托管程序集来保护它们。

此选项需要 Enterprise 许可证，仅当“使用快速部署”禁用时才可用。 “将程序集捆绑到本机代码”在默认情况下处于禁用状态。

请注意，“捆绑到本机代码”选项执行不意味着程序集会编译到本机代码中。 无法使用 [**AOT 编译**](#aot)将程序集编译到本机代码中（当前只是试验性功能，不用于生产用途）。

<a name="aot" />

### <a name="aot-compilation"></a>AOT 编译

[打包属性](#Set_Packaging_Properties)页上的**AOT 编译**选项支持预先编译程序集。 启用此选项后，通过在运行时之前预编译程序集可将实时 (JIT) 启动开销降到最低。 生成的本机代码包括在 APK 以及未编译程序集中。 这可缩短应用程序启动时间，但代价是 APK 大小会变得稍大。

“AOT 编译”选项要求使用 Enterprise 或更高版本的许可证。 仅在项目配置为发布模式时，才可使用“AOT 编译”，并且该选项默认处于禁用状态。 有关 AOT 编译的详细信息，请参阅 [AOT](https://www.mono-project.com/docs/advanced/aot/)。

#### <a name="llvm-optimizing-compiler"></a>LLVM 优化编译器

LLVM 优化编译器会创建更小更快速的编译代码，并将 AOT 编译的程序集转换为本机代码，但生成时间会变缓慢。 默认情况下，LLVM 编译器处于禁用状态。 要使用 LLVM 编译器，必须首先启用“AOT 编译”选项（在[打包属性](#Set_Packaging_Properties)页面上）。


> [!NOTE]
> “LLVM 优化编译器”选项需要企业许可证。  

<a name="Set_Packaging_Properties" />

## <a name="set-packaging-properties"></a>设置打包属性

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

可在项目“属性”的“Android 选项”部分设置打包属性，如以下屏幕截图所示：

[![打包属性](images/vs/04-packaging-sml.png)](images/vs/04-packaging.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

可在“项目选项”中设置打包属性，如下面的屏幕截图所示：

[![打包属性](images/xs/04-packaging-sml.png)](images/xs/04-packaging.png#lightbox)

-----

其中许多属性（例如“使用共享运行时”和“使用快速部署”）专用于调试模式。 但是，在发布模式下配置应用程序时，还需要进行其他设置，这些设置用于确定如何[针对大小和执行速度优化应用](#shrink_apk)、如何[防止篡改应用](#protect_app)，以及如何打包应用以支持不同的体系结构和大小限制。

### <a name="specify-supported-architectures"></a>指定支持的体系结构

准备 Xamarin.Android 应用进行发布时，必须指定支持的 CPU 体系结构。 单个 APK 可包含计算机代码，以支持多个不同的体系结构。 请参阅 [CPU 体系结构](~/android/app-fundamentals/cpu-architectures.md)，深入了解如何支持多个 CPU 体系结构。

### <a name="generate-one-package-apk-per-selected-abi"></a>每个选定 ABI 生成一个包 (.APK)

启用此选项后，会为每个支持的 ABI（在“高级”选项卡上进行选择，如 [CPU 体系结构](~/android/app-fundamentals/cpu-architectures.md)中所述）分别创建一个 APK，而不是为所有支持的 ABI 创建单个大型 APK。 仅在项目配置为用于发布模式时，才可使用此选项，并且其默认处于禁用状态。

### <a name="multi-dex"></a>Multi-Dex

如果启用“启用 Multi-Dex”选项，Android SDK 工具将用于绕过 **.dex** 文件格式的 65K 方法限制。 65K 方法限制基于应用_引用_的 Java 方法数（包括应用依赖的任何库中的方法数）&ndash; 不基于_源代码中写入_的方法数。 如果应用程序只定义了几个方法，却使用了多个方法或大型库，则可能超出 65K 限制。

应用可能未使用每个引用库中的每个方法；因此，ProGuard（见上文）等工具可能会将未使用的方法从代码中删除。 最佳做法是仅在绝对必要时启用“启用 Multi-Dex”，也就是说，即使使用 ProGuard，应用引用的 Java 方法仍然超过 65K。

若要深入了解 Multi-Dex，请参阅[配置超出 64K 方法的应用](https://developer.android.com/tools/building/multidex.html)。

<a name="Compile" />

## <a name="compile"></a>Compile

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

完成上述所有步骤后，应用即可用于编译。 选择“生成”>“重新生成解决方案”以验证其是否在发布模式下成功生成。 请注意，此步骤尚不会产生 APK。

[对应用包进行签名](~/android/deploy-test/signing/index.md)中更详细地讨论了打包和签名。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

完成上述所有步骤后，即可编译应用程序（选择“生成”>“全部生成”）以验证该应用程序已按发布模式成功生成。 请注意，此步骤尚不会产生 APK。

-----

<a name="archive" />

## <a name="archive-for-publishing"></a>存档以供发布

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要开始发布过程，请在**解决方案资源管理器**中右键单击项目，然后选择“存档...”上下文菜单项：

[![存档应用](images/vs/07-archive-for-publishing-sml.png)](images/vs/07-archive-for-publishing.png#lightbox)

选择“存档...”选项将启动**存档管理器**并开始应用程序包的存档过程，如以下屏幕截图所示：

[![存档管理器](images/vs/08-archive-manager-sml.png)](images/vs/08-archive-manager.png#lightbox)

另一种创建存档的方法是：在**解决方案资源管理器**中，右键单击“解决方案”，然后选择“全部存档...”，这会生成解决方案并存档可生成存档的所有 Xamarin 项目：

[![全部存档](images/vs/09-archive-all-sml.png)](images/vs/09-archive-all.png#lightbox)

“存档”和“全部存档”均会自动启动**存档管理器**。 若要直接启动**存档管理器**，请单击“工具”>“存档管理器...”菜单项：

[![启动存档管理器](images/vs/10-launch-archive-manager-sml.png)](images/vs/10-launch-archive-manager.png#lightbox)

右键单击“解决方案”节点并选择“查看存档”可随时查看该解决方案的存档：

[![查看存档](images/vs/11-view-archives-sml.png)](images/vs/11-view-archives.png#lightbox)

### <a name="the-archive-manager"></a>存档管理器

**存档管理器**由“解决方案列表”窗格、“存档列表”和“详细信息面板”组成：

[![“存档管理器”窗格](images/vs/12-archive-manager-detail-sml.png)](images/vs/12-archive-manager-detail.png#lightbox)

“解决方案列表”将显示所有解决方案，其中至少有一个项目已存档。 “解决方案列表”包括以下各部分：

* **当前解决方案** &ndash; 显示当前的解决方案。 请注意，如果当前解决方案不含现有存档，此区域可能为空。
* **全部存档** &ndash; 显示包含存档的所有解决方案。
* **搜索**文本框（顶部）&ndash; 根据文本框中输入的搜索字符串筛选“全部存档”列表中列出的解决方案。

“存档列表”显示有关所选解决方案的所有存档的列表。 “存档列表”包括以下各部分：

* **所选解决方案名称** &ndash; 显示“解决方案列表”中所选解决方案的名称。 “存档列表”中显示的所有信息均与此选定的解决方案有关。
* **平台筛选器** &ndash; 此字段可按平台类型（如 iOS 或 Android）筛选存档。
* **存档项目** &ndash; 选定解决方案的存档列表。 此列表中的每个项均包括项目名称、创建日期和平台。 还可以显示其他信息，例如存档或发布项目时的进度。

“详细信息面板”显示有关每个存档的其他信息。 用户还可以从此面板启动分发工作流或打开创建分发的文件夹。 “生成注释”部分可将生成注释包括在存档中。

### <a name="distribution"></a>分布

准备好发布存档版应用程序后，请在“存档管理器”中选择该存档，然后单击“分发...”按钮：

[![“分发”按钮](images/vs/13-distribute-sml.png)](images/vs/13-distribute.png#lightbox)

“分发通道”对话框包括以下方面的信息：应用、分发工作流进度指示以及分发渠道选项。 首次运行时，提供两个选项：

[![选择分发通道](images/vs/14-distribution-channel-sml.png)](images/vs/14-distribution-channel.png#lightbox)

可选择以下分发通道之一：

* **Ad-Hoc** &ndash; 将已签名的 APK 保存到磁盘，以将其旁加载到 Android 设备。 继续查看[对应用包进行签名](~/android/deploy-test/signing/index.md)，了解如何创建 Android 签名标识、为 Android 应用程序创建新的签名证书以及将“临时”版本的应用发布到磁盘。 这是为测试创建 APK 的好方法。

* **Google Play** &ndash; 将已签名的 APK 发布到 Google Play。 继续查看[发布到 Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)，了解如何对 APK 进行签名并将其发布到 Google Play 商店。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要开始发布过程，请选择“生成”>“存档以供发布”：

[![存档以供发布](images/xs/07-archive-for-publishing-sml.png)](images/xs/07-archive-for-publishing.png#lightbox)

“存档以供发布”选项可生成项目，并将其捆绑到存档文件中。 “全部存档”菜单选项可存档解决方案中的所有可存档项目。 生成和捆绑操作完成后，这两个选项会自动打开“存档管理器”：

[![“存档”视图](images/xs/08-archives-view-sml.png)](images/xs/08-archives-view.png#lightbox)

在此示例中，**存档管理器**仅列出一个已存档应用程序，即 **MyApp**。 请注意，注释字段允许一条简短注释随存档文件一起保存。 若要发布存档版的 Xamarin.Android 应用程序，请在“存档管理器”中选择该应用，然后单击“签名和分发”（如上所示）。 出现的“签名和分发”对话框提供两个选择：

[![签名和分发](images/xs/09-sign-and-distribute-sml.png)](images/xs/09-sign-and-distribute.png#lightbox)

可从此处选择分发渠道：

-   Ad-Hoc &ndash; 将已签名的 APK 保存到磁盘，以将其旁加载到 Android 设备。 继续查看[对应用包进行签名](~/android/deploy-test/signing/index.md)，了解如何创建 Android 签名标识、为 Android 应用程序创建新的签名证书以及将“临时”版本的应用发布到磁盘&ldquo;&rdquo;。 这是为测试创建 APK 的好方法。


-   **Google Play** &ndash; 将已签名的 APK 发布到 Google Play。
    继续查看[发布到 Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)，了解如何对 APK 进行签名并将其发布到 Google Play 商店。

-----

## <a name="related-links"></a>相关链接

- [多核设备和 Xamarin.Android](~/android/deploy-test/multicore-devices.md)
- [CPU 体系结构](~/android/app-fundamentals/cpu-architectures.md)
- [AOT](https://www.mono-project.com/docs/advanced/aot/)
- [收缩代码和资源](https://developer.android.com/tools/help/proguard.html)
- [配置方法数超过 64K 的应用](https://developer.android.com/tools/building/multidex.html)
