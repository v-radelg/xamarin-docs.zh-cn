---
title: 适用于 Xamarin.iOS 的故障排除技巧
description: 本文档提供适用于 Xamarin.iOS 应用程序开发过程故障排除的各种技巧。 它描述了特定错误消息，以及其他潜在问题。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2018
ms.openlocfilehash: 1a98cf854ffdd1d4904981f85fd8e33ad486743c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417369"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>适用于 Xamarin.iOS 的故障排除技巧 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin.iOS 不能解决 System.ValueTuple

此错误是由于使用 Visual Studio 不兼容。

- **Visual Studio 2017 Update 1** （版本 15.1 或更低版本） 是仅与兼容**System.ValueTuple NuGet 4.3.0** （或更低版本）。

- **Visual Studio 2017 Update 2** （版本 15.2 或更高版本） 是仅与兼容**System.ValueTuple NuGet 4.3.1**或更高版本。

请选择对应于 Visual Studio 2017 安装正确 System.ValueTuple NuGet。


## <a name="receiving-error-retrieving-update-information-error-message"></a>收到检索更新信息的错误错误消息

尝试更新该软件和此错误消息出现时，请尝试在 IDE 中重新启动 IDE 并注销然后再在到你的帐户。

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>如何使用 Interface Builder 创建插座或操作？

使用适用于 Mac 和 Visual studio 的 Xamarin 设计器适用于 Visual Studio 中的 iOS 引入，Xamarin.iOS 开发人员现在可以创建情节提要和.xibs 通过 UI 的优势。 请参阅[Hello，iOS](~/ios/get-started/hello-ios/index.md)使用设计器的详细信息的指南。

此外可以对 Apple 的引用[输出口](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html)并[操作](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html)IB 中使用输出口和操作的详细信息的指南。

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding 将引发 notsupportedexception 异常

您可能正在使用未按默认添加的编码。 检查[国际化](~/ios/app-fundamentals/localization/index.md)页后，可以了解如何添加对更多编码的支持。

## <a name="systemmissingmethodexception-anything-else"></a>System.MissingMethodException （任何其他内容）

该成员可能已删除链接器，并因此在运行时程序集中不存在。  有多个解决方案：

- 添加[ `[Preserve]` ](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute)属性成员。  这将阻止链接器中将其删除。
- 调用时[ **mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29)，使用 **-nolink**或者 **-linksdkonly**选项：
  - **-nolink**禁用所有链接。
  - **-linksdkonly**仅将链接提供 Xamarin.iOS 程序集，如**xamarin.ios.dll**，同时保留用户创建的程序集中的所有类型 (即。 应用程序项目)。

请注意，链接程序集，以便生成可执行文件是较小;因此，禁用链接可能会导致更大的可执行文件不是最好。

## <a name="you-are-getting-a-modelnotimplementedexception"></a>如果您听到 ModelNotImplementedException

如果收到此异常，这意味着您所调用的基。重写一个模型的类上的方法 （)。 不需要的模型 （这些是使用 [模型] 特性标记的类） 在类中调用基方法。

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>此类不是编码符合密钥 XXXX 键值

如果在加载它意味着在托管类找不到 XXXX 值 NIB 文件时收到此错误。 这意味着你缺少如下声明：

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

上面的定义由自动生成 Visual Studio for Mac 向 Visual Studio for Mac 中添加任何 XIB 文件`NAME_OF_YOUR_XIB_FILE.designer.xib.cs`文件。

此外，其中包含上面的代码中的类型必须的子类[NSObject](xref:Foundation.NSObject)。  如果在命名空间中包含的类型，它还应具有[[注册]](xref:Foundation.RegisterAttribute)提供没有命名空间的类型名称 （如 Interface Builder 不支持在类型中的命名空间） 的属性：

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>未知的类 XXXX Interface Builder 文件中

如果在接口生成器文件中定义一个类，但未提供实际实现的示例中，会生成此错误在C#代码。

您需要添加一些代码如下：

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException:未找到为 Foo.Bar::ctor(System.IntPtr) 构造函数

当代码尝试实例化从 Interface Builder 文件引用的类的实例时，将在运行时生成此错误。 这意味着你忘记添加采用一个单一 IntPtr，作为参数的构造函数。

带有一个 IntPtr 句柄的构造函数用于绑定具有其非托管表示形式的托管的对象。

若要解决此问题，到类 Foo.Bar 添加以下代码行：

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>{Foo} 类型不包含用于定义`GetNativeField' and no extension method `GetNativeField 的类型找不到 {Foo}

如果你收到此错误在设计器生成的文件 (*。 xib.designer.cs)，这意味着两个条件之一：

 **1） 缺少分部类或基类**

在设计器生成的分部类必须具有相应分部类在用户代码中继承的一些子类`NSObject`，通常`UIViewController`。 确保您有这样的类提供错误的类型。

 **2） 默认命名空间更改**

使用你的项目的默认命名空间设置生成的设计器文件。 如果您更改这些设置，或者重命名项目，生成的分部类可能无法再在相同的命名空间对应的用户代码。

在项目选项对话框中，可以找到 Namespace 设置。 默认命名空间中找到**常规-> Main 设置**部分。 如果为空，则项目的名称用作默认值。 更多高级命名空间设置可在**源代码->.NET 命名策略**部分。

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>警告的操作：永远不会使用 Foo 的私有方法。 (CS0169)

接口生成器文件的操作被连接到小组件通过在运行时，反射，因此预期此警告。

可以使用"#pragma 警告禁用 0169年""#pragma 警告启用 0169" 围绕你的操作如果你想要取消显示此警告只需为这些方法，或如果你想要禁用其整个项目 （非添加 0169 编译器选项中的"忽略警告"字段建议）。

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch 失败，出现以下消息：无法打开程序集 ' / path/to/yourproject.exe

如果看到此错误消息，通常问题是你的项目的绝对路径包含空格。 这将修复的 Xamarin.iOS，未来版本中，但可以通过将项目移到不含空格的文件夹来解决此问题。

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>Sqlite3 版本太旧，-请至少升级到 v3.5.0 ！

执行以下所有操作时，将发生这种情况：

1.  使用 Mono.Data.Sqlite
1.  使用 Mac OS X Leopard (10.5)
1.  运行你的应用在模拟器中。


问题是 Mono 选取 OS X `libsqlite3.dylib`，不 iPhoneSimulator 的`libsqlite3.dylib`文件。 您的应用程序*将*设备，但只是无法在模拟器上工作。

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>将部署到设备失败，并 System.Exception:AMDeviceInstallApplication returned 3892346901

此错误意味着你的证书/捆绑包 id 的代码签名配置与在设备上安装了预配配置文件不匹配。  确认您有适当的证书在项目选项中选择-> iPhone 捆绑包签名，并且项目选项中指定的正确程序包 id-> iPhone 应用程序

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>代码完成功能不工作在 Visual Studio for Mac

请确保使用最新版本的 Visual Studio for Mac 和 Xamarin.iOS

如果问题仍然存在，请[提交 bug](http://monodevelop.com/Developers#Reporting_Bugs)、 附加 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{TIMESTAMP}.log**，并**组件的 {TIMESTAMP}.log**日志文件。

如果所有其他方法均失败，您可以尝试删除的代码完成缓存，以便重新生成它：

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

请注意正确键入以下命令，或可能会意外地删除重要的文件。

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>Visual Studio for Mac 崩溃时复制文本

流行的 Mac 实用工具 QuickSilver、 Google 工具栏和启动具有用于 Mac 的内存损坏 Visual Studio 的剪贴板功能。 在其选项中，您可以作为它们应不会干扰的进程，Visual Studio for Mac 列出。

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio for Mac 错误报告所需的 Mono 2.4

如果你由于最近的更新，更新 Visual Studio for Mac，当你尝试启动它时再次它抱怨 Mono 2.4 不可存在，只需是[升级 Mono 2.4 安装](http://www.go-mono.com/mono-downloads/download.html)。  

Mono 2.4.2.3_6 修复了阻止可靠地运行，有时会挂起 Visual Studio for Mac 在启动 Visual Studio for Mac 或阻止正在生成代码完成数据库的一些重要问题。

一旦安装新的 Mono，请按预期方式将启动 Visual Studio for Mac。

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>在断言.../../../../mono/metadata/generic-sharing.c:704，oti 不满足条件

如果收到以下的堆栈跟踪：

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

这意味着要链接到你的项目使用 thumb 代码编译的静态库。 从 iPhone SDK 版本 3.1 （或更高版本在撰写本文时） 开始链接非 Thumb 代码 (Xamarin.iOS) 和 Thumb 代码 （静态库） 时，Apple 引入了其链接器中的 bug。你将需要与静态库来缓解此问题的非 Thumb 版本链接。

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException:尝试 JIT 编译方法 （托管到托管的包装） Foo[]:System.Collections.Generic.ICollection'1.get_Count （）

[] 后缀指示您或类库正在通过泛型集合，如 ienumerable<>、 icollection<> 或 IList <> 数组上调用的方法。 解决此问题，可以显式强制 AOT 编译器包含此类方法通过调用方法，并确保触发了异常的调用之前执行此代码。 在这种情况下，您可以编写：

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

这将强制 AOT 编译器包含 get_Count 方法。

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio for Mac 源编辑器是速度极慢

有时 Visual Studio for Mac 源编辑器变得非常缓慢，使其不显示为挂起之间键入字符数秒钟的时间。

此问题是非常少见，非常难以重现的-它通常无法重现同一台计算机上后启 Visual Studio for mac。 因此我们将不胜感激如果可以在重新启动 Visual Studio for Mac 中之前, 执行几个调试步骤，并将结果发送给我们。

1.  请尝试关闭编辑器选项卡上，并重新打开它。 需要少量编辑或四处移动插入符号，直到再次发生缓慢？
1.  禁用"则不同同步"使用"Quartz Debug"开发人员工具 (可找到使用 Spotlight)，并检查是否能够源编辑器性能恢复正常状态。
1.  使用电子束以同步仍处于禁用状态，请尝试重复步骤 (1)。
1.  如果在编辑器挂起超过几秒钟，尝试运行"killall-退出 [Visual Studio for Mac]"在终端中而被挂起。 它可能很难时间 kill 命令发生这种情况时编辑器已经被挂起，但它至关重要，若要执行此操作，因为命令会强制 Mono 要写入到 MD 日志，我们可以使用发现时 XS 挂起线程都在何种状态的所有线程的堆栈跟踪。



请附加 XS 日志 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{TIMESTAMP}.log**，和**组件-{TIMESTAMP}.log**(在较旧版本的 XS/MonoDevelop，只需发送 **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**)。

 **注意：中 XS 2.2 最终修复上述问题**

## <a name="compiled-application-is-very-large"></a>编译的应用程序是非常大

若要支持调试，调试版本包含额外的代码。 在发布模式下生成的项目是大小的一小部分。

截至 Xamarin.iOS 1.3 调试生成包含调试 Mono （每个类的框架中每个方法） 的每个单个组件的支持。  

我们将使用 Xamarin.iOS 1.4 引入用于调试的更加精细的方法，默认行为是仅针对你的代码和库，库可以提供调试检测，并且不执行此操作的所有[Mono 程序集](~/cross-platform/internals/available-assemblies.md)（这仍将可能的但必须要选择加入到调试这些程序集）。

## <a name="installation-hangs"></a>安装挂起

如果您有运行的 iPhone 模拟器的 Mono 和 Xamarin.iOS 安装程序挂起。 此问题并不局限于 Mono 或 Xamarin.iOS，这是任何软件的尝试安装软件上 MacOS Snow Leopard，如果在 iPhone 模拟器运行在安装时出现相同的问题。

请确保你退出在 iPhone 模拟器，然后重试安装。

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>用尽了 trampolines 类型 0

如果运行设备时收到此消息，您可以创建多个类型 0 trampolines （键入特定于） 通过修改您的项目选项"iPhone 生成"部分。  你想要添加的设备的额外参数生成目标：

 `-aot "ntrampolines=2048"`

默认数 trampolines 为 1024年。  请尝试增大此数字，直到具有足够的应用程序。

## <a name="ran-out-of-trampolines-of-type-1"></a>用尽了 trampolines 类型 1

如果进行大量使用递归泛型的您可能会在设备上收到此消息。  通过修改您的项目选项"iPhone 生成"部分中，创建多个类型 1 trampolines （类型为 RGCTX）。  你想要添加的设备的额外参数生成目标：

 `-aot "nrgctx-trampolines=2048"`

默认数 trampolines 为 1024年。  请尝试增大此数字，直到具有足够的有关泛型的使用情况。

## <a name="ran-out-of-trampolines-of-type-2"></a>用尽了 trampolines 类型 2

如果进行频繁使用接口，您可能会收到此消息在设备上。
通过修改您的项目选项"iPhone 生成"部分中，创建多个类型 2 trampolines （类型为 IMT Thunk）。  你想要添加的设备的额外参数生成目标：

 `-aot "nimt-trampolines=512"`

默认的 IMT Thunk trampolines 数为 128。  请尝试增大此数字，直到您有足够的接口的使用情况。

## <a name="debugger-is-unable-to-connect-with-the-device"></a>调试器不能与设备连接

当开始调试设备配置时，你将看到调试器显示一个对话框，指示它尝试连接到该应用程序。 有多个原因，调试器可能无法连接到该应用程序，具体取决于模式因为使用连接 （USB 或 WiFi）。

 **如果设备和调试器主机位于不同的网络**，防火墙或专用网络可能会阻止连接到 WiFi 模式中的调试器主机提供的应用程序。

 **Visual Studio for Mac 可能不能查询主机的正确 IP**。 在 WiFi 模式 Visual Studio for Mac，给出了应用程序所有 Ip，它可以找到的主机和应用程序尝试所有可以了解是否它可以使用其中任何一个连接到 Visual Studio for mac。

 **另一台设备连接到主机上的 USB 端口中。** 在少数情况下，其他设备连接到 USB 端口在主机上的具有已知要以某种方式会影响在 USB 模式中进行调试。

如果 WiFi 或 USB 模式不起作用，您可以轻松地尝试其他： 在 Visual Studio for Mac 中，打开首选项，请转到首选项 / 调试器 / iPhone 调试程序页中，并切换"iOS 设备通过 WiFi 调试而不是通过 USB"复选框。   如果都不适用，可以看到设备控制台以详细模式中故障的详细信息 (其中通过添加启用了"-v-v-v"到项目的选项中的其他 mtouch 参数)。

## <a name="error-134-mtouch-failed-with-the-following-message"></a>错误 134: mtouch 失败，出现以下消息：

如果你尝试使用-nolink 生成上的版本的 Xamarin.iOS 1.4 样式，可能会引发此错误。 可以通过在 monodevelop 项目配置中指定额外参数来解决此错误。

添加参数

 `-nosymbolstrip`

和问题应该会得到解决。

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>分发标识不显示在 Visual Studio for Mac 签名选项

Visual Studio for Mac 2.2 具有导致其无法检测包含逗号的分发证书的 bug。 请更新 Visual Studio for Mac 2.2.1。

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>错误"AFCFileRefWrite 返回：1"上传期间

将应用上传到你的设备时可能会收到错误"AFCFileRefWrite 返回：1". 这可能是如果您有一个零长度文件。

## <a name="error-mtouch-failed-with-no-output"></a>错误"mtouch 失败且无输出"

Xamarin.iOS 和 Visual Studio for Mac 的当前版本失败时的项目名称或存储解决方案或项目的目录包含空格。
修复此问题的方法：


-  请确保你的项目或存储位置的目录都不包含空格。
-  在项目"Main 设置"请确保项目名称不包含任何空格。

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>错误"已上传二进制文件无效。 使用预发布 beta 版本的 SDK 生成应用程序"

此错误通常由于与 Xamarin.iOS 2.0.0 发布前在 iPad 开发过程中启动项目，如你的 Info.plist 中可能有一些键：

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

应删除此密钥对，因为 Visual Studio for Mac 它为您自动处理。

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>错误"预发布 beta 版本的 SDK 用于生成应用程序"

（由 Ed Anuff 提供）

请执行以下步骤：

-  在 iPhone 生成到 3.2 或 iTunes 的 SDK 版本连接的更改将拒绝此上传由于其查看使用 SDK 版本不会早于 3.2 构建的 iPad 兼容应用程序
-  创建自定义项目的 Info.plist 并显式设置为在其中 3.0 MinimumOSVersion。   这将重写设置 Xamarin.iOS 的 MinimumOSVersion 3.2 值。   如果不这样做，应用程序不能在 iPhone 上运行。
-  重新生成、 zip 和上的传到 iTunes connect。

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>未经处理的异常：“System.Exception:找不到选择器 someSelector： 在 {type}

此异常是由以下三种情况引起的：


1.  而无需将相应的 [导出] 特性应用于一种方法提供了到 OBJECTIVE-C 运行时的选择器
1.  已启用完整链接并不应用于 [导出] ed 方法的 [Preserve] 属性。
1.  已应用于继承的类型中的私有方法的 [Export] 特性。


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>MainWindow.xib.designer.cs 文件不会更新

在 Xamarin Studio 2.4 导致其无法组与新的项目中的 MainWindow.xib.designer 文件 MainWindow.xib 文件出现 bug。 这意味着它不会更新该特定文件的设计器代码。

此问题固定的版本的 Visual Studio for Mac 现已推出其内置的更新程序，因此请确保使用较新版本。

您可以通过删除 （不删除） 来解决现有项目 xib 和其设计器文件，然后将其添加回。 此重新应正确组文件。

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>正在创建后消失 UIAlertView 或 UIActionSheet

如果你有如下一些代码：

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

在"actionSheet"对象存在为该函数中的临时变量和函数将终止，对象就可以进行垃圾回收，因此其结果是进行垃圾回收。

若要解决此问题，需要保留对"actionSheet"你的方法，某个位置之外，将 live 超出您的方法的引用。

## <a name="project-always-runs-in-the-ipad-simulator"></a>项目始终运行在 iPad 模拟器

IPhone SDK 4.0 安装程序安装 2 Sdk-3.2 SDK，用于构建仅限 iPad 的应用程序，并为 4.0 SDK，用于生成 iPhone 和通用应用。 它还会安装模拟仅 iPad，3.2 模拟器以及 4.0 的模拟器，模拟 iPhone 或 iPhone 4。 删除所有旧的 Sdk 和模拟器。

Visual Studio for Mac iPhone 项目生成选项中构建您的应用程序包含将使用的 SDK 版本的设置。 此设置可在**项目选项-> 生成-> iPhone 生成**。

Visual Studio for Mac 中的新项目作为其默认 SDK 设置，使用最早的已安装的 SDK 和 Visual Studio for Mac 如果指定的 SDK 不存在，将使用它可以找到以生成您的应用程序的最接近。 执行此操作，以便项目始终就不需要最新的 SDK。 但是，这当前会导致 3.2 SDK 正在使用-这会导致正在使用 iPad 模拟器。

若要解决此问题通过使用为 4.0 SDK，请转到**项目选项-> 生成-> iPhone 生成**> 并将 SDK 值更改为"4.0"，然后使用下拉列表框。 必须为每个配置和平台组合，访问面板的顶部使用下拉列表来执行此操作。

不应与"的最低操作系统版本"设置混淆的 SDK 版本。
此值不会不一定要匹配的 SDK 版本值-它会影响您的应用程序将安装上，可以是早于 SDK，前提是使用仅存在于较旧操作系统，或防止使用运行时操作系统版本检查的较新功能的使用的 Api 的操作系统的最低版本ks。 应将其设置为在其测试您的应用程序的最早 OS 版本。

另请注意**项目-> iPhone 模拟器目标**> 菜单可用于选择运行/调试项目时，默认情况下使用模拟器。 此外，**运行-> 运行与**> 菜单可用于选择用来运行特定模拟器

## <a name="ibtool-returns-error-133"></a>ibtool 将返回错误 133

这意味着已安装的 XCode 4。   在 XCode 4 中，工具 ibtool 已删除，不再可以编辑 XIB 文件使用独立工具。

如果你想要使用 Interface Builder，安装[XCode 系列 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792)，可通过 Apple 的网站。

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>"无法创建 mime 类型的显示绑定： application/vnd.apple-<wbr/>接口生成器"

如果你尝试从非 iPhone 项目创建 iPhone UI，会发生此错误。 请确保使用 iPhone/iPad 解决方案开始，不能只是将 iPhone UI 元素添加到 iPhone/iPad 项目。

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>在 iOS 模拟器内执行时启动故障

如果看到如下所示的堆栈跟踪以及在模拟器中运行时崩溃 (SIGSEGV):

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
...则可能在模拟器应用程序目录中有一个 （或多个） 过时的程序集。 由于 Apple iOS 模拟器中添加并更新文件，但永远不会将其删除，则可能存在这样的程序集。 如果发生这种情况则最简单的解决方案是从模拟器菜单中选择"重置和内容和设置..."。   

**警告：** 这将从模拟器删除所有文件、 应用程序和数据。   下一次执行应用程序时，Visual Studio for Mac 会将其部署到模拟器，并且将会导致故障发生任何旧的、 陈旧程序集。

## <a name="simulator-hangs-during-application-installation"></a>在应用程序安装过程中的模拟器挂起

这可能会在应用程序名称中包含。（句点） 在它们的名称。
即使可以中 （如设备） 的许多其他情况下的工作原理，这不能用作 CFBundleExecutable-中的可执行文件名称。

 *"值不应包含任何扩展名称。"- [https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>错误："不支持自定义特性类型 0x43"时双击.xib 文件

原因是尝试打开.xib 文件时未正确设置环境变量。 这应不会使用正常使用的 Visual Studio 针对 Mac/Xamarin.iOS，并重新打开 Visual Studio for Mac 进行/应用程序 — 应修复的问题。

尝试更新软件和此错误消息出现后，请电子邮件 *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>应用程序在模拟器上运行，但无法在设备上

此问题可能表现在多个窗体中，并且不会始终生成一致的错误。 如果应用程序包含.xib，检查以确保**生成操作**.xib 设置为**接口定义**。 这是.xibs 的默认生成操作。

若要检查的生成操作，请右键单击.xib 文件，然后选择**生成操作**。


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException:没有数据是可用于编码 437

当 Xamarin.iOS 应用程序中包括第三方库，你可能会遇到错误，在窗体"System.NotSupportedException:没有数据是可用于编码 437"尝试编译和运行应用时。 例如，库，如`Ionic.Zip.ZipFile`，可能会引发此异常在操作过程。

这可以通过打开 Xamarin.iOS 项目中，转到用于解决**iOS 生成** > **国际化**并检查**西部**国际化。
