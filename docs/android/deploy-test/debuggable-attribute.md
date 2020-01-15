---
title: 可调试属性
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487966"
---
# <a name="debuggable-attribute"></a>可调试属性

若要进行调试，Android 需支持 Java 调试线协议 (JDWP)。 这是一种允许 ADB 等工具与 JVM 通信的技术。 尽管在开发期间 JDWP 非常重要，仍应在发布应用程序之前将其禁用。

JDWP 可以由 Android 应用程序中 `android:debuggable` 属性的值配置。 选择以下三种方法之一在 Xamarin.Android 中设置此属性  ：

## <a name="androidmanifestxml"></a>AndroidManifest.xml

创建或打开 `AndroidManifext.xml` 文件，并在其中设置 `android:debuggable` 属性。 请特别注意，不要交付启用了调试的发行版本。

## <a name="add-an-application-class-attribute"></a>添加应用程序类属性

如果 Xamarin.Android 应用具有一个包含 `[Application]` 属性的类，请将该属性更新为 `[Application(Debuggable = true)]`。 将其设置为 `false` 以禁用。

## <a name="add-an-assembly-attribute"></a>添加程序集属性

如果 Xamarin Android 应用还没有 `[Application]` 类属性，请在 c# 文件中添加程序集级别属性 `[assembly: Application(Debuggable=true)]`。 将其设置为 `false` 以禁用。

## <a name="summary"></a>总结

如果 `AndroidManifest.xml` 和 `ApplicationAttribute` 均存在，则 `AndroidManifest.xml` 的内容将优先于 `ApplicationAttribute` 所指定的内容。

如果同时添加类属性和程序集属性，将出现编译器错误  ：

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

默认情况 - 如果既没有 `AndroidManifest.xml` 也没有 `ApplicationAttribute`，则 `android:debuggable` 属性的值将取决于是否生成了调试符号。 如果调试符号存在，则 Xamarin.Android 会将 `android:debuggable` 属性设为 `true`。

> [!WARNING]
> `android:debuggable` 属性的值不一定依赖于生成配置。 发行版本可能会将 `android:debuggable` 属性设为 true。 如果使用属性设置此值，则可以选择在编译器指令中包装属性：
> 
> ```csharp
> #if DEBUG
> [Application(Debuggable = true)]
> #else
> [Application(Debuggable = false)]
> #endif
> ```

## <a name="related-links"></a>相关链接

- [Android 市场中的可调试应用](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/)
