---
title: 链接 Xamarin.iOS 应用
description: 本文档介绍 Xamarin.iOS 链接器，该链接器用于消除 Xamarin.iOS 应用程序中未使用的代码，以缩减其大小。
ms.prod: xamarin
ms.assetid: 3A4B2178-F264-0E93-16D1-8C63C940B2F9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 23022351b6ac2da59550be668cb9ed4fe84904dd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284768"
---
# <a name="linking-xamarinios-apps"></a>链接 Xamarin.iOS 应用

生成应用程序时，Visual Studio for Mac 或 Visual Studio 会调用一个名为 mtouch  的工具，其中包括托管代码的链接器。 它用于从类库中删除应用程序未使用的功能。 目标是缩减应用程序的大小，以使其只附带必须的位。

链接器使用静态分析来确定应用程序易于遵循的不同代码路径。 不过其处理有些繁重，因为它必须浏览每个程序集的所有细节，才可确保不会删除任意可发现的细节。 默认情况下，不会在模拟器生成中启用，以在调试过程中加快生成的时间。 但是，由于其生成的应用程序较小，所以它可以加速 AOT 编译并上传到设备，默认情况下，所有“设备(发布)生成”  都使用链接器。

由于链接器是静态工具，它无法标记通过反射调用或已动态实例化的包含类型和方法。 有几个选项可用于解决这一限制。

<a name="Linker_Behavior" />

## <a name="linker-behavior"></a>链接器行为

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

可以通过“项目选项”  中的链接器行为下拉菜单自定义链接过程。 若要访问此下拉菜单，请双击 iOS 项目，并浏览至“iOS 生成”>“链接器选项”  ，如下所示：

[![](linker-images/image1.png "链接器选项")](linker-images/image1.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

可以通过 Visual Studio 中“项目属性”  中的链接器行为下拉菜单自定义链接过程。

请执行以下操作：

1. 在“解决方案资源管理器”  中，右键单击“项目名称”  ，然后选择“属性”  ：

    ![](linker-images/linking01w.png "在“解决方案资源管理器”中，右键单击项目名称，然后选择“属性”")
2. 在“项目属性”  中，选择“iOS 生成”  ：

    ![](linker-images/linking02w.png "选择“iOS 生成”")
3. 请按照下面的说明更改链接选项。

-----

如下所述，有三个主要选项：


### <a name="dont-link"></a>不链接

禁用链接将确保不修改任何程序集。 出于性能原因，这是 IDE 针对 iOS 模拟器时的默认设置。 对于设备生成，只有当链接器包含阻止应用程序运行的 bug 时，才应将此选项用作解决方法。 如果应用程序仅使用 -nolink  ，请提交[错误报告](https://github.com/xamarin/xamarin-macios/issues/new)。

使用命令行工具 mtouch 时，它对应于 -nolink 选项  。

<a name="Link_SDK_assemblies_only" />

### <a name="link-sdk-assemblies-only"></a>仅链接 SDK 程序集

在这种模式下，链接器不会改变你的程序集，而是通过删除应用程序不使用的所有内容来减小 SDK 程序集（即 Xamarin.iOS 附带的内容）的大小。 这是 IDE 针对 iOS 设备时的默认设置。

这是最简单的选项，因为它不需要更改任何代码。 与链接所有内容的差异在于，链接器在此模式下无法执行几个优化，因此，这是在链接所有内容需要的工作和最终应用程序大小之间的一个折中。

使用命令行工具 mtouch 时，它对应于 -linksdk  选项。

<a name="Link_all_assemblies" />

### <a name="link-all-assemblies"></a>链接所有程序集

链接所有内容时，链接器可以使用其整套优化来尽可能的缩减应用程序的大小。 该选项会修改用户代码，只要代码以链接器的静态分析无法检测到的方式使用功能，代码就可能会中断。 在这种情况下（例如 webservices、反射或序列化），可能需要在应用程序中进行某些调整才能链接所有内容。

使用命令行工具 mtouch  时，它对应于 -linkall  选项。

<a name="Controlling_the_Linker" />

## <a name="controlling-the-linker"></a>控制链接器

使用链接器时，它有时会删除可能以动态方式（甚至以间接方式）调用的代码。 为了涵盖这些情况，链接器会提供几种功能和选项，以让你更好地控制其操作。

<a name="Preserving_Code" />

### <a name="preserving-code"></a>保留代码

使用链接器时，它有时可以删除可能以动态方式调用或使用 System.Reflection.MemberInfo.Invoke 调用的代码，或通过使用 `[Export]` 属性，然后手动调用选择器来将方法导出到 Objective-C。

在这些情况下，你可以指示链接器考虑使用整个类，或通过在类级别或成员级别上应用 `[Xamarin.iOS.Foundation.Preserve]` 属性来保留单个成员。 每个未被应用程序静态链接的成员都可能被删除。 因此，此属性用于标记未静态引用但应用程序仍然需要的成员。

例如，如果你动态实例化类型，则需要保留类型的默认构造函数。 如果你使用 XML 序列化，则需要保留类型的属性。

你可以在类型的每个成员上或类型本身应用此属性。 如果你想保留整个类型，则可以在类型上使用语法 `[Preserve
(AllMembers = true)]`。

有时你需要保留某些成员，但只有在保留了包含类型的情况下才可以操作。 在这些情况下，使用 `[Preserve (Conditional=true)]`

如果不想采用 Xamarin 库上的依赖项（例如，假设生成一个跨平台可移植类库 (PCL)），你仍然可以使用此属性。

若要执行此操作，应只声明一个 PreserveAttribute 类，并在代码中使用此类，如下所示：

```csharp
public sealed class PreserveAttribute : System.Attribute {
    public bool AllMembers;
    public bool Conditional;
}
```

在哪个命名空间中定义它并不太重要，链接器会按类型名称查找此属性。

 <a name="Skipping_Assemblies" />

### <a name="skipping-assemblies"></a>跳过程序集

可以指定应从链接器进程中排除的程序集，同时允许正常链接其他程序集。 如果在某些程序集上不能使用 `[Preserve]`（例如第三方代码）或作为 bug 的临时解决方法，这会非常有用。

使用命令行工具 mtouch 时，它对应于 `--linkskip` 选项。

使用“链接所有程序集”  选项时，如果你想告知链接器跳过整个程序集，请将下面的内容放入在顶级程序集的“其他 mtouch 参数”  选项中：

```csharp
--linkskip=NameOfAssemblyToSkipWithoutFileExtension
```

如果你希望链接器跳过多个程序集，请包含多个 `linkskip` 参数：

```csharp
--linkskip=NameOfFirstAssembly --linkskip=NameOfSecondAssembly
```

没有使用此选项的用户界面，但可以在 Visual Studio for Mac 项目选项对话框或 Visual Studio 项目属性窗格中的“其他 mtouch 参数”  文本字段内提供。 （例如， --linkskip=mscorlib  不会链接 mscorlib.dll，但会链接解决方案中的其他程序集）。

<a name="Disabling_Link_Away" />

### <a name="disabling-link-away"></a>禁用“离开链接”

链接器会删除不太可能在设备上使用的代码（例如，不支持或不允许）。 在极少数的情况下，应用程序或库可能会依赖于此（工作或不工作）的代码。 自 Xamarin.iOS 5.0.1 起，可指示链接器跳过此优化。

使用命令行工具 mtouch 时，它对应于 -nolinkaway  选项。

没有使用此选项的用户界面，但可以在 Visual Studio for Mac 项目选项对话框或 Visual Studio 项目属性窗格中的“其他 mtouch 参数”  文本字段内提供。 （例如， --nolinkaway  不会删除额外代码（大约 100 kb））。

### <a name="marking-your-assembly-as-linker-ready"></a>将程序集标记为链接器就绪

用户可以选择只链接 SDK 程序集，而不执行任何指向其代码的链接。  这也意味着，不会链接到任何不属于 Xamarin 的核心 SDK 的第三方库。

这种情况通常是因为用户不想将 `[Preserve]` 属性手动添加到其代码中。  缺点是不会链接到第三方库，通常情况下，这是一个很好的默认设置，因为它不可能知道第三方库是否对链接器友好。

如果你在项目中有库，或者是可重用库的开发人员并且希望链接器将程序集视为可链接，则只需要添加程序集级别的属性 [`LinkerSafe`](xref:Foundation.LinkerSafeAttribute) 即可，如下所示：

```csharp
[assembly:LinkerSafe]
```

你的库并不真正需要为此引用 Xamarin 库。  例如，如果你正在生成将在其他平台中运行的可移植类库，你仍可以使用 `LinkerSafe` 属性。
Xamarin 链接器按名称查找 `LinkerSafe` 属性，而不是按其实际类型查找。  这意味着，你可以编写此代码，它仍然可以运行：

```csharp
[assembly:LinkerSafe]
// ... assembly attribute should be at top, before source
class LinkerSafeAttribute : System.Attribute {}
```

## <a name="custom-linker-configuration"></a>自定义链接器配置

请按照[创建链接器配置文件的说明](~/cross-platform/deploy-test/linker.md)进行操作。


## <a name="related-links"></a>相关链接

- [自定义链接器配置](~/cross-platform/deploy-test/linker.md)
- [在 Mac 上链接](~/mac/deploy-test/linker.md)
- [在 Android 上链接](~/android/deploy-test/linker.md)
