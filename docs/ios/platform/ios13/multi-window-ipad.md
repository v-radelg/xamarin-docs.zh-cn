---
title: Xamarin 中的多个 Windows for iPad
description: 向 iPad 应用程序添加多窗口支持。
ms.prod: xamarin
ms.assetid: 524b6f2e-dbdf-11e9-8a34-2a2ae2dbcce4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/20/2019
ms.openlocfilehash: b3f96f342679d8be6d2f8fbc0ad5962ca675d2a5
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213803"
---
# <a name="multiple-windows-for-ipad"></a>适用于 iPad 的多个窗口

iOS 13 现在支持 iPad 上相同应用的并行窗口。 这可以实现 windows 之间的新体验和拖放式交互。 本文档介绍了如何设置应用程序以支持此功能，并介绍了这些新功能。 

## <a name="project-configuration"></a>项目配置

若要为多个窗口配置你的项目`info.plist` ，请`NSUserActivityTypes`修改以声明你的应用程序将处理此类型的`UIApplicationSceneManifest`活动， `UIApplicationSupportsMultipleScenes`并为多个`UISceneConfigurations`窗口启用并将你的带有情节提要的场景。

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>com.xamarin.Gallery.openDetail</string>
</array>
<key>UIApplicationSceneManifest</key>
<dict>
    <key>UIApplicationSupportsMultipleScenes</key>
    <true/>
    <key>UISceneConfigurations</key>
    <dict>
        <key>UIWindowSceneSessionRoleApplication</key>
        <array>
            <dict>
                <key>UISceneConfigurationName</key>
                <string>Default Configuration</string>
                <key>UISceneDelegateClassName</key>
                <string>SceneDelegate</string>
                <key>UISceneStoryboardFile</key>
                <string>Main</string>
            </dict>
        </array>
    </dict>
</dict>
```

## <a name="opening-multiple-windows"></a>打开多个窗口

在 iPad 上打开并运行应用程序后，有几种方法可用于打开该应用程序的多个窗口，iOS 处理是默认的。

- **应用公开**-点击停靠中的应用图标，以便在应用处于打开状态时进入此模式。
- **滑过**-将应用图标从正在运行的应用的顶部上方拖动到浮动窗口。
- **拆分屏幕**-将应用图标从停靠拖动到 iPad 屏幕的边缘，以创建一个新的并排窗口。

您的应用程序中提供了用于进入多窗口模式的其他交互。

**从应用中拖动**-在应用中使用拖动交互，就像在`NSUserActivity`前面的示例中拖动应用图标一样启动新的。

当使用[拖放交互][0]时，您可以创建一个`NSUserActivity`并关联要传递到您要求 iOS 打开的新窗口中的数据。

```csharp
public UIDragItem [] GetItemsForBeginningDragSession (UICollectionView collectionView, IUIDragSession session, NSIndexPath indexPath)
{
    var selectedPhoto = GetPhoto (indexPath);

    var userActivity = selectedPhoto.OpenDetailUserActivity ();
    var itemProvider = new NSItemProvider (UIImage.FromFile (selectedPhoto.Name));
    itemProvider.RegisterObject (userActivity, NSItemProviderRepresentationVisibility.All);

    var dragItem = new UIDragItem (itemProvider) {
        LocalObject = selectedPhoto
    };

    return new [] { dragItem };
}
```

在上面的代码中， `selectedPhoto`模型对象具有返回调用`OpenDetailUserActivity()`的`NSUserActivity`方法。 拖动笔势完成后， `UIDragItem`会`userActivity`通过`NSItemProvider`提供。

**显式操作**-按钮或链接上的用户笔势提供了打开新窗口的功能。

从中， `RequestSceneSessionActivation` `UISceneSession` `UIApplication`你可以通过调用来启动新的。 如果现有场景已经存在，则应使用该场景。 默认情况下，将为您创建一个新的场景。

```csharp
pubic void ItemSelected(UICollectionView collectionView, NSIndexPath indexPath)
{
    var userActivity = selectedPhoto.OpenDetailUserActivity ();

    UIApplication.SharedApplication.RequestSceneSessionActivation(
        sceneSession: null,
        userActivity: userActivity,
        options: null,
        errorHandler: null
    );
}
```

在此示例中， `userActivity`是传递`RequestSceneSessionActivation`给方法的唯一值，以便基于显式用户操作打开应用程序的新窗口`ItemSelected` ; 在本例中为的处理程序`UICollectionView`。

## <a name="related-links"></a>相关链接

- [在 Xamarin 中拖放][0]

[0]: ~/ios/platform/introduction-to-ios11/drag-and-drop.md
