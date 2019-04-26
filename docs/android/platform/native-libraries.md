---
title: 使用本机库
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 1b0771a0ccc2597ebd800468b82044e4020d9d94
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153303"
---
# <a name="using-native-libraries"></a>使用本机库

Xamarin.Android 支持通过标准的 PInvoke 机制的本机库的使用。 此外可以绑定其他本机库，后者不是你.apk 到操作系统的一部分。

若要部署 Xamarin.Android 应用程序使用的本机库，将二进制库添加到项目并设置其**生成操作**到**AndroidNativeLibrary**。

若要部署使用 Xamarin.Android 类库项目的本机库，将二进制库添加到项目并设置其**生成操作**到**EmbeddedNativeLibrary**。

请注意，由于 Android 支持多个应用程序二进制接口 (Abi)，则 Xamarin.Android 必须知道本机库专为哪个 ABI。
可以通过两种方法完成：

1.  路径"探查"
1.  通过使用`AndroidNativeLibrary/Abi`位于项目文件中的元素


通过路径探查，本机库的父目录名称用于指定库的目标 ABI。 因此，如果您将添加`lib/armeabi/libfoo.so`到项目中，然后 ABI 将被"探查"为`armeabi`。

或者，可以编辑项目文件显式指定 ABI 使用：

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

有关使用本机库的详细信息，请参阅[互操作使用本机库](https://www.mono-project.com/docs/advanced/pinvoke/)。

## <a name="debugging-native-code-with-visual-studio"></a>使用 Visual Studio 调试本机代码

如果您使用的*Visual Studio 2019*或*Visual Studio 2017*，无需修改你的项目文件，如上文所述。
可以生成和调试C++添加到项目引用在 Xamarin.Android 解决方案内C++**动态共享库 (Android)** 项目。

若要调试本机C++在项目中的代码，执行以下步骤：

1. 双击项目**属性**，然后选择**Android 选项**页。
2. 向下滚动到**调试选项**。
3. 在中**调试器**下拉列表菜单中，选择**C++** (而不是默认值 **/.net (Xamarin)**)。

Visual StudioC++开发人员可以查看[SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)示例尝试来调试C++从 Visual Studio 2019 或使用 Xamarin; Visual Studio 2017 和是指我们[博客文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/)的详细信息信息。



## <a name="related-links"></a>相关链接

- [SanAngeles_NativeDebug (sample)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
- [开发 Xamarin Android 本机应用程序](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
