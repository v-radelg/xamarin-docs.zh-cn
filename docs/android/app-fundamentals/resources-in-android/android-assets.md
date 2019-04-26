---
title: 使用 Android 资产
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 1e9a71de7725c8382e133d85977407bcc859fc58
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013600"
---
# <a name="using-android-assets"></a>使用 Android 资产

_资产_提供一种方法要包含在你的应用程序中的任意文件，例如文本、 xml、 字体、 音乐和视频。 如果尝试以包括这些文件作为"资源"，Android 将处理它们，到其资源的系统和将无法获取原始数据。 如果你想要访问数据保持不变，资产是一种方式执行此操作。

资产添加到你的项目将显示就像从你的应用程序使用可以读取的文件系统一样[AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)。
在此简单演示中，我们要将文本文件资产添加到我们的项目，使用读取该`AssetManager`，并将其显示在一个 TextView。


## <a name="add-asset-to-project"></a>将资产添加到项目

资产，请转`Assets`项目文件夹。 将新的文本文件添加到此文件夹名为`read_asset.txt`。 将一些文本放入其中，像"我来自资产 ！"。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 应已设置**生成操作**到此文件**AndroidAsset**:

![将生成操作设置为 AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

应该将设置 visual Studio for Mac**生成操作**到此文件**AndroidAsset**:

[![将生成操作设置为 AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

选择正确**BuildAction**可确保，将在编译时到 APK 中打包该文件。


## <a name="reading-assets"></a>读取资产

使用读取资产[AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)。 实例`AssetManager`即可访问[资产](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/)属性`Android.Content.Context`，例如活动。
在下面的代码中，我们打开我们**read_asset.txt**资产，读取内容，并将其使用一个 TextView 显示。

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```


## <a name="running-the-application"></a>运行应用程序

运行应用程序，你应该会看到如下：

![示例屏幕截图](android-assets-images/screenshot.png)


## <a name="related-links"></a>相关链接

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [上下文](https://developer.xamarin.com/api/type/Android.Content.Context/)
