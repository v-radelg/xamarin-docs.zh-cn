---
title: Xamarin.Android 错误矩阵
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: f3721ad661f4b817375b0d625c9b5cc293e6d44c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945300"
---
# <a name="xamarinandroid-errors-matrix"></a>Xamarin.Android 错误矩阵

## <a name="errors-reference"></a>错误参考

本文档提供有关从 Xamarin 上的各个错误代码的一些信息。

|类别|描述|
|--- |--- |
|XA0xxx|mandroid 错误|
|XA1xxx|文件复制 / 符号链接 （相关项目） 错误|
|XA2xxx|链接器错误|
|XA3xxx|AOT 错误|
|XA4xxx|代码生成错误|
|XA5xxx|Gcc 高级版和工具链错误|
|XA6xxx|mandroid 内部工具错误|
|XA7xxx|保留|
|XA8xxx|保留|
|XA9xxx|许可错误|


## <a name="error-codes"></a>错误代码

### <a name="xa0xxx-errors"></a>XA0xxx 错误

|错误代码|描述|
|--- |--- |
|XA0000|意外的错误-请填写[bug 报表](https://github.com/xamarin/xamarin-android/issues/new)。|
|XA0001|-devname 提供不需要任何特定于设备的操作。|
|XA0002|无法分析环境变量{0}。|
|XA0003|应用程序名称{0}.exe 与 SDK 或产品程序集 (.dll) 名称冲突。|
|XA0004|新 refcounting 逻辑要求 sgen 太启用。|
|XA0005|输出目录{0}不存在。|
|XA0006|不没有在任何开发平台{0}，使用--平台 = 来指定 SDK 的平台|
|XA0007|根程序集{0}不存在。|
|XA0008|应提供一个根只有程序集。|
|XA0009|加载程序集时出错： {0}。|
|XA0010|无法分析命令行参数： {0}。|
|XA0011|{0} 生成时所针对的较新的运行时 ({1}) 不是 MonoTouch 支持。|
|XA0012|不完整的数据用于完成{0}。|
|XA0013|分析支持需要 sgen 太启用。|
|XA0014|iOS{0}不支持面向 ARMv6 的构建应用程序。|
|XA0020|无法确定 mandroid 路径。|
|XA0100|EmbeddedNativeLibrary{0}是无效的 Android 应用程序项目中。 请改为使用 AndroidNativeLibrary。|

### <a name="xa1xxx-errors"></a>XA1xxx 错误

|错误代码|描述|
|--- |--- |
|XA1001|在指定目录中找不到应用程序。|
|XA1002|无法创建符号链接，复制文件。|
|XA1003|无法终止该应用程序{0}。 您可能需要手动终止应用程序。|
|XA1004|无法获取安装的应用程序的列表。|
|XA1005|无法终止该应用程序{0}上的设备{1}: {2}。 您可能需要手动终止应用程序。|
|XA1006|无法安装该应用程序{0}上的设备{1}: {2}。|
|XA1007|未能启动应用程序{0}上的设备{1}: {2}。 您仍可以通过点击它手动启动该应用程序。|
|XA1008|无法启动模拟器： {0}。|
|XA1009|无法将复制程序集 '{0}到{1}: {2}。|
|XA1010|无法加载程序集 '{0}': {1}。|
|XA1011|无法将添加缺少的资源文件:{0}。|
|XA1101|无法启动应用。|
|XA1102|未能附加到应用程序 （若要终止它）： {0}。|
|XA1103|无法分离。|
|XA1104|发送数据包失败： {0}。|
|XA1105|意外的响应类型。|
|XA1106|无法在设备上获取应用程序的列表：请求超时。|
|XA1107|应用程序启动失败。|
|XA1201|无法加载模拟器： {0}。|
|XA1301|本机库 '{0}({1}) 已被忽略，因为它不匹配当前生成 architecture(s) ({2})。|

### <a name="xa2xxx-errors"></a>XA2xxx 错误

|错误代码|描述|
|--- |--- |
|XA2001|无法链接程序集。|
|XA2002|无法解析引用： {0}。|
|XA2003|选项{0}由于禁用了链接都将被忽略。|
|XA2004|额外的链接器定义文件{0}找不到。|
|XA2005|定义从{0}无法分析。|
|XA2006|对元数据项目引用{0}(在中定义{1}) 从{2}无法解析。|

### <a name="xa3xxx-errors"></a>XA3xxx 错误

这些是 AOT 错误。

|错误代码|描述|
|--- |--- |
|XA3001|可以不 AOT 的程序集{0}。|
|XA3002|AOT 限制：方法{0}必须是静态的因为它用 [MonoPInvokeCallback] 修饰。|
|XA3003|冲突-调试和-llvm 选项。 软调试被禁用。|


### <a name="xa4xxx-errors"></a>XA4xxx 错误

这些是代码生成错误。

|错误代码|描述|
|--- |--- |
|XA4001|主模板中找不到 expansed{0}。|
|XA4101|在注册机构无法生成类型的签名{0}。|
|XA4102|在注册机构找到无效的类型{0}中方法的签名{2}。 使用{1}相反。|
|XA4103|在注册机构找到无效的类型{0}中方法的签名{2}:该类型实现 INativeObject，但没有采用两个构造函数 (IntPtr，bool) 参数。|
|XA4104|在注册机构不能封送类型的返回值{0}中方法的签名{1}。|
|XA4105|在注册机构不能封送的类型参数{0}中方法的签名{1}。|
|XA4106|在注册机构不能封送结构的返回值{0}中方法的签名{1}。|
|XA4107|在注册机构不能封送的类型参数{0}中方法的签名{1}。|
|XA4108|在注册机构不能获取托管类型的 ObjectiveC 类型{0}。|
|XA4109|未能编译生成的注册机构代码。 请提出[bug 报表](http://bugzilla.xamarin.com)。|
|XA4110|在注册机构不能封送类型的输出参数{0}中方法的签名{1}。|
|XA4111|在注册机构不能生成的签名类型{0}中方法'{1}。|
|XA4200|对于 claas 类型只能生成 ACW 的。|
|XA4201|无法确定类型的 JNI 名称{0}。|
|XA4203|指定的类型名称必须是完全限定的。|
|XA4204|无法解析接口类型{0}。 是否缺少程序集引用?|
|XA4205|[ExportField] 只能在不带参数的方法。|
|XA4206|不能在泛型类型上使用 [导出]。|
|XA4207|不能在泛型类型上使用 [ExportField]。|
|XA4208|[Java.Interop.ExportFieldAttribute] 不能返回 void 的方法上使用。|
|XA4209|未能创建类 JavaTypeInfo:{0}由于{1}。|
|XA4210|需要使用 ExportAttribute 或 ExportFieldAttribute 时将添加对 Mono.Android.Export.dll 的引用。|
|XA4211|AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}is $(TargetFrameworkVersion) 小于{1}。 使用 API-{1} ACW 编译。|


### <a name="xa5xxx-errors"></a>XA5xxx 错误

|错误代码|描述|
|--- |--- |
|XA5101|缺少{0}编译器。 请安装 Android NDK。|
|XA5102|无法从程序集为本机代码的转换。 请提出[bug 报表](http://bugzilla.xamarin.com)。|
|XA5103|未能将文件编译{0}。 请提出[bug 报表](http://bugzilla.xamarin.com)。|
|XA5201|本机链接失败。 请查看提供给 gcc 高级版的用户标志： {0}|
|XA5202|本机链接失败。 请查看生成日志。|
|XA5303|本机链接警告： {0}。|
|XA5300|Android SDK 找不到或未完全安装。|
|XA5301|缺少去除工具。 请安装 Xcode 命令行工具组件。|
|XA5302|缺少 dsymutil 工具。 请安装 Xcode 命令行工具组件。|
|XA5203|未能生成调试符号 （dSYM 目录）。 请查看生成日志。|
|XA5204|无法将最小化最终二进制文件。 请查看生成日志。|
|XA5205|缺少 aapt 工具。 请安装 Android SDK 生成工具包。|
|XA5206|{0}。 Android 资源目录{1}不存在。|
|XA5207|{0}。 Java 库文件{1}不存在。|
|XA5208|下载失败。 请下载{0}并将其放{1}目录。|
|XA5209|解压缩失败。 请下载{0}并将其解压缩到{1}目录。|
|XA5210|{0}。 本机库文件{1}不存在。|

### <a name="xa6xxx-errors"></a>XA6xxx 错误

|错误代码|描述|
|--- |--- |
|XA6001|Cecil 的正在运行的版本不支持最小化的程序集。|
|XA6002|不删除程序集{0}。|
|XA6003|UnauthorizedAccessException 消息。|

### <a name="xa9xxx-errors"></a>XA9xxx 错误

这些错误代码是许可和激活错误。


#### <a name="xa9000"></a>XA9000

 **原因：** 许可证已过期

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|警告|警告|警告|警告|警告|


#### <a name="xa9001"></a>XA9001

 **原因：** 试用版已过期

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|警告|警告|警告|警告|警告|



#### <a name="xa9002"></a>XA9002

 **原因：** AndroidJavaSource

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|确定|确定|确定|

 **原因：** AndroidJavaLibrary

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|确定|确定|确定|

 **如果绑定程序集具有嵌入.jar，这是捕获在包时，不生成时间。**

 **原因：** AndroidNativeLibrary

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|确定|确定|确定|


#### <a name="xa9003"></a>XA9003

 **原因：** System.Runtime.Serialization

 **检查期间：** package

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|

 **原因：** System.ServiceModel.Web

 **检查期间：** package

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|

 **原因：** Mono.Data.Tds

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|

 **这被引用 System.Data.dll，这允许的**

 **原因：** Mono.Android.Export

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|确定|确定|确定|



#### <a name="xa9004"></a>XA9004

 **原因：** -分析

 **检查期间：** package

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|



#### <a name="xa9005"></a>XA9005

 **原因：** 大小限制 (32 kb)。

 **检查期间：** package

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|确定|-|-|-|



#### <a name="xa9006"></a>XA9006

 **原因：** System.Data.SqlClient 命名空间。

 **检查期间：** package

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|


#### <a name="xa9008"></a>XA9008

 **原因：** 从命令行生成。

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|确定|确定|确定|


#### <a name="xa9009"></a>XA9009

 **原因：** 缺少的序列号。

 **检查期间：** 分钟

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9010"></a>XA9010

 **原因：** 无效的 ProductId。

 **检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|

等效于 XA9018。



#### <a name="xa9011"></a>XA9011

 **原因：** 未能更新许可证文件 （到新的文件格式）。

 **检查期间：** 激活

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|

#### <a name="xa9012"></a>XA9012

 **原因：** 无 internet

 **检查期间：** 激活

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9013"></a>XA9013

 **原因：** 未知错误

 **检查期间：** 激活

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9014"></a>XA9014

 **原因：** 无效的激活代码

 **检查期间：** 激活

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9017"></a>XA9017

 **原因：** 激活服务器不会返回有效的许可证。

 **检查期间：** 激活

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|错误|错误|错误|错误|错误|


#### <a name="xa9018"></a>XA9018

**原因：** 许可证无效

**检查期间：** Build

|Starter|独立|Business(Trial)|业务|企业|
|--- |--- |--- |--- |--- |
|-|-|错误|-|-|

