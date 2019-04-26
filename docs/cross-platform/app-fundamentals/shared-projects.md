---
title: 使用共享项目共享代码
description: 共享的项目，可以编写多个不同的应用程序项目引用的通用代码。 该代码编译为每个引用的项目的一部分，并可以包括编译器指令，以帮助将特定于平台的功能合并到基本共享的代码。
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 61096635cd94d0fdd0abe6fda59c4efa41eeceb1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61192861"
---
# <a name="shared-projects-code-sharing"></a>共享项目代码共享

_共享的项目，可以编写多个不同的应用程序项目引用的通用代码。该代码编译为每个引用的项目的一部分，并可以包括编译器指令，以帮助将特定于平台的功能合并到基本共享的代码。_

共享项目 （有时也称为共享资产项目），可以编写代码，包括 Xamarin 应用程序的多个目标项目之间共享。

它们支持编译器指令，以便您可以有条件地包括特定于平台的代码编译到项目引用共享项目的子集。 此外，还有 IDE 支持，以帮助管理编译器指令和可视化的代码将在每个应用程序中看起来。

如果已使用过的文件链接来共享代码项目之间，共享的项目的工作方式类似，但大大改进的 IDE 支持。

## <a name="what-is-a-shared-project"></a>共享项目是什么？

与大多数其他项目类型不同的共享的项目没有任何输出 （以 DLL 形式），而是会将代码编译到引用它的每个项目。 下图中阐释了这-从概念上讲共享项目的全部内容是"复制到"引用的每个项目并编译，就好像它是其中一部分。

![](shared-projects-images/sharedassetproject.png "共享的项目体系结构")

共享项目中的代码可以包含编译器指令将启用或禁用的具体取决于哪个应用程序项目使用的代码，建议在关系图中的彩色的平台框的代码段。

不会不获取共享的项目编译本身，它存在单纯用作源代码文件可以包含在其他项目中的分组。 当引用另一个项目，作为有效地编译代码*一部分*的该项目。 共享项目不能引用 （包括其他共享的项目） 的任何其他项目类型。

请注意，Android 应用程序项目不能引用其他 Android 应用程序项目-例如，Android 单元测试项目不能引用的 Android 应用程序项目。 有关此限制的详细信息，请参阅此[论坛讨论](http://forums.xamarin.com/discussion/comment/98092/)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 演练

本部分逐步讲解如何创建和使用共享的项目使用 Visual Studio for mac。 请参阅向[共享的项目示例](#Shared_Project_Example)部分，了解完整的示例。

## <a name="creating-a-shared-project"></a>创建共享的项目

若要创建新的共享项目，导航到**文件 > 新建解决方案...** (或右键单击现有的解决方案，然后选择**添加 > 添加新项目...**):

[![新的共享的项目](shared-projects-images/xs-newsolution-sml.png "新的解决方案")](shared-projects-images/xs-newsolution.png#lightbox)

在下一个屏幕上，选择项目名称，然后单击**创建**。

新的共享项目如下所示-请注意，有没有引用或组件节点;这些不支持共享的项目。

![空共享项目](shared-projects-images/xs-empty.png "空共享项目")

为共享的项目，以非常有用，它需要至少一个可生成的项目 （如 iOS 或 Android 应用程序或库或 PCL 项目） 的引用。 它没有引用它，因此语法 （或任何其他） 时，共享的项目不会不获取编译错误将不会突出显示之前引用的其他内容。

添加对共享项目的引用可像引用常规库项目一样。 此屏幕截图显示了引用共享项目的 Xamarin.iOS 项目。

![](shared-projects-images/xs-reference.png "对共享项目的项目引用")

对共享项目引用的其他库或应用程序后可以生成解决方案并在代码中查看任何错误。 当对共享项目引用的_两个或多个_其他项目，将出现一个菜单左上方的源代码编辑器中显示选择的项目引用此文件。

## <a name="shared-project-options"></a>共享项目选项

当您右键单击某个共享的项目并选择**选项**那里较少的设置比其他项目类型。 由于共享的项目未编译 （代表自身） 中，无法设置输出或编译器选项、 项目配置、 程序集签名或自定义命令。 共享项目中的代码有效地从任何正在引用其继承这些值。



**选项**屏幕如下所示的项目**名称**并**Default Namespace**是您通常将更改的只有两个设置。


![](shared-projects-images/xs-sharedprojectoptions.png "共享项目选项")



# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)



## <a name="visual-studio-walkthrough"></a>Visual Studio 演练


本部分将指导完成如何创建和使用共享的项目使用 Visual Studio。 请参阅向[共享的项目示例](#Shared_Project_Example)部分，了解完整的实现。

### <a name="creating-a-shared-project"></a>创建共享的项目

若要创建新的共享项目，导航到**文件 > 新建解决方案...** 和选择的项目和解决方案的名称。

![](shared-projects-images/vs-newsolution.png "新建解决方案")

您还可以将新的共享项目通过右键单击解决方案文件，然后选择添加到解决方案**添加 > 新建项目...**.新的共享项目看起来如下所示 （后已添加的类文件）-请注意，没有任何引用或组件节点;这些不支持共享的项目。

![](shared-projects-images/vs-empty.png "空共享的项目")

为共享的项目，以非常有用，它需要至少一个可生成的项目 （如 iOS 或 Android 应用程序或库或 PCL 项目） 的引用。 它没有引用它，因此语法 （或任何其他） 时，共享的项目不会不获取编译错误将不会突出显示之前引用的其他内容。

添加对共享项目的引用可像引用常规库项目一样。 此屏幕截图显示了引用共享项目的 Xamarin.iOS 项目。

![](shared-projects-images/vs-reference.png "对共享项目的项目引用")

对共享项目引用的其他库或应用程序后可以生成解决方案并在代码中查看任何错误。 当对共享项目引用的_两个或多个_其他项目中的源的代码编辑器，若要查看哪些项目引用在当前代码文件的顶部左侧出现一个菜单。


### <a name="shared-project-properties"></a>共享的项目属性


当你选择一个共享项目那里设置更少比其他项目类型属性面板中。 由于共享的项目未编译 （代表自身） 中，无法设置输出或编译器选项、 项目配置、 程序集签名或自定义命令。 共享项目中的代码有效地从任何正在引用其继承这些值。

**属性**下方 – 显示面板**根 Namespace**是唯一可以更改的设置。

![](shared-projects-images/vs-sharedprojectproperties.png "共享的项目属性")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>共享的项目示例

[Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)示例使用一个共享项目，以包含通用代码使用由两个 iOS、 Android 和 Windows Phone 应用程序。 同时`SQLite.cs`和`TaskRepository.cs`源代码文件可以利用编译器指令 （例如。 `#if __ANDROID__`) 可为每个引用它们的应用程序生成不同的输出。

完整的解决方案结构如下所示 (在 Visual Studio for Mac 和 Visual Studio 中分别):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Visual Studio Mac 解决方案")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Visual Studio 解决方案")

-----

Windows Phone 项目可以导航从在 Visual Studio for Mac 中，即使该项目类型不支持编译在 Visual Studio for mac。

正在运行的应用程序如下所示：

![](shared-projects-images/example.png "iOS、 Android、 Windows Phone 示例")

## <a name="summary"></a>总结

本文档介绍如何共享项目工作，可以如何创建和使用 Visual Studio for Mac 和 Visual Studio 中，在它们，并引入演示操作中的共享项目的简单示例应用程序。

## <a name="related-links"></a>相关链接

- [Tasky 示例应用](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [可移植类库 （示例）](~/cross-platform/app-fundamentals/pcl.md)
- [共享代码选项 （示例）](~/cross-platform/app-fundamentals/code-sharing.md)
