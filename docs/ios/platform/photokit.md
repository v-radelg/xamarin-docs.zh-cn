---
title: 在 Xamarin.iOS PhotoKit
description: 本文档介绍了 PhotoKit，讨论其模型对象、 如何查询模型数据，并保存对照片库的更改。
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: d59cac9403a244ce553d84e0590b8a9c3d4d2f30
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61365843"
---
# <a name="photokit-in-xamarinios"></a>在 Xamarin.iOS PhotoKit

PhotoKit 是一个新的框架，允许应用程序查询系统图像库和创建自定义用户界面，可以查看和修改其内容。 它包括多个类，表示图像和视频资源，以及资产，例如唱片集和文件夹的集合。

## <a name="model-objects"></a>模型对象

PhotoKit 表示它调用模型对象中的这些资源。 表示照片和视频本身的模型对象属于类型`PHAsset`。 一个`PHAsset`包含元数据，例如资产的媒体类型和其创建日期。
同样，`PHAssetCollection`和`PHCollectionList`类分别包含资产集合和集合列表有关的元数据。 资产集合是资产，例如所有照片和视频在给定年份的组。 同样，集合列表是组的资产集合，如照片和视频按年分组。

## <a name="querying-model-data"></a>查询模型数据

PhotoKit 轻松查询模型数据通过各种 fetch 方法。 例如，若要检索的所有映像，将调用`PFAsset.Fetch`，并传递`PHAssetMediaType.Image`媒体类型。

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

`PHFetchResult`实例中将包含所有`PFAsset`实例表示映像。 若要获取这些图像本身，请使用`PHImageManager`(或缓存的版本中， `PHCachingImageManager`) 来发出请求的映像，通过调用`RequestImageForAsset`。 例如，下面的代码检索图像中每个资产`PHFetchResult`要在集合视图单元格中显示：


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

这会导致的映像，如下所示的网格：

![](photokit-images/image4.png "正在运行的应用显示一个图像网格")
 
## <a name="saving-changes-to-the-photo-library"></a>将更改保存到照片库

这是如何处理查询和读取数据。 此外可以对库重新编写的更改。 由于多个希望应用程序能够与系统照片库进行交互，您可以注册观察者的使用 PhotoLibraryObserver 更改通知。 然后，当更改，你的应用程序可以相应地更新。 例如，下面是一个简单实现，以重新加载在上面的集合视图：

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
    
若要实际将更改写回从你的应用程序，您创建更改请求。 每个模型类有一个关联的更改请求类。 例如，若要更改 PHAsset，则创建 PHAssetChangeRequest。 若要执行的写回的照片库和发送给观察程序与上述更改的步骤如下：

-   执行编辑操作。
-   将筛选出的图像数据保存到 PHContentEditingOutput 实例。
-   请更改请求将更改窗体发布编辑输出。

下面是将更改写回适用 Core 映像树上筛选器的图像的示例：

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
    
当用户选择按钮时，应用筛选器：

![](photokit-images/image5.png "正在应用的筛选器的一个示例")
 
并感谢 PHPhotoLibraryChangeObserver，更改都反映在集合视图时用户后退浏览：

![](photokit-images/image6.png "当用户导航回会反映在集合视图中更改")
