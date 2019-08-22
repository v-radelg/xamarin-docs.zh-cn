---
title: Xamarin.iOS 应用中的 iTunesMetadata.plist 文件
description: 本文介绍了 iTunesMetadata.plist 文件，该文件用于向 iTunes 提供 iOS 应用程序使用 Ad Hoc 分发进行测试或企业部署的相关信息。
ms.prod: xamarin
ms.assetid: 70676eba-6a99-4a3a-bccc-84359fe9c2c3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f632b44e087c0e380ecd81c2af7db3d49c072f7e
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887748"
---
# <a name="the-itunesmetadataplist-file-in-xamarinios-apps"></a>Xamarin.iOS 应用中的 iTunesMetadata.plist 文件

本文介绍了 iTunesMetadata.plist 文件，该文件用于向 iTunes 提供 iOS 应用程序使用 Ad Hoc 分发进行测试或企业部署的相关信息。 

在 iTunes Connect 中创建 iOS 应用程序（无论是用于在 iTunes App Store 中销售还是免费发布）时，开发人员可指定应用程序类型、子类型、版权声明、支持的 iOS 设备和必需设备功能等信息。 对于通过 Ad Hoc 分发传递给测试员或企业用户的 iOS 应用程序，缺少此信息。

若要向 Ad Hoc 分发提供缺少的信息，可选择创建可选的 `iTunesMetadata.plist` 文件，然后将其包含在应用程序 IPA 文件中。 此 plist 文件是特殊格式的 XML 文件（详情请参阅[属性列表编程指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html)），其中包含定义给定 iOS 应用程序相关信息的键/值对。

<a name="iTunesMetadata_contents" />

## <a name="the-itunesmetadataplist-contents"></a>iTunesMetadata.plist 内容

以下示例介绍了典型的 `iTunesMetadata.plist` 文件，该文件用于定义 Ad Hoc 分发所需的 iTunes 信息：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>UIRequiredDeviceCapabilities</key>
    <dict>
        <key>armv7</key>
        <true/>
        <key>front-facing-camera</key>
        <true/>
    </dict>
    <key>artistName</key>
    <string>Company, Inc.</string>
    <key>bundleDisplayName</key>
    <string>App Name</string>
    <key>bundleShortVersionString</key>
    <string>1.5.1</string>
    <key>bundleVersion</key>
    <string>1.5.1</string>
    <key>copyright</key>
    <string>© 2015 Company, Inc.</string>
    <key>drmVersionNumber</key>
    <integer>0</integer>
    <key>fileExtension</key>
    <string>.app</string>
    <key>gameCenterEnabled</key>
    <false/>
    <key>gameCenterEverEnabled</key>
    <false/>
    <key>genre</key>
    <string>Games</string>
    <key>genreId</key>
    <integer>6014</integer>
    <key>itemName</key>
    <string>App Name</string>
    <key>kind</key>
    <string>software</string>
    <key>playlistArtistName</key>
    <string>Company, Inc.</string>
    <key>playlistName</key>
    <string>App Name</string>
    <key>releaseDate</key>
    <string>2015-11-18T03:23:10Z</string>
    <key>s</key>
    <integer>143441</integer>
    <key>softwareIconNeedsShine</key>
    <false/>
    <key>softwareSupportedDeviceIds</key>
    <array>
        <integer>9</integer>
    </array>
    <key>softwareVersionBundleId</key>
    <string>com.company.appid</string>
    <key>subgenres</key>
    <array>
        <dict>
            <key>genre</key>
            <string>Puzzle</string>
            <key>genreId</key>
            <integer>7012</integer>
        </dict>
        <dict>
            <key>genre</key>
            <string>Word</string>
            <key>genreId</key>
            <integer>7019</integer>
        </dict>
    </array>
    <key>versionRestrictions</key>
    <integer>16843008</integer>
</dict>
</plist>

```

下面将详细介绍各个键的值。

### <a name="uirequireddevicecapabilities"></a>UIRequiredDeviceCapabilities

通过 `UIRequiredDeviceCapabilities` 键，iTunes 可了解 iOS 应用程序需要哪些设备特定的功能才可在给定的 iOS 设备上安装。 其提供作为功能 (`<key>...</key>`) 和各功能布尔值的字典 (`<dict>...</dict>`)。 如果某功能的值为 `true`，则必须显示该功能。 若为 `false`，则不可在设备上显示该功能。 例如:

```xml
<key>UIRequiredDeviceCapabilities</key>
<dict>
    <key>armv7</key>
    <true/>
    <key>front-facing-camera</key>
    <true/>
</dict>
```

指定 iOS 设备必须在支持 ARM7 指令集并拥有前置摄像头后才可安装该应用程序。 有关允许值的完整列表，请参阅 Apple 的 [UIRequiredDeviceCapabilities](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW3) 文档。

### <a name="artistname-and-playlistartistname"></a>artistName 和 playlistArtistName

`artistName` 和 `playlistArtistName` 键用于定义创建要在 iTunes 内显示的 iOS 应用程序的公司名称。 示例:

```xml
<key>artistName</key>
<string>Company, Inc.</string>
...
<key>playlistArtistName</key>
<string>Company, Inc.</string>
```

### <a name="bundledisplayname-itemname-and-playlistname"></a>bundleDisplayName、itemName 和 playlistName

`bundleDisplayName`、`itemName` 和 `playlistName` 键用于定义将在 iTunes 中显示的 iOS 应用程序的名称。 示例:

```xml
<key>bundleDisplayName</key>
<string>App Name</string>
...
<key>itemName</key>
<string>App Name</string>
...
<key>playlistName</key>
<string>App Name</string>
```

### <a name="bundleshortversionstring-and-bundleversion"></a>bundleShortVersionString 和 bundleVersion

`bundleShortVersionString` 和 `bundleVersion` 键用于定义将在 iTunes 中显示的 iOS 应用程序版本号。 示例:

```xml
<key>bundleShortVersionString</key>
<string>1.5.1</string>
<key>bundleVersion</key>
<string>1.5.1</string>
```

### <a name="softwareversionbundleid"></a>softwareVersionBundleId

`softwareVersionBundleId` 键用于指定 iOS 应用程序的程序包 ID。 示例:

```xml
<key>softwareVersionBundleId</key>
<string>com.company.appid</string>
```

### <a name="copyright"></a>copyright

`copyright` 键用于定义 iTunes 中显示的版权声明。 示例:

```xml
<key>copyright</key>
<string>© 2015 Company, Inc.</string>
```

### <a name="releasedate"></a>releaseDate

`releaseDate` 键用于提供要在 iTunes 中显示的 iOS 应用程序的发布日期。 示例:

```xml
<key>releaseDate</key>
<string>2015-11-18T03:23:10Z</string>
```

### <a name="softwareiconneedsshine"></a>softwareIconNeedsShine

`softwareIconNeedsShine` 键用于就 iOS 应用程序图标是否需要对 iOS 6（及先前版本）使用高亮突出显示  通知 iTunes。 示例:

```xml
<key>softwareIconNeedsShine</key>
<false/>
```

### <a name="gamecenterenabled-and-gamecentereverenabled"></a>gameCenterEnabled 和 gameCenterEverEnabled

`gameCenterEnabled` 和 `gameCenterEverEnabled` 键用于就此 iOS 应用程序是否支持 Apple Game Center 通知 iTunes 。 示例:

```xml
<key>gameCenterEnabled</key>
<false/>
<key>gameCenterEverEnabled</key>
<false/>
```

### <a name="genre-genreid-and-subgenres"></a>genre、genreId 和 subgenres

`genre` 和 `genreId` 键用于就此 iOS 应用程序属于哪种类型通知 iTunes。 示例:

```xml
<key>genre</key>
<string>Games</string>
<key>genreId</key>
<integer>6014</integer>
```

`subgenres` 键可选择性地用于进一步定义 iOS 应用程序的子类型（最多两个）。 示例:

```xml
<key>subgenres</key>
<array>
    <dict>
        <key>genre</key>
        <string>Puzzle</string>
        <key>genreId</key>
        <integer>7012</integer>
    </dict>
    <dict>
        <key>genre</key>
        <string>Word</string>
        <key>genreId</key>
        <integer>7019</integer>
    </dict>
</array>
```

对于 iOS 应用程序，Apple 当前定义以下的类型和类型 ID：

[!include[](~/ios/includes/table-appstore.md)]

有关详细信息，请参阅 Apple 的 [Genre IDs Appendix](http://www.apple.com/itunes/affiliates/resources/documentation/genre-mapping.html)（类型 ID 附录）文档。

### <a name="softwaresupporteddeviceids"></a>softwareSupportedDeviceIds

`softwareSupportedDeviceIds` 键用于就此 iOS 应用程序支持哪些 iOS 设备通知 iTunes。 示例:

```xml
<key>softwareSupportedDeviceIds</key>
<array>
    <integer>9</integer>
</array>
```

其中以下值可用：

- 1 – 经典 iPhone
- 2 – iPod Touch
- 4 – iPad
- 9 – 新型 iPhone

### <a name="standard-keys"></a>标准键

以下键内附在 iOS 应用程序的所有 `iTunesMetadata.plist` 文件中，且始终使用相同的值：

```xml
<key>drmVersionNumber</key>
<integer>0</integer>
<key>fileExtension</key>
<string>.app</string>
...
<key>kind</key>
<string>software</string>
...
<key>s</key>
<integer>143441</integer>
...
<key>versionRestrictions</key>
<integer>16843008</integer>
```

<a name="iTunesMetadata_creating" />

## <a name="creating-an-itunesmetadataplist-file"></a>创建 iTunesMetadata.plist 文件

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 在 Visual Studio for Mac 中使用 `iTunesMetadata.plist` 文件时，有两种选择：

- 使用 Visual Studio for Mac 的可视化 plist 编辑器创建和维护文件。
- 在纯文本编辑器中创建和维护文件。

 下面将详细介绍这两种选择。

### <a name="using-the-visual-plist-editor"></a>使用可视化 Plist 编辑器

请执行以下操作：

1. 在“解决方案资源管理器”  中，右键单击 Xamarin.iOS 项目文件，再依次选择“添加”   > “新文件...” 
2. 在“新文件”对话框中，依次选择“iOS”   > “属性列表”  ：

    ![](itunesmetadata-images/image01.png "选择 iOS 属性列表")
3. 对“名称”  输入 `iTunesMetadata`，然后单击“新建”  按钮。
4. 在“解决方案资源管理器”  中，双击打开 `iTunesMetadata.plist` 文件进行编辑：

    ![](itunesmetadata-images/image02.png "iTunesMetadata.plist 编辑器")
5. 单击绿色的“+”  新建条目，再输入“`UIRequiredDeviceCapabilities`”作为键名称：

    ![](itunesmetadata-images/image03.png "创建新条目并输入 UIRequiredDeviceCapabilities 作为键名称")
6. 单击“字符串”  值类型，再从弹出列表中选择“字典”  ：

    ![](itunesmetadata-images/image04.png "从弹出列表中选择字典")
7. 单击属性名左侧的折叠图标，以显示字典条目：

    ![](itunesmetadata-images/image05.png "显示字典条目")
8. 依次单击“添加新条目”  文本和绿色的“+”  ，向字典添加条目：

    ![](itunesmetadata-images/image06.png "向字典添加一个条目")
9. 输入“`armv7`”作为键名称，选择“布尔”  类型，再输入“Yes”  作为值：

    ![](itunesmetadata-images/image07.png "输入“armv7”作为键名称，选择“布尔”类型，并输入“Yes”作为值")
10. 重复上述步骤，直到已使用所有必需键/值对填充 `iTunesMetadata.plist` 文件（有关详细信息，请参阅上方的 [iTunesMetadata.plist 内容](#iTunesMetadata_contents)部分）。

11. 保存对 plist 文件的更改。

### <a name="using-a-plain-text-editor"></a>使用纯文本编辑器

请执行以下操作：

1. 在纯文本编辑器中，新建文本文件并将其命名为 `iTunesMetadata.plist`。
2. 复制上面的 [iTunesMetadata.plist 内容](#iTunesMetadata_contents)中的示例内容。
3. 将内容粘贴到文件并按需进行编辑。
4. 保存文件并返回 Visual Studio for Mac。
5. 在“解决方案资源管理器”  中，右键单击 Xamarin.iOS 项目文件，再依次选择“添加”   > “现有文件...”  。
6. 在“打开文件”对话框中，选择上面创建的 `iTunesMetadata.plist` 文件，然后单击“确定”  按钮。
7. 将此文件的“生成操作”  保留设置为“无”  。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

由于面向 Visual Studio 的 Xamarin 插件仅支持 `Info.plist` 和 `Entitlement.plist` 文件的可视化编辑器，因此，需要在标准文本编辑器中创建 `iTunesMetadata.plist` 文件并将其手动包含在 Xamarin.iOS 项目中。

请执行以下操作：

1. 在纯文本编辑器中，新建文本文件并将其命名为 `iTunesMetadata.plist`。
2. 复制上面的 [iTunesMetadata.plist 内容](#iTunesMetadata_contents)中的示例内容。
3. 将内容粘贴到文件并按需进行编辑。
4. 保存文件并返回 Visual Studio。
5. 在“解决方案资源管理器”  中，右键单击 Xamarin.iOS 项目文件，再依次选择“添加”   > “现有文件...”  。
6. 在“打开文件”对话框中，选择上面创建的 `iTunesMetadata.plist` 文件，然后单击“打开”  按钮。
7. 将此文件的“生成操作”  保留设置为“无”  。

-----

稍后，准备在 IDE 中生成 IPA 时，请选择此 `iTunesMetadata.plist` 文件。

## <a name="summary"></a>总结

本文介绍了 `iTunesMetadata.plist` 文件，该文件可用于就 Ad Hoc 分发的 iOS 应用程序通知 iTunes。 它还介绍了 plist 文件中的标准键，以及如何在 Visual Studio 和 Visual Studio for Mac 中创建和维护该文件。

## <a name="related-links"></a>相关链接

- [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [内部分发](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Ad Hoc 分发](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [IPA 支持](~/ios/deploy-test/app-distribution/ipa-support.md)
- [疑难解答](~/ios/deploy-test/troubleshooting.md)
