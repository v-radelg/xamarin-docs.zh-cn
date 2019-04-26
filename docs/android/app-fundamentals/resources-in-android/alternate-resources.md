---
title: 备用资源
ms.prod: xamarin
ms.assetid: AE5A864E-192D-475E-C731-99249C2E7D9E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: e71e79b58d912ecb697576e92ae921a848f24f4c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013205"
---
# <a name="alternate-resources"></a>备用资源

备用资源是针对某个特定设备或如当前语言、 特定屏幕大小或像素密度的运行时配置这些资源。 如果 Android 可以匹配特定设备或配置默认的资源比更具体的资源，则将改为使用该资源。 如果找不到匹配的当前配置的备用资源，则将加载默认资源。 Android 决定如何将更详细地，在资源位置部分介绍应用程序将使用哪些资源

备用资源都组织为根据资源类型，就像默认资源资源文件夹中的子目录。 替代资源子目录的名称是在窗体中：_ResourceType_-_Qualifier_

*限定符*是用于标识特定设备配置的名称。
在名称每个短划线分隔可能有多个限定符。 例如，下面的屏幕截图显示了一个简单的项目具有用于区域设置、 屏幕密度、 屏幕大小和方向等的各种配置的备用资源：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![备用资源](alternate-resources-images/alternate-resources-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![备用资源](alternate-resources-images/alternate-resources-xs.png)
 
-----
 

将限定符添加到资源类型时，将应用以下规则：

1. 可能有多个限定符，与每个短划线分隔的限定符。

2. 也许只能指定一次限定符。

3. 限定符必须是它们在下表中显示的顺序。

有关参考下面列出了可能的限定符：

- **MCC 和 mnc 是否** &ndash; [移动国家/地区代码](https://en.wikipedia.org/wiki/List_of_mobile_country_codes)(MCC) 和 （可选）[移动电话网络代码](https://en.wikipedia.org/wiki/Mobile_Network_Code)（mnc 是否）。 SIM 卡将提供 MCC，而设备连接到的网络将提供 mnc。 虽然可以使用移动国家/地区代码的目标区域设置，但建议方法是使用下面指定的语言限定符。 有关示例，对德国，目标资源是限定符`mcc262`。 在美国，T 移动的目标资源限定符是`mcc310-mnc026`。
  有关移动国家/地区代码和移动网络代码的完整列表请参阅<http://mcc-mnc.com/>。

- **语言**&ndash;两个字母[ISO 639-1 语言代码](https://en.wikipedia.org/wiki/ISO_639-1)和后面可跟两个字母[ISO 3166 alpha 2 区域代码](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)。 
  如果提供了这两个限定符，则它们之间用`-r`。 例如，若目标讲法语的区域设置则的限定符`fr`使用。 若要面向加拿大法语区域设置中，`fr-rCA`会使用。 语言代码和区域代码的完整列表，请参阅[的表示形式的名称的语言代码](http://www.loc.gov/standards/iso639-2/php/English_list.php)并[国家/地区名称和代码元素](http://www.iso.org/iso/country_codes/iso_3166_code_lists/country_names_and_code_elements.htm)。

- **最小宽度**&ndash;指定应用程序旨在上执行的最小屏幕宽度。 中的更详细地介绍[对于不同的屏幕创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在 API 级别 13 (Android 3.2) 及更高版本可用。 例如，限定符`sw320dp`到目标设备的高度和宽度至少为使用 320dp。

- **可用宽度**&ndash;格式 w 中的屏幕的最小宽度*N*dp，其中*N*是无关的像素密度的宽度。
  根据用户旋转设备，可能会更改此值。 中的更详细地介绍[对于不同的屏幕创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在 API 级别 13 (Android 3.2) 及更高版本可用。 示例： 使用限定符 w720dp 面向的宽度设置为最低 720dp 的设备。

- **可用高度**&ndash;格式小时内屏幕的最小高度*N*dp，其中*N*是在分发点的高度。 根据用户旋转设备，可能会更改此值。 中的更详细地介绍[对于不同的屏幕创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  在 API 级别 13 (Android 3.2) 及更高版本可用。 例如，使用限定符 h720dp 面向具有的最低 720dp 高度的设备

- **屏幕大小**&ndash;此限定符是的则这些资源的屏幕大小的泛化。 中更详细地介绍了它[对于不同的屏幕创建资源](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)。 
  可能值为 `small`、`normal`、`large` 和 `xlarge`。 在 API 级别 9 (Android 2.3/Android 2.3.1/Android 2.3.2) 中添加

- **屏幕上方面**&ndash;基于纵横比，不屏幕方向。 长屏是更广。 添加在 API 级别 4 (Android 1.6)。 可能的值为 long 类型的值和 notlong。

- **屏幕方向**&ndash;纵向或横向屏幕方向。 这可能会在应用程序的生存期内更改。
  可能的值为 `port` 和 `land`。

- **停靠模式**&ndash;一辆汽车中的设备停靠或停靠在办公桌前。 添加在 API 级别 8 (Android 2.2.x)。 可能的值为 `car` 和 `desk`。

- **夜间模式**&ndash;是否运行该应用程序在夜间或在一天中。 这可能会更改应用程序的生命周期内并且旨在使开发人员有机会在夜间使用接口的较暗版本。 添加在 API 级别 8 (Android 2.2.x)。 可能的值为 `night` 和 `notnight`。

- **屏幕像素密度 (dpi)** &ndash;实际屏幕上的给定区域中的像素数。 通常以每英寸点数 (dpi)。 可能的值有：

    - `ldpi` &ndash; 低密度屏幕。

    - `mdpi` &ndash; 中等密度屏幕

    - `hdpi` &ndash; 高密度屏幕

    - `xhdpi` &ndash; 特高的密度屏幕

    - `nodpi` &ndash; 缩放的资源

    - `tvdpi` &ndash; 在 API 级别 13 (Android 3.2) 屏幕的 mdpi 和 hdpi 之间中引入。

- **触摸屏类型**&ndash;指定触摸屏设备可能具有的类型。 可能的值为`notouch`（没有触摸屏） `stylus` （电阻式触摸屏适用于触笔），和`finger`（触摸屏）。

- **键盘可用性**&ndash;指定哪种键盘是可用。 这可能会在应用程序的生存期内更改&ndash;例如当用户在打开的硬件键盘。 可能的值有：

    - `keysexposed` &ndash; 该设备已使用键盘。 如果没有启用的软件键盘，然后才使用此选项时打开硬件键盘。

    - `keyshidden` &ndash; 设备已硬件键盘，但其处于隐藏状态，然后不启用任何软件键盘。

    - `keyssoft` &ndash; 该设备已启用的软件键盘。

- **主文本输入法**&ndash;用于指定哪些类型的硬件密钥为可用于输入。 可能的值有：

    - `nokeys` &ndash; 不没有输入任何硬件密钥。

    - `qwerty` &ndash; 没有可用的全键盘。

    - `12key` &ndash; 12 键硬件键盘


- **导航键可用性** &ndash; 5 方式或方向键 （光板） 导航时可用。 这可能会更改你的应用程序的生存期内。 可能的值有：

    - `navexposed` &ndash; 导航键是可供用户使用

    - `navhidden` &ndash; 导航键将不可用。

-  **主要的非触摸导航方法**&ndash;导航可在设备上的类型。 可能的值有：

    - `nonav` &ndash; 可用的唯一导航设施是触摸屏

    - `dpad` &ndash; d-pad （光板） 是可用于导航

    - `trackball` &ndash; 设备具有用于导航轨迹球

    - `wheel` &ndash; 其中有一个或多个方向轮可用不常见的情况

-  **平台版本 （API 级别）** &ndash;格式 v 设备支持的 API 级别*N*，其中*N*是为目标的 API 级别。 例如，v11 将目标 API 级别 11 (Android 3.0) 设备。


有关更多详细信息资源限定符请参阅[提供资源](https://developer.android.com/guide/topics/resources/providing-resources.html)Android 开发人员网站上。


## <a name="how-android-determines-what-resources-to-use"></a>Android 如何确定使用哪些资源

它是非常可能和可能的 Android 应用程序将包含多个资源。 请务必了解在该设备上运行 Android 选择应用程序的资源的方式。

Android 通过遍历以下测试的规则确定基本的资源：

- **消除相互矛盾的限定符**&ndash;例如，如果设备方向为纵向，然后所有横向资源目录将被都拒绝。

- **忽略不支持的限定符**&ndash;并非所有的限定符可供所有 API 级别。 如果资源目录包含的设备不支持的限定符，则将忽略该资源目录。

- **确定下一步的最高优先级限定符**&ndash;指上表选择 （从上到下） 的下一步最高优先级限定符。

- **保持限定符任何资源目录**&ndash;是否存在任何匹配到上表限定符的资源目录选择 （从上到下） 的下一步最高优先级限定符。

下面的流程图也说明了这些规则：

[![资源流程图](alternate-resources-images/flowchart-sml.png)](alternate-resources-images/flowchart.png#lightbox)

当系统正在寻找特定于密度的资源，但找不到它们时，它将尝试查找其他密度的特定资源并将其扩展。 Android 不一定使用默认资源。
例如时寻找低密度资源和它不可用，, Android 可能对默认或中等密度资源选择高密度的资源版本。 这是 0.5 的因为高密度的资源可以按比例缩小到原来，这会导致比减少需要 0.75 倍的中等密度资源较少的可见性问题。

作为示例，请考虑具有以下可绘制资源目录的应用程序：

    drawable
    drawable-en
    drawable-fr-rCA
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

和现在使用以下配置设备上运行应用程序：

- **区域设置** &ndash; en GB
- **方向**&ndash;端口
- **屏幕密度** &ndash; hdpi
- **触摸屏类型** &ndash; notouch
- **主要的输入的法** &ndash; 12key

因为它们会发生冲突的区域设置中的法语资源开始时，消除`en-GB`，我们使用保留：

    drawable
    drawable-en
    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi
    drawable-port-ldpi
    drawable-port-notouch-12key

接下来，第一个限定符是从上述限定符表中选择：MCC 和 mnc。 不没有包含此限定符，因此忽略 MCC/mnc 代码任何资源目录。

选择下一步的限定符，则这是语言。 没有匹配的语言代码的资源。 不匹配的语言代码的所有资源目录`en`被拒绝，以便资源的列表现在为：

    drawable-en-port
    drawable-en-notouch-12key
    drawable-en-port-ldpi

下一步是存在的限定符是为屏幕方向，因此所有不匹配的屏幕方向的资源目录`port`消除了：

    drawable-en-port
    drawable-en-port-ldpi

接下来是屏幕密度的限定符`ldpi`，这会导致一个排除多个资源目录：

    drawable-en-port-ldpi

由于此过程中，Android 将使用可绘制资源的资源目录中`drawable-en-port-ldpi`设备。

> [!NOTE]
> 屏幕大小限定符提供此选择过程的一个例外。 很可能适用于 Android 选择专为比当前设备提供了一个较小屏幕的资源。 例如，大屏幕设备可能使用的资源为正常大小屏幕提供。 但是此反之不成立： 同一个大屏幕设备将使用为加大屏幕提供的资源。 如果 Android 找不到与给定的屏幕大小匹配的资源集，该应用程序将崩溃。
