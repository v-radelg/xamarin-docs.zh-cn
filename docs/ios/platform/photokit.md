---
title: Xamarin 中的 PhotoKit
description: 本文档介绍 PhotoKit，讨论其模型对象，如何查询模型数据，以及如何将更改保存到照片库。
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: def34efd1fd48cc0e7dd802a6d3e843be1e156a4
ms.sourcegitcommit: 5ddb107b0a56bef8a16fce5bc6846f9673b3b22e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75558802"
---
# <a name="photokit-in-xamarinios"></a>Xamarin 中的 PhotoKit

[![下载示例](~/media/shared/download.png) 下载代码示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-samplephotoapp/)

PhotoKit 是一个框架，它允许应用程序查询系统映像库并创建自定义用户界面来查看和修改其内容。 它包括多种类，这些类表示图像和视频资产，以及资产（如影集和文件夹）的集合。

## <a name="permissions"></a>权限

在你的应用程序可以访问照片库之前，会向用户显示权限对话框。 你必须在**info.plist**文件中提供说明性文本，以说明你的应用程序如何使用照片库，例如：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Applies filters to photos and updates the original image</string>
```

## <a name="model-objects"></a>模型对象

PhotoKit 在它调用模型对象的内容中表示这些资产。 代表照片和视频的模型对象的类型为 `PHAsset`。 `PHAsset` 包含元数据（例如资产的媒体类型和其创建日期）。
同样，`PHAssetCollection` 和 `PHCollectionList` 类分别包含有关资产集合和集合列表的元数据。 资产集合是一组资产，例如给定年份的所有照片和视频。 同样，收集列表也是一组资产集合，如按年份分组的照片和视频。

## <a name="querying-model-data"></a>查询模型数据

使用 PhotoKit 可以轻松地通过各种 fetch 方法来查询模型数据。 例如，若要检索所有映像，请调用 `PHAsset.Fetch`，同时传递 `PHAssetMediaType.Image` 媒体类型。

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

然后，`PHFetchResult` 实例将包含表示图像的所有 `PHAsset` 实例。 若要获取图像本身，请使用 `PHImageManager` （或缓存版本 `PHCachingImageManager`）通过调用 `RequestImageForAsset`发出对映像的请求。 例如，以下代码将为 `PHFetchResult` 中的每个资产检索一个图像，以便在 "集合" 视图单元格中显示：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
    imageMgr.RequestImageForAsset (
        (PHAsset)fetchResults [(uint)indexPath.Item],
        thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (),
        (img, info) => {
            imageCell.ImageView.Image = img;
        }
    );
    return imageCell;
}
```

这会生成图像网格，如下所示：

![](photokit-images/image4.png "The running app displaying a grid of images")

## <a name="saving-changes-to-the-photo-library"></a>保存对照片库所做的更改

这就是处理查询和读取数据的方法。 你还可以将更改写入库。 由于多个感兴趣的应用程序能够与系统照片库进行交互，因此你可以注册一个观察者，以便使用 `PhotoLibraryObserver`向其通知更改。 然后，当发生更改时，应用程序可以相应地进行更新。 例如，下面是一个简单的实现，用于重载上面的集合视图：

```csharp
class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
{
    readonly PhotosViewController controller;
    public PhotoLibraryObserver (PhotosViewController controller)

    {
        this.controller = controller;
    }

    public override void PhotoLibraryDidChange (PHChange changeInstance)
    {
        DispatchQueue.MainQueue.DispatchAsync (() => {
            var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
            controller.fetchResults = changes.FetchResultAfterChanges;
            controller.CollectionView.ReloadData ();
        });
    }
}
```

若要实际从你的应用程序中写回更改，你需要创建一个更改请求。 每个模型类都具有关联的更改请求类。 例如，若要更改 `PHAsset`，请创建 `PHAssetChangeRequest`。 执行写回照片库并发送到观察者的更改的步骤如下：

1. 执行编辑操作。
2. 将筛选的图像数据保存到 `PHContentEditingOutput` 的实例。
3. 发出更改请求，以从编辑输出发布更改。

下面是一个示例，该示例将更改写入到应用核心映像 noir 筛选器的映像：

```csharp
void ApplyNoirFilter (object sender, EventArgs e)
{
    Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {

        // perform the editing operation, which applies a noir filter in this case
        var image = CIImage.FromUrl (input.FullSizeImageUrl);
        image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
        var noir = new CIPhotoEffectNoir {
            Image = image
        };
        var ciContext = CIContext.FromOptions (null);
        var output = noir.OutputImage;
        var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
        imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
        var editingOutput = new PHContentEditingOutput(input);
        var adjustmentData = new PHAdjustmentData();
        var data = uiImage.AsJPEG();
        NSError error;
        data.Save(editingOutput.RenderedContentUrl, false, out error);
        editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
        PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
            PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
            request.ContentEditingOutput = editingOutput;
        },
        (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
    });
}
```

当用户选择该按钮时，将应用筛选器：

![两个示例，在应用筛选器前后显示照片](photokit-images/image5.png)

由于 `PHPhotoLibraryChangeObserver`，当用户导航回来时，此更改将反映在 "集合" 视图中：

![显示已修改照片的照片集合视图](photokit-images/image6.png)
