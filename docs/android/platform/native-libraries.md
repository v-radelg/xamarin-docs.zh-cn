---
title: 使用本机库
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 7ef9e0415d7d1e5fe75be70e0ccf6e06a5eaf332
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027058"
---
# <a name="using-native-libraries"></a>使用本机库

Xamarin 支持通过标准 PInvoke 机制使用本机库。 你还可以将不属于 OS 的其他本机库绑定到你的 apk。

若要使用 Xamarin Android 应用程序部署本机库，请将库的二进制文件添加到项目中，并将其**生成操作**设置为**AndroidNativeLibrary**。

若要使用 Xamarin Android 库项目部署本机库，请将库的二进制文件添加到项目，并将其**生成操作**设置为**EmbeddedNativeLibrary**。

请注意，由于 Android 支持多个应用程序二进制接口（Abi），因此，Xamarin 必须知道为其生成本机库的 ABI。
可以通过两种方法完成：

1. 路径 "窃听"
1. 通过使用项目文件中的 `AndroidNativeLibrary/Abi` 元素

通过路径探查，本机库的父目录名称用于指定库的目标 ABI。 因此，如果你将 `lib/armeabi/libfoo.so` 添加到项目中，则 ABI 将为 "探查" `armeabi`。

或者，你可以编辑项目文件以显式指定要使用的 ABI：

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

有关使用本机库的详细信息，请参阅[使用本机库的互操作](https://www.mono-project.com/docs/advanced/pinvoke/)。

## <a name="debugging-native-code-with-visual-studio"></a>在 Visual Studio 中调试本机代码

如果使用的是*Visual studio 2019*或*visual studio 2017*，则无需像上文所述修改项目文件。
可以通过添加对C++ C++ **动态共享库（Android）** 项目的项目引用，在 Xamarin Android 解决方案内生成和调试。

若要在C++项目中调试本机代码，请执行以下步骤：

1. 双击 "项目**属性**"，然后选择 " **Android 选项**" 页。
2. 向下滚动到 "**调试选项**"。
3. 在**调试器**下拉菜单中，选择**C++** （而不是默认的 **.net （Xamarin）** ）。

Visual Studio C++开发人员可以查看[SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)示例，尝试从C++ visual Studio 2019 或带 Xamarin 的 visual studio 2017 进行调试;有关详细信息，请参阅我们的[博客文章](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/)。

## <a name="related-links"></a>相关链接

- [SanAngeles_NativeDebug （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [开发 Xamarin Android 本机应用程序](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
