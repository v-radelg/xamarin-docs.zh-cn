---
title: 如何手动安装 Xamarin.Android.Support 包所需的 Android 支持库？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 107fcd39a64ef1d7758d211ab47a07e1ece52f4e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757242"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>如何手动安装 Xamarin.Android.Support 包所需的 Android 支持库？

## <a name="example-steps-for-xamarinandroidsupportv4"></a>支持 Xamarin 的示例步骤 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

下载所需的 Xamarin。支持 NuGet 包（例如通过使用 NuGet 包管理器进行安装）。

使用`ildasm`检查 NuGet 包所需的**android_m2repository**版本：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

示例输出：

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

使用从**ildasm**返回的 URL 从 Google 下载 **\_android m2repository。** 或者，你可以查看 Android SDK 管理器中当前已安装的_Android 支持存储库_版本：

!["Android SDK 管理器，显示安装了 Android 支持存储库版本 32"](install-android-support-library-images/sdk-extras.png)

如果版本与 NuGet 包所需的版本相匹配，则无需下载任何新内容。 相反，可以在_SDK 路径_中的 " **\\额外 android** " 下重新压缩现有的**m2repository**目录（如 Android SDK 管理器窗口顶部所示）。

计算从**ildasm**返回的 URL 的 MD5 哈希值。 将生成的字符串格式化为使用所有大写字母，不使用空格。 例如，根据需要调整`$url`变量，然后在 PowerShell 中运行以下2行（基于[Xamarin 中的C#原始代码](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)）：

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

示例输出：

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

将**android\_m2repository**复制到 **%\\LOCALAPPDATA% Xamarin\\zips\\** 文件夹中。 重命名该文件，以使用以前的 MD5 哈希计算步骤中的 MD5 哈希。 例如:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

可有可无将文件解压缩到 **% LOCALAPPDATA%\\xamarin\\xamarin\\23.4.0.0\\\\内容**（创建**内容 m2repository\\** 子目录）。 如果跳过此步骤，则使用库的第一个生成将需要更长时间，因为它需要完成此步骤。
子目录（在本示例中为**23.4.0.0** ）的版本号与 NuGet 包版本不完全相同。 您可以使用`ildasm`查找正确的版本号：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

示例输出：

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

下载所需的 Xamarin。支持 NuGet 包（例如通过使用 NuGet 包管理器进行安装）。

双击 Visual Studio for Mac 中 Android 项目的 "_引用_" 部分下的 " _Xamarin_ " 程序集，以在程序集浏览器中打开该程序集。 确保将 "_语言_" 下拉端设置为_C#_ ，并从 "程序集浏览器" 导航树中选择顶层 Xamarin._支持_的程序集。 在一个`IncludeAndroidResourcesFrom`或属性`JavaLibraryReference`下找到属性：`SourceUrl`

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

使用从**ildasm**返回的返回的`SourceUrl`从 Google 下载 **\_android m2repository** 。 或者，你可以查看 Android SDK 管理器中当前已安装的_Android 支持存储库_版本：

!["Android SDK 管理器，显示安装了 Android 支持存储库版本 32"](install-android-support-library-images/sdk-extras.png)

如果版本与 NuGet 包所需的版本相匹配，则无需下载任何新内容。 相反，你可以在_SDK 路径_中重新压缩位于 "**额外/android** " 下的现有**M2repository**目录（如 Android SDK 管理器窗口顶部所示）。

计算从**ildasm**返回的 URL 的 MD5 哈希值。 将生成的字符串格式化为使用所有大写字母，不使用空格。 例如，根据需要调整 URL 字符串，然后在**终端应用**命令提示符下运行以下命令：

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

另一种方法是使用`csharp`解释器运行[Xamarin 本身C#所使用的相同代码](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)。
为此，请根据需要`url`调整变量，然后在**终端**命令提示符下运行以下命令：

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

示例输出：

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

将**android\_m2repository**复制到 **$HOME/.local/share/xamarin/zips/** 文件夹。 重命名该文件，以使用以前的 MD5 哈希计算步骤中的 MD5 哈希。 例如:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

可有可无将文件解压缩到： 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

（创建**content/m2repository**子目录）。 如果跳过此步骤，则使用库的第一个生成将需要更长时间，因为它需要完成此步骤。

子目录（在本示例中为**23.4.0.0** ）的版本号与 NuGet 包版本不完全相同。 如前所述，可以使用 Visual Studio for Mac**中的程序**集浏览器来查找正确的版本号。 `Version`在`IncludeAndroidResourcesFrom`或属性`JavaLibraryReference`之一下查找属性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>其他参考

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) –不准确的 "下载失败。 请下载{0}并将其{1}放入目录。 " 和 "请安装包： '{0}' 在 SDK 安装程序中可用" 错误消息与 Xamarin. 支持包相关

### <a name="next-steps"></a>后续步骤

本文档讨论截至2016年8月的当前行为。 本文档中所述的技术不是适用于 Xamarin 的稳定测试套件的一部分，因此可能会在将来中断。

若要获得更多帮助，请联系我们，或者，如果在使用上述信息后仍出现此问题，请参阅[哪些支持选项可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项、建议的信息，以及如何在需要时记录新 bug.
