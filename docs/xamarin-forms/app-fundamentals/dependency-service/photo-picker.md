---
title: 从图片库中选取照片
description: 本文介绍如何使用 Xamarin.Forms DependencyService 类从手机的图片库中选取照片。
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 5c4d43723bc23d8a16be8fec0a895a31ab8bcfdc
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233960"
---
# <a name="picking-a-photo-from-the-picture-library"></a>从图片库中选取照片

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)

本文介绍如何创建一种应用程序，使用户可通过该应用程序从手机的图片库中选取照片。 由于 Xamarin.Forms 不包含此功能，因此有必要使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 来访问每个平台上的本机 API。  本文将介绍在此任务中使用 `DependencyService` 的以下步骤：

- **[创建接口](#Creating_the_Interface)** &ndash; 了解如何在共享代码中创建接口。
- **[iOS 实现](#iOS_Implementation)** &ndash; 了解如何在 iOS 本机代码中实现接口。
- **[Android 实现](#Android_Implementation)** &ndash; 了解如何在 Android 本机代码中实现接口。
- **[通用 Windows 平台实现](#UWP_Implementation)** &ndash; 了解如何在通用 Windows 平台 (UWP) 的本机代码中实现接口。
- **[在共享代码中实现](#Implementing_in_Shared_Code)** &ndash; 了解如何使用 `DependencyService` 从共享代码调用本机实现。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>创建界面

首先，在表示所需功能的共享代码中创建接口。 如果是照片选取应用程序，只需要一种方法。 该方法在示例代码的 .NET Standard 库中的 [`IPicturePicker`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) 接口进行定义：

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

`GetImageStreamAsync` 方法定义为异步，因为该方法必须快速返回，但是不能为所选照片返回 `Stream` 对象，除非用户浏览了图片库并选择了一张照片。

该接口使用特定于平台的代码在所有平台上实现。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 实现

`IPicturePicker` 接口的 iOS 实现使用 [`UIImagePickerController`](xref:UIKit.UIImagePickerController)，正如[“从库中选择照片”](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)方案和[示例代码](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)中描述那样。

iOS 实现包含在示例代码的 iOS 项目中的 [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) 类中。 为了使该类对 `DependencyService` 管理器可见，此类必须被标识为 `Dependency` 类型的 [`assembly`] 属性，且该类必须是公共的，并显式地实现 `IPicturePicker` 接口：

```csharp
[assembly: Dependency (typeof (PicturePickerImplementation))]

namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<Stream> GetImageStreamAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.PhotoLibrary,
                MediaTypes = UIImagePickerController.AvailableMediaTypes(UIImagePickerControllerSourceType.PhotoLibrary)
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

`GetImageStreamAsync` 方法创建 `UIImagePickerController` 并初始化它，以从照片库中选择图像。 需要两个事件处理程序：一个用于用户选择照片，另一个用于用户取消照片库的显示。 然后 `PresentModalViewController` 向用户显示照片库。

此时，`GetImageStreamAsync` 方法必须将 `Task<Stream>` 对象返回给调用它的代码。 只有用户已完成与照片库的交互并调用其中一个事件处理程序时，该任务才会完成。 对于这种情况，[`TaskCompletionSource`](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) 类是必不可少的。 该类提供要从 `GetImageStreamAsync` 方法返回的适当泛型类型的 `Task` 对象，稍后完成任务时该类将收到通知。

用户已选择图片时，将调用 `FinishedPickingMedia` 事件处理程序。 但是，处理程序提供 `UIImage` 对象，而 `Task` 必须返回 .NET `Stream` 对象。 这需要执行两个步骤来完成：首先将 `UIImage` 对象转换为存储在 `NSData` 对象中的内存中的 JPEG 文件，然后将 `NSData` 对象转换为 .NET `Stream` 对象。 通过调用 `TaskCompletionSource` 对象的 `SetResult` 方法，以提供 `Stream` 对象的方式完成任务：

```csharp
namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;
        ...
        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            UIImage image = args.EditedImage ?? args.OriginalImage;

            if (image != null)
            {
                // Convert UIImage to .NET Stream object
                NSData data = image.AsJPEG(1);
                Stream stream = data.AsStream();

                UnregisterEventHandlers();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
            }
            else
            {
                UnregisterEventHandlers();
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            UnregisterEventHandlers();
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }

        void UnregisterEventHandlers()
        {
            imagePicker.FinishedPickingMedia -= OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled -= OnImagePickerCancelled;
        }
    }
}

```

iOS 应用程序需要用户的许可才能访问手机的照片库。 将以下内容添加到 Info.plist 文件的 `dict` 部分：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 实现

Android 实现使用[选择图像](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)方案和[示例代码](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)中描述的技术。 但是，如果用户已选择图片库中的图像，则调用的方法是派生自 `Activity` 的类中的 `OnActivityResult` 替代。 因此，Android 项目中的常规 [`MainActivity`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) 类补充了一个字段、一个属性和 `OnActivityResult` 方法的一个替代：

```csharp
public class MainActivity : FormsAppCompatActivity
{
    ...
    // Field, property, and method for Picture Picker
    public static readonly int PickImageId = 1000;

    public TaskCompletionSource<Stream> PickImageTaskCompletionSource { set; get; }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent intent)
    {
        base.OnActivityResult(requestCode, resultCode, intent);

        if (requestCode == PickImageId)
        {
            if ((resultCode == Result.Ok) && (intent != null))
            {
                Android.Net.Uri uri = intent.Data;
                Stream stream = ContentResolver.OpenInputStream(uri);

                // Set the Stream as the completion of the Task
                PickImageTaskCompletionSource.SetResult(stream);
            }
            else
            {
                PickImageTaskCompletionSource.SetResult(null);
            }
        }
    }
}

```

`OnActivityResult` 替代指示所选图片文件具有 Android `Uri` 对象，但是可以通过调用从活动的 `ContentResolver` 属性中获得的 `ContentResolver` 对象的 `OpenInputStream` 方法将其转换为 .NET `Stream` 对象。

与 iOS 实现一样，任务完成时，Android 实现将使用 `TaskCompletionSource` 进行通知。 该 `TaskCompletionSource` 对象在 `MainActivity` 类中定义为公共属性。 这样便可在 Android 项目中的 [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) 类中引用该属性。 这是使用 `GetImageStreamAsync` 方法的类：

```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.Droid
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("image/*");
            intent.SetAction(Intent.ActionGetContent);

            // Start the picture-picker activity (resumes in MainActivity.cs)
            MainActivity.Instance.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            MainActivity.Instance.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return MainActivity.Instance.PickImageTaskCompletionSource.Task;
        }
    }
}
```

该方法访问 `MainActivity` 类有几个目的：实现 `Instance` 属性、`PickImageId` 字段、`TaskCompletionSource` 属性，以及调用 `StartActivityForResult`。 该方法是由 `FormsAppCompatActivity` 类定义的，此类是 `MainActivity` 的基类。

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>UWP 实现

与 iOS 和 Android 实现不同，用于通用 Windows 平台的照片选取器的实现不需要 `TaskCompletionSource` 类。 [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) 类使用 [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) 类访问照片库。 由于 `FileOpenPicker` 的 `PickSingleFileAsync` 方法本身是异步的，`GetImageStreamAsync` 方法可以简单地将 `await` 与该方法（以及其他异步方法）一起使用，并返回 `Stream` 对象：


```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.UWP
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public async Task<Stream> GetImageStreamAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary,
            };

            openPicker.FileTypeFilter.Add(".jpg");
            openPicker.FileTypeFilter.Add(".jpeg");
            openPicker.FileTypeFilter.Add(".png");

            // Get a file and return a Stream
            StorageFile storageFile = await openPicker.PickSingleFileAsync();

            if (storageFile == null)
            {
                return null;
            }

            IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
            return raStream.AsStreamForRead();
        }
    }
}
```

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共享代码中实现

为每个平台实现接口后，现在 .NET Standard 库中的应用程序即可利用该接口。

[`App`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) 类创建 `Button` 来选择照片：

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

`Clicked` 处理程序使用 `DependencyService` 类调用 `GetImageStreamAsync`。 这会导致在平台项目中进行调用。 如果该方法返回 `Stream` 对象，那么处理程序将用 `TapGestureRecognizer` 为该图片创建 `Image` 元素，并将页面上的 `StackLayout` 替换为 `Image`：

```csharp
pickPictureButton.Clicked += async (sender, e) =>
{
    pickPictureButton.IsEnabled = false;
    Stream stream = await DependencyService.Get<IPicturePicker>().GetImageStreamAsync();

    if (stream != null)
    {
        Image image = new Image
        {
            Source = ImageSource.FromStream(() => stream),
            BackgroundColor = Color.Gray
        };

        TapGestureRecognizer recognizer = new TapGestureRecognizer();
        recognizer.Tapped += (sender2, args) =>
        {
            (MainPage as ContentPage).Content = stack;
            pickPictureButton.IsEnabled = true;
        };
        image.GestureRecognizers.Add(recognizer);

        (MainPage as ContentPage).Content = image;
    }
    else
    {
        pickPictureButton.IsEnabled = true;
    }
};
```

点击 `Image` 元素使页面恢复正常。


## <a name="related-links"></a>相关链接

- [从库 (iOS) 中选择照片](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [选择图像 (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService（示例）](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
