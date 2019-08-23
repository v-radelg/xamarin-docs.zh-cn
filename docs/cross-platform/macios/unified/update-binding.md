---
title: 将绑定迁移到 Unified API
description: 本文介绍更新现有 Xamarin 绑定项目以支持适用于 Xamarin 和 Xamarin 应用程序的统一 Api 所需的步骤。
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 90ef47d4e105dc401369c92e9196111c060314e3
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976421"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>将绑定迁移到 Unified API

_本文介绍更新现有 Xamarin 绑定项目以支持适用于 Xamarin 和 Xamarin 应用程序的统一 Api 所需的步骤。_

## <a name="overview"></a>概述

2015年2月1日起, Apple 要求 iTunes 和 Mac App Store 的所有新提交必须是64位应用程序。 因此, 任何新的 Xamarin 或 Xamarin 应用程序都需要使用新的 Unified API 而不是现有的经典 Monotouch.dialog 和 MonoMac Api 来支持64位。

此外, 任何 Xamarin 绑定项目还必须支持将新的统一 Api 包含在64位 Xamarin 或 Xamarin 项目中。 本文将介绍更新现有绑定项目以使用 Unified API 所需的步骤。

## <a name="requirements"></a>要求

若要完成本文中所述的步骤, 需要满足以下要求:

- **Visual Studio for Mac** -在开发计算机上安装和配置 Visual Studio for Mac 的最新版本。
- **Apple mac** -需要 apple mac 才能构建 IOS 和 Mac 的绑定项目。

Windows 计算机上的 Visual studio 不支持绑定项目。

## <a name="modify-the-using-statements"></a>修改 Using 语句

统一 Api 使你可以比以往更轻松地在 Mac 和 iOS 之间共享代码, 并允许你支持具有相同二进制的32和64位应用程序。 通过删除命名空间中的_MonoMac_和_monotouch.dialog_前缀, 可以更简单地跨 Xamarin 和 xamarin iOS 应用程序项目实现共享。

因此, 我们需要修改所有绑定协定 (以及绑定`.cs`项目中的其他文件), 以删除`using`语句中的_MonoMac_和_monotouch.dialog_前缀。

例如, 给定以下 using 语句:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

我们将去掉前缀, `MonoTouch`导致出现以下结果:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

同样, 我们需要为绑定项目中的任何`.cs`文件执行此操作。 进行这种更改后, 下一步是将绑定项目更新为使用新的本机数据类型。

有关 Unified API 的详细信息, 请参阅[Unified API](~/cross-platform/macios/unified/index.md)文档。 有关支持32和64位应用程序的更多背景和有关框架的信息, 请参阅[32 和64位平台注意事项](~/cross-platform/macios/32-and-64/index.md)文档。

## <a name="update-to-native-data-types"></a>更新为本机数据类型

客观-C 将`NSInteger`数据类型映射到`int32_t` `int64_t` 32 位系统上的和64位系统上的。 若要匹配此行为, 新 Unified API 会将以前使用的`int` (在 .net 中定义为 " `System.Int32`始终") 替换为新的数据类型`System.nint`:。

除了`nint`新的数据类型, Unified API 还`nuint`介绍了和`nfloat`类型, 以便映射到`NSUInteger`和`CGFloat`类型。

在上述情况下, 我们需要查看我们的 API, 并确保的所有`NSInteger`实例`NSUInteger` `CGFloat` , 以及以前映射到`float` `int` `uint`的并更新为新`nint`的 `nuint` 和`nfloat`类型。

例如, 给定的目标 C 方法定义:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

如果以前的绑定协定具有以下定义:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

我们会将新的绑定更新为:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```

如果要映射到的第三方版本与最初链接到的版本不同, 我们需要`.h`检查库的标头文件, 并查看是否存在、、 `uint32_t`或的显式调用`int` `int32_t` `unsigned int`已升级为`NSInteger`或`NSUInteger` 。 `CGFloat` `float` 如果是这样, 则还需要对`nint`其`nuint`映射`nfloat`进行相同的修改。

若要了解有关这些数据类型更改的详细信息, 请参阅[本机类型](~/cross-platform/macios/nativetypes.md)文档。

## <a name="update-the-coregraphics-types"></a>更新 CoreGraphics 类型

用于`CoreGraphics`使用32或64位的 "点"、"大小" 和 "矩形" 数据类型取决于其上运行的设备。 当 Xamarin 最初绑定 iOS 和 Mac api 时, 我们使用了现有的数据结构, 这些结构发生了`System.Drawing`匹配`RectangleF`中的数据类型 (例如)。

由于支持64位和新的本机数据类型的要求, 在调用`CoreGraphic`方法时, 需要对现有代码进行以下调整:

- **CGRect** - `RectangleF`在`CGRect`定义浮点矩形区域时使用而不是。
- **CGSize** - `SizeF`在`CGSize`定义浮点大小 (宽度和高度) 时使用而不是。
- **CGPoint** -定义`CGPoint`浮点位置`PointF` (X 和 Y 坐标) 时使用而不是。

在上述情况下, 我们将需要查看我们的 API, 并确保之前绑定`CGRect`到`CGSize` `SizeF` `RectangleF`或`CGPoint` `PointF`更改为本机类型`CGRect`的任何实例,`CGSize` 或`CGPoint`直接。

例如, 给定的目标-C 初始值设定项:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

如果以前的绑定包含以下代码:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

我们会将该代码更新为:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

现在已准备好所有代码更改, 我们需要修改绑定项目或使文件与统一 Api 进行绑定。

## <a name="modify-the-binding-project"></a>修改绑定项目

若要将绑定项目更新为使用统一 api, 最后一步是修改`MakeFile`用于构建项目的或 Xamarin 项目类型 (如果从 Visual Studio for Mac 中进行绑定) 并指示_btouch_绑定而不是经典 Api。


### <a name="updating-a-makefile"></a>更新生成文件

如果我们使用生成文件将绑定项目构建到 Xamarin 中。DLL, 则需要包含`--new-style`命令行选项, 而不是`btouch`调用`btouch-native` 。

因此, 请考虑`MakeFile`以下事项:

<!--markdownlint-disable MD010 -->
```makefile
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch


all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```
<!--markdownlint-enable MD010 -->

我们需要从调用`btouch`切换到`btouch-native`, 因此我们将按如下所示调整宏定义:

```makefile
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

我们会更新对`btouch`的调用, 并`--new-style`添加选项, 如下所示:

<!--markdownlint-disable MD010 -->
```makefile
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```
<!--markdownlint-enable MD010 -->

现在, 我们可以正常`MakeFile`执行, 以生成 API 的新64位版本。

### <a name="updating-a-binding-project-type"></a>更新绑定项目类型

如果我们使用 Visual Studio for Mac 绑定项目模板来构建 API, 则需要更新为新的 Unified API 版本的绑定项目模板。 执行此操作的最简单方法是启动新的 Unified API 绑定项目并复制所有现有代码和设置。

请执行以下操作：

1. 开始 Visual Studio for Mac。
2. 选择 "**文件** > " "**新建** > **解决方案 ...** "
3. 在 "新建解决方案" 对话框中, 选择 " **ios** > **Unified API** > **ios 绑定项目**": 

    [![](update-binding-images/image01new.png "在 \"新建解决方案\" 对话框中, 选择 \"iOS/Unified API/iOS 绑定项目\"")](update-binding-images/image01new.png#lightbox)
4. 在 "配置新项目" 对话框中, 输入新绑定项目的**名称**, 然后单击 **"确定"** 按钮。
5. 包括要为其创建绑定的64位版本的目标-C 库。
6. 在现有的32位 Classic API 绑定项目 (如`ApiDefinition.cs`和`StructsAndEnums.cs`文件) 中复制源代码。
7. 对源代码文件进行上述已记录的更改。

进行所有这些更改后, 就可以生成新的64位版本的 API, 就像32位版本一样。

## <a name="summary"></a>总结

在本文中, 我们已展示了需要对现有 Xamarin 绑定项目进行的更改, 以支持新的统一 Api 和64位设备, 以及生成新的64位兼容版本的 API 所需的步骤。



## <a name="related-links"></a>相关链接

- [Mac 和 iOS](~/cross-platform/macios/index.md)
- [Unified API](~/cross-platform/macios/nativetypes.md)
- [32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)
- [升级现有的 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Unified API](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
