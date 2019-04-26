---
title: 如何手动安装 Xamarin.Android.Support 包所需的 Android 支持库？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 84ee33fe174c01656144e55bc3cbba7c773950fd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153424"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>如何手动安装 Xamarin.Android.Support 包所需的 Android 支持库？

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Xamarin.Android.Support.v4 的示例步骤 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

下载所需的 Xamarin.Android.Support NuGet 包 （例如通过使用 NuGet 包管理器中安装它）。

使用`ildasm`若要查看哪些版本**android_m2repository.zip** NuGet 包都需要：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
示例输出：

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

下载**android\_m2repository.zip**从 Google 使用的 URL 返回从**ildasm**。 或者，可以检查哪个版本的_Android 支持存储库_当前已安装 Android SDK 管理器中：

!["Android SDK 管理器显示 Android 支持存储库版本安装的 32"](install-android-support-library-images/sdk-extras.png)

如果版本与匹配所需的 NuGet 包，然后您无需下载任何新内容。 您可以改为重新压缩现有**m2repository**目录下的**extras\\android**中_SDK 路径_（如下所示的 Android 顶部SDK 管理器窗口）。

计算从返回的 URL 的 MD5 哈希**ildasm**。 设置要使用全部以大写字母，不留空格的结果字符串的格式。 例如，调整`$url`变量，作为所需，然后运行下面两行代码 (基于[原始C#从 Xamarin.Android 代码](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) 在 PowerShell 中：

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
示例输出：

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

复制**android\_m2repository.zip**到 **%LOCALAPPDATA%\\Xamarin\\zips\\** 文件夹。 重命名要使用从上一个 MD5 哈希计算步骤的 MD5 哈希的文件。 例如：

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

（可选）将解压缩到的文件 **%LOCALAPPDATA%\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\内容\\** (创建**内容\\m2repository**子目录)。 如果跳过此步骤，然后使用库在第一个生成将会稍长因为它将需要完成此步骤。
子目录的版本号 (**23.4.0.0**在此示例中) 不是 NuGet 包版本完全一样。 可以使用`ildasm`查找正确的版本号：

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

下载所需的 Xamarin.Android.Support NuGet 包 （例如通过使用 NuGet 包管理器中安装它）。

双击_Xamarin.Android.Support.v4_下的程序集_引用_的 Android 项目中 Visual Studio for Mac 在程序集浏览器中打开该程序集的部分。 絋粄_语言_下拉列表设置为_C#_ ，然后选择顶级_Xamarin.Android.Support.v4_从程序集浏览器导航树中的程序集. 找到`SourceUrl`下的一个属性`IncludeAndroidResourcesFrom`或`JavaLibraryReference`属性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

下载**android\_m2repository.zip**从 Google 使用`SourceUrl`从返回**ildasm**。 或者，可以检查哪个版本的_Android 支持存储库_当前已安装 Android SDK 管理器中：

!["Android SDK 管理器显示 Android 支持存储库版本安装的 32"](install-android-support-library-images/sdk-extras.png)

如果版本与匹配所需的 NuGet 包，然后您无需下载任何新内容。 您可以改为重新压缩现有**m2repository**目录下的**extras/android**中_SDK 路径_（如下所示的 Android SDK 管理器窗口顶部）.

计算从返回的 URL 的 MD5 哈希**ildasm**。 设置要使用全部以大写字母，不留空格的结果字符串的格式。 例如，调整为所需的 URL 字符串，然后在中运行以下命令**Terminal.app**命令提示符：

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

另一种方法是使用`csharp`解释程序以运行[相同C#Xamarin.Android 本身使用的代码](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)。
为此，请调整`url`变量，作为所需，然后在中运行以下命令**Terminal.app**命令提示符：

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
示例输出：

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

复制**android\_m2repository.zip**到 **$HOME/.local/share/Xamarin/zips/** 文件夹。 重命名要使用从上一个 MD5 哈希计算步骤的 MD5 哈希的文件。 例如：

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

（可选）将它解压缩到文件： 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(创建**内容/m2repository**子目录)。 如果跳过此步骤，然后使用库在第一个生成将会稍长因为它将需要完成此步骤。

子目录的版本号 (**23.4.0.0**在此示例中) 不是 NuGet 包版本完全一样。 在中作为**ildasm**前面步骤中，可以使用 Visual Studio for Mac 中的程序集浏览器以查找正确的版本号。 寻找`Version`下的一个属性`IncludeAndroidResourcesFrom`或`JavaLibraryReference`属性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>其他参考

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – Inaccurate"下载失败。 请下载{0}并将其放{1}目录。" 和"请安装包:{0}SDK 安装程序中提供"与 Xamarin.Android.Support 包相关的错误消息

### <a name="next-steps"></a>后续步骤

本文档讨论截至 2016 年 8 月的当前行为。 本文档中所述的技术不是 xamarin，稳定的测试套件的一部分，因此它在将来可能会损坏。

获取进一步的帮助，请与我们联系，或如果此问题仍即使利用上述信息，请参阅[了可用于 Xamarin 的支持选项？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要，提交新 bug。

