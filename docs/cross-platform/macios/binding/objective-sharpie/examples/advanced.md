---
title: 高级 （手动） 的真实示例
description: 本文档介绍如何使用 xcodebuild 输出作为目标 Sharpie，后者用于了解目标 Sharpie 实质上的作用的输入。
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e820a0c208907a95dda4a50427bb4dac27b88964
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977893"
---
# <a name="advanced-manual-real-world-example"></a>高级 （手动） 的真实示例

**此示例使用[POP 库从 Facebook](https://github.com/facebook/pop)。**

本部分介绍了一更高级的绑定，其中，我们将使用 Apple 的方法`xcodebuild`工具以首先生成 POP 项目，然后输入目标 Sharpie，手动推导出。 这实质上是包括目标 Sharpie 实质上上一节中执行的操作。

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

由于 POP 库都 Xcode 项目 (`pop.xcodeproj`)，我们只需使用`xcodebuild`生成 POP。 此过程可能反过来又会生成目标 Sharpie 可能需要分析的标头文件。 这是构建之前绑定是重要的原因。 通过在生成时`xcodebuild`确保相同的 SDK 标识符和体系结构的传递你想要传递给目标 Sharpie （并且请记住，目标 Sharpie 3.0 可以执行此通常操作 ！）：

```
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1
 
=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===
 
...
 
CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP
 
...
 
** BUILD SUCCEEDED **
```

作为的一部分将有很多在控制台中输出的生成信息`xcodebuild`。 上面所示，我们可以看到"CpHeader"目标运行其中标头文件复制到生成输出目录。 这通常是这种情况，并使绑定更容易： 作为本机库生成的一部分，标头文件通常复制到可以使分析变得更容易为绑定的"公开"可使用位置。 在这种情况下，我们知道 POP 的标头文件位于`build/Headers`目录。

我们现已准备好将绑定 POP。 我们知道我们想要为 SDK 构建`iphoneos8.1`与`arm64`体系结构，以及我们所关心的标头文件位于`build/Headers`下 POP git 签出。 如果我们查看`build/Headers`目录中，我们将看到多个标头文件：

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

如果我们看一下`POP.h`，我们可以看到它是库的主顶级标头文件`#import`s 其他文件。 因此，我们只需传递`POP.h`到目标 Sharpie 和 clang 将完成其余部分在幕后：

```
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

你会注意到我们传递`-scope build/Headers`目标 Sharpie 参数。 因为 C 和 OBJECTIVE-C 的库必须`#import`或`#include`库并不想要将绑定，API 的详细信息，实现其他标头文件`-scope`参数告知目标 Sharpie 忽略中未定义任何 API文件中的某处`-scope`目录。

您会发现`-scope`参数通常是可选的完全实现的库，但是没有显式提供没有什么坏处。

此外，我们指定`-c -Ibuild/headers`。 首先，`-c`参数告知目标 Sharpie 停止解释命令行参数并传递任何后续自变量_直接为 clang 编译器_。 因此，`-Ibuild/Headers`是指示要搜索的 clang 的 clang 编译器参数包括下`build/Headers`，这是所在的 POP 标头。 而不使用此参数，clang 将不知道文件的位置的`POP.h`是`#import`运算结果。 _使用目标 Sharpie 几乎所有"问题"看，这是找出要传递到 clang_。

### <a name="completing-the-binding"></a>完成绑定

现在已生成目标 Sharpie`Binding/ApiDefinitions.cs`和`Binding/StructsAndEnums.cs`文件。

这些是目标 Sharpie 基本第一次绑定，并在少数情况下可能只需要。 但是上述，开发人员通常需要对目标 Sharpie 完成后手动修改生成的文件[修复任何问题](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)，可能不会自动处理通过该工具。

更新完成后，现在可以添加用于 Mac 的 Visual Studio 中的绑定项目到这两个文件，或直接传递`btouch`或`bmac`工具以生成最终的绑定。

在绑定过程的详尽描述，请参阅我们[完整操作实例说明](~/ios/platform/binding-objective-c/walkthrough.md)。
