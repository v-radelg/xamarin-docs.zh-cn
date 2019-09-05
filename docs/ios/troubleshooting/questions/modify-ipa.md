---
title: 在 Visual Studio 中生成文件后，是否可以在 IPA 文件中添加或删除文件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: d1546b83304d8c66f7433bd77c5ebecc9dc95aaa
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278472"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>在 Visual Studio 中生成文件后，是否可以在 IPA 文件中添加或删除文件？

是的，但这种情况通常会要求你在进行更改后对`.app`包进行重新签名。

请注意，在`.ipa`正常使用时不需要修改文件。 本文仅用于提供信息。

## <a name="example-removing-a-file-from-a-ipa-archive"></a>示例：从`.ipa`存档中删除文件

对于此示例，假设 Xamarin iOS 项目的名称为`iPhoneApp1` `generated session id` ，并且为`cc530d20d6b19da63f6f1c6f67a0a254`

1. 从 Visual Studio 正常生成文件。`.ipa`

2. 切换到 Mac 生成主机。

3. 在`~/Library/Caches/Xamarin/mtbs/builds`文件夹中找到生成。 可以将此路径粘贴到**查找器 > 转 > 转到文件夹**，浏览查找器中的文件夹。 查找与项目名称匹配的文件夹。 在该文件夹中，查找与生成的匹配`generated session id`的文件夹。 这很可能是具有最近修改时间的子文件夹。

4. 打开新`Terminal.app`窗口。

5. 在`cd` "应用程序" 窗口中键入，然后将`generated session id`文件夹 & 拖动到`Terminal.app`窗口中：

    ![](modify-ipa-images/session-id-folder.png "在查找器中查找生成的会话 id 文件夹")

6. 键入 "返回密钥"，将目录更改`generated session id`为该文件夹。

7. 使用以下命令将`old/` 文件解压缩到临时文件夹中。`.ipa` 根据特定`Ad-Hoc`项目`iPhoneApp1`的需要调整和名称。

    > ditto-xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. ipa 旧/

8. `Terminal.app`使窗口保持打开状态。

9. 从中`.ipa`删除所需的文件。 您可以使用 Finder 将它们移动到垃圾桶，或使用`Terminal.app`在命令行上删除它们。 若要在查找器中`Payload/iPhone`查看文件的内容，请单击该文件并选择 "**显示包内容**"。

10. 使用与步骤3中相同的常规方法，查找下面`~/Library/Logs/Xamarin/MonoTouchVS/`的日志文件，该日志文件的项目名称`generated session id`和名称均为：![](modify-ipa-images/build-log.png "在查找器中找到项目生成日志")

11. 从步骤10打开生成日志，例如双击它。

12. 查找包含`tool /usr/bin/codesign execution started with arguments: -v --force --sign`的行。

13. 键入`/usr/bin/codesign`步骤8中的 "Terminal. app" 窗口。

14. 从步骤12的行中复制`-v`所有以开头的自变量，并将其粘贴到 "应用程序" 窗口中。

15. 将最后一个参数更改为位于`.app` `old/Payload/`文件夹中的捆绑，然后运行命令。

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. 更改为终端`old/`中的目录：

    ```bash
    cd old
    ```

17. 使用命令将目录的内容压缩到新`.ipa`文件中。 `zip` 你可以更改`"$HOME/Desktop/iPhoneApp1-1.0.ipa"`参数，以将文件`.ipa`输出到你想要的位置：

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>常见错误消息

如果出现`Invalid Signature. A sealed resource is missing or invalid.`这种情况，这通常意味着在`.app`捆绑中更改了某些内容，并且`.app`之后未正确重新签署该捆绑包。 另请注意，如果想要创建`.ipa`具有分发配置文件的，则_必须_使用分发配置`.ipa`文件生成原始文件。 否则， `Entitlements.xcent`将不正确。

若要提供有关此错误发生方式的具体示例，如果在步骤9后`codesign --verify`在终端窗口中运行以下命令，你将看到错误以及错误的确切原因：

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

应用商店验证过程将报告类似的错误消息：

> 错误 ITMS-90035："签名无效。 密封的资源丢失或无效。 位于路径 [iPhoneApp1/iPhoneApp1] 的二进制文件包含无效的签名。 请确保已使用分发证书（而不是即席证书或开发证书）对应用程序进行签名。 验证 Xcode 中的代码签名设置是否在目标级别（重写项目级别的任何值）正确无误。 此外，请确保你要上传的绑定是使用 Xcode 中的 Release 目标生成的，而不是在模拟器目标中生成的。 如果你确定代码签名设置是正确的，请在 Xcode 中选择 "全部清除"，删除查找器中的 "生成" 目录，并重新生成发布目标。 有关详细信息，请参阅[https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)
