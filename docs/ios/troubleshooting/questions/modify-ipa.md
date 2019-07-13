---
title: 可以将文件添加到或 IPA 文件在 Visual Studio 生成后删除文件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 047ee06522d4b2c07937e0e1bd9985248a164f01
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865022"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>可以将文件添加到或 IPA 文件在 Visual Studio 生成后删除文件？

是的但它通常将需要重新签名`.app`捆绑包之后进行更改。

请注意该修改`.ipa`文件不需要在正常使用。 本文是仅提供信息之目的。

## <a name="example-removing-a-file-from-a-ipa-archive"></a>示例： 删除文件从`.ipa`存档

对于此示例假定，Xamarin.iOS 项目的名称是`iPhoneApp1`和`generated session id`是 `cc530d20d6b19da63f6f1c6f67a0a254`

1. 生成`.ipa`文件作为普通从 Visual Studio。

2. 切换到 Mac 生成主机。

3. 查找中的生成`~/Library/Caches/Xamarin/mtbs/builds`文件夹。 你可以粘贴到此路径**Finder > 转 > 转到文件夹**浏览在查找器中的文件夹。 查找与项目名称相匹配的文件夹。 在该文件夹中，查找匹配的文件夹`generated session id`生成。 这将很可能是具有最新的修改时间的子文件夹。

4. 打开一个新`Terminal.app`窗口。

5. 类型`cd`Terminal.app 窗口，然后拖放到`generated session id`文件夹复制到`Terminal.app`窗口：

    ![](modify-ipa-images/session-id-folder.png "在查找器中查找生成的会话 id 文件夹")

6. 键入 return 键，若要更改目录更改为`generated session id`文件夹。

7. 解压缩`.ipa`到一个临时文件`old/`文件夹使用以下命令。 调整`Ad-Hoc`和`iPhoneApp1`所需的特定项目的名称。

    > ditto -xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa old/

8. 保留`Terminal.app`窗口处于打开状态。

9. 删除从所需的文件`.ipa`。 您可以将它们移动到回收站使用查找程序，或使用命令行上将其删除`Terminal.app`。 若要查看的内容`Payload/iPhone`文件在查找程序中，单击该文件，然后选择**显示包内容**。

10. 使用相同的常规方法如下所示步骤 3 中，找到日志文件下的`~/Library/Logs/Xamarin/MonoTouchVS/`具有项目名称和`generated session id`名称中：![](modify-ipa-images/build-log.png "在查找器中找到项目生成日志")

11. 生成日志从步骤 10 中，例如双击打开它。

12. 找到的行，包括`tool /usr/bin/codesign execution started with arguments: -v --force --sign`。

13. 类型`/usr/bin/codesign`到步骤 8 中 Terminal.app 窗口。

14. 复制的所有参数开头`-v`中的行从步骤 12 中，并将其粘贴到 Terminal.app 窗口。

15. 更改最后一个参数是`.app`捆绑包位于`old/Payload/`文件夹，然后运行命令。

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. 将更改为`old/`目录在终端中：

    ```bash
    cd old
    ```

17. 压缩到一个新目录的内容`.ipa`文件中使用`zip`命令。 您可以更改`"$HOME/Desktop/iPhoneApp1-1.0.ipa"`自变量输出`.ipa`文件位置：

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>常见的错误消息

如果您看到`Invalid Signature. A sealed resource is missing or invalid.`，这通常意味着某些内容已更改内`.app`捆绑包，并且`.app`捆绑包未正确重新签名之后。 另请注意，如果你想要创建`.ipa`与分发配置文件，您_必须_生成原始`.ipa`与分发配置文件。 否则为`Entitlements.xcent`将不正确。

若要提供的方式可能出现此错误，具体的示例，如果运行以下`codesign --verify`命令在终端窗口中在步骤 9 之后，将看到该错误的确切原因以及错误：

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

和 App Store 验证过程将报告类似的错误消息：

> 错误 ITMS-90035:"签名无效。 密封的资源已丢失或无效。 路径 [iPhoneApp1.app/iPhoneApp1] 处的二进制文件包含无效的签名。 请确保您已签名应用程序的分发证书、 不是临时证书或开发证书。 验证在目标级别 （它们将替代在项目级别的任何值） 在 Xcode 中的代码签名设置正确。 此外，请确保在 Xcode 中，而不是模拟器目标使用发布目标生成要上载的绑定。 如果确定你的代码签名设置正确，选择"全部清除"在 Xcode 中，删除的"生成"目录中查找工具中，并重新生成发布目标。 有关详细信息，请查阅[ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
