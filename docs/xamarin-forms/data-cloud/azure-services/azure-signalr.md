---
title: 具有 Xamarin 的 Azure SignalR 服务
description: 通过 Xamarin 进行 Azure SignalR 服务和 Azure Functions 入门
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: a4d0f5c5ceefcfe9a36a5fcf10c6fb4937c1db90
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739215"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>具有 Xamarin 的 Azure SignalR 服务

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core SignalR 是一个应用程序模型，可简化将实时通信添加到应用程序的过程。 Azure SignalR 服务允许快速开发和部署可缩放的 SignalR 应用程序。 Azure Functions 是一种生存期较短的无服务器代码方法，可以组合起来形成事件驱动的可缩放应用程序。

本文和示例展示了如何将 Azure SignalR 服务和 Azure Functions 与 Xamarin 结合起来，以将实时消息传递到连接的客户端。

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>创建 Azure SignalR 服务并 Azure Functions 应用

该示例应用程序包含三个关键组件：一个 Azure SignalR 服务中心、一个包含两个函数的 Azure Functions 实例，以及一个可发送和接收消息的移动应用程序。 这些组件的交互方式如下：

1. 移动应用程序调用**Negotiate** Azure Function 获取有关 SignalR 中心的信息。
1. 移动应用程序使用协商信息向 SignalR 中心注册自身，并形成连接。
1. 注册后，移动应用程序将消息发送到 "**交谈**" Azure 功能。
1. **对话**函数将传入消息传递给 SignalR 中心。
1. SignalR 中心将消息广播到所有连接的移动应用程序实例，包括原始发送方。

> [!NOTE]
> 可以使用 Visual Studio 2019 和 Azure 运行时工具在本地运行示例应用程序中的**Negotiate**和**交谈**函数。 但是，Azure SignalR 服务不能在本地进行模拟，并且很难向物理或虚拟设备公开本地承载的 Azure Functions 以便进行测试。 建议将 Azure Functions 部署到 Azure Functions 应用程序实例，因为这样做可以进行跨平台测试。 有关部署的详细信息，请参阅[与 Visual Studio 2019 一起部署 Azure Functions](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-signalr-service"></a>创建 Azure SignalR 服务

可以通过在 Azure 门户的左上角选择 "**创建资源**" 并搜索**SignalR**，来创建 Azure SignalR 服务。 可以在免费层创建 Azure SignalR 服务。 Azure SignalR 服务必须处于**无服务器**服务模式。 如果意外选择了 "默认" 或 "经典" 服务模式，可以稍后在 Azure SignalR 服务属性中进行更改。

以下屏幕截图显示了如何创建新的 Azure SignalR 服务：

![Azure 门户中的 Azure SignalR 服务创建屏幕截图](azure-signalr-images/azure-signalr-create.png "创建 Azure SignalR 服务")

创建后，Azure SignalR 服务的 "**密钥**" 部分包含一个**连接字符串**，用于将 Azure Functions 应用连接到 SignalR 中心。 以下屏幕截图显示在 Azure SignalR 服务中找到连接字符串的位置：

![Azure 门户中的 Azure SignalR 连接字符串的屏幕截图](azure-signalr-images/azure-signalr-connection-string.png "Azure SignalR 连接字符串")

此连接字符串用于[与 Visual Studio 2019 一起部署 Azure Functions](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-functions-app"></a>创建 Azure Functions 应用

若要测试示例应用程序，应在 Azure 门户中创建新的 Azure Functions 应用程序。 记下**应用程序名称**，因为示例应用程序**Constants.cs**文件中使用了此 URL。 以下屏幕截图显示了一个名为 "xdocsfunctions" 的新 Azure Functions 应用程序的创建：

[![Screenshot Azure Functions 应用创建](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

可以通过 Visual Studio 2019 将 Azure 函数部署到 Azure Functions 的应用程序实例。 以下各节介绍如何将示例应用程序中的两个函数部署到 Azure Functions 的应用程序实例。

### <a name="build-azure-functions-in-visual-studio-2019"></a>在 Visual Studio 2019 中生成 Azure Functions

该示例应用程序包含一个名为**ChatServer**的类库，其中包括两个无服务器 Azure Functions 在名为**Negotiate.cs**和**Talk.cs**的文件中。

@No__t_0 函数使用包含 `AccessToken` 属性和 `Url` 属性的 `SignalRConnectionInfo` 对象来响应 web 请求。 移动应用程序使用这些值向 SignalR 中心注册自身。 下面的代码演示 `Negotiate` 函数：

```csharp
[FunctionName("Negotiate")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous,"get",Route = "negotiate")]
    HttpRequest req,
    [SignalRConnectionInfo(HubName = "simplechat")]
    SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

@No__t_0 函数响应在 POST 正文中提供消息对象的 HTTP POST 请求。 POST 正文会转换为 `SignalRMessage` 并转发到 SignalR 中心。 下面的代码演示 `Talk` 函数：

```csharp
[FunctionName("Talk")]
public static async Task<IActionResult> Run(
    [HttpTrigger(
        AuthorizationLevel.Anonymous,
        "post",
        Route = "talk")]
    HttpRequest req,
    [SignalR(HubName = "simplechat")]
    IAsyncCollector<SignalRMessage> questionR,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string json = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic obj = JsonConvert.DeserializeObject(json);

        var name = obj.name.ToString();
        var text = obj.text.ToString();

        var jObject = new JObject(obj);

        await questionR.AddAsync(
            new SignalRMessage
            {
                Target = "newMessage",
                Arguments = new[] { jObject }
            });

        return new OkObjectResult($"Hello {name}, your message was '{text}'");
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("There was an error: " + ex.Message);
    }
}
```

若要了解有关 Azure 函数和 Azure Functions 应用的详细信息，请参阅[Azure Functions 文档](/azure/azure-functions/)。

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>与 Visual Studio 2019 一起部署 Azure Functions

Visual Studio 2019 允许你将函数部署到 Azure Functions 应用。 Azure 托管函数通过为所有设备提供可访问的测试终结点来简化跨平台测试。

右键单击示例函数应用，然后选择 "**发布**" 启动对话框，将函数发布到 Azure Functions 应用。 如果按照前面的步骤设置 Azure Function App，可以选择 "**选择现有**项" 将示例应用程序发布到 Azure Functions 应用程序。 以下屏幕截图显示了 Visual Studio 2019 中的 "发布" 对话框选项：

![Visual Studio 2019 中的 "发布选项" 对话框](azure-signalr-images/vs-publish-target.png "Visual Studio 2019 中的发布选项")

登录到 Microsoft 帐户后，可以找到并选择 Azure Functions 应用作为发布目标。 以下屏幕截图显示了 Visual Studio 2019 发布对话框中 Azure Functions 应用示例：

![Visual Studio 2019 发布对话框中的 Azure Functions 应用](azure-signalr-images/vs-app-selection.png "在 Visual Studio 2019 发布对话框中 Azure Functions 应用")

选择 Azure Functions 的应用程序实例后，将显示 "网站 URL"、"配置" 和 "目标 Azure Functions 应用的其他信息"。 选择 "**编辑 Azure App Service 设置**"，然后在**远程**字段中输入连接字符串。 **协商**和**交谈**函数使用连接字符串连接到 azure SignalR 服务，并在 Azure 门户的 azure SignalR 服务的 "**密钥**" 部分中找到。 有关连接字符串的详细信息，请参阅[创建 Azure SignalR 服务](#create-an-azure-signalr-service)。

输入连接字符串后，可以单击 "**发布**" 将函数部署到 Azure Functions 应用。 完成后，函数将在 Azure 门户中的 Azure Functions 应用中列出。 以下屏幕截图显示了 Azure 门户中的已发布函数：

![Azure Functions 应用中发布的函数](azure-signalr-images/azure-functions-deployed.png "Azure Functions 应用中发布的函数")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>将 Azure SignalR Service 与 Xamarin 集成

Azure SignalR 服务和 Xamarin 应用程序之间的集成是在 `MainPage` 类中实例化的 SignalR 服务类，该服务类将事件处理程序分配给三个事件。 有关这些事件处理程序的详细信息，请参阅[在 Xamarin 中使用 SignalR 服务类](#use-the-signalr-service-class-in-xamarinforms)。

该示例应用程序包含一个**Constants.cs**类，该类必须使用 Azure Functions 应用程序的 URL 终结点进行自定义。 将 `HostName` 属性的值设置为 Azure Functions 应用地址。 以下代码显示了**Constants.cs**属性，其中 `HostName` 值的示例：

```csharp
public static class Constants
{
    public static string HostName { get; set; } = "https://example-functions-app.azurewebsites.net/";

    // Used to differentiate message types sent via SignalR. This
    // sample only uses a single message type.
    public static string MessageName { get; set; } = "newMessage";

    public static string Username
    {
        get
        {
            return $"{Device.RuntimePlatform} User";
        }
    }
}
```

> [!NOTE]
> 示例应用程序**Constants.cs**文件中的 `Username` 属性使用设备的 `RuntimePlatform` 值作为用户名。 这样就可以轻松地测试设备跨平台，并识别哪个设备正在发送消息。 在实际应用程序中，此值可能是在注册或登录过程中收集的唯一用户名。

### <a name="the-signalr-service-class"></a>SignalR 服务类

示例应用程序的**ChatClient**项目中的 `SignalRService` 类显示一个实现，该实现调用 Azure Functions 应用中的函数以连接到 Azure SignalR 服务。

@No__t_1 类中的 `SendMessageAsync` 方法用于将消息发送到连接到 Azure SignalR 服务的客户端。 此方法对 Azure Functions 应用中托管的**对话**函数执行 HTTP POST 请求，其中包括 JSON 序列化的 `Message` 对象作为 POST 有效负载。 **对话**函数将消息传递到 Azure SignalR 服务，以便广播到所有连接的客户端。 下面的代码演示了 `SendMessageAsync` 方法：

```csharp
public async Task SendMessageAsync(string username, string message)
{
    IsBusy = true;

    var newMessage = new Message
    {
        Name = username,
        Text = message
    };

    var json = JsonConvert.SerializeObject(newMessage);
    var content = new StringContent(json, Encoding.UTF8, "application/json");
    var result = await client.PostAsync($"{Constants.HostName}/api/talk", content);

    IsBusy = false;
}
```

@No__t_1 类中的 `ConnectAsync` 方法对 Azure Functions 应用中托管的**Negotiate**函数执行 HTTP GET 请求。 **Negotiate**函数返回 JSON，并将其反序列化为 `NegotiateInfo` 类的实例。 检索 `NegotiateInfo` 对象后，它将用于使用 `HubConnection` 类的实例直接注册到 Azure SignalR 服务。

ASP.NET Core SignalR 会将传入数据从打开的连接转换为消息，并允许开发人员定义消息类型，并按类型将事件处理程序绑定到传入消息。 @No__t_0 方法将为示例应用程序的**Constants.cs**文件（默认为 "newMessage"）中定义的消息名称注册事件处理程序。

下面的代码演示了 `ConnectAsync` 方法：

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
            .WithUrl(negotiate.Url, options =>
            {
                options.AccessTokenProvider = async () => negotiate.AccessToken;
            })
            .Build();

        connection.On<JObject>(Constants.MessageName, AddNewMessage);
        await connection.StartAsync();

        IsConnected = true;
        IsBusy = false;

        Connected?.Invoke(this, true, "Connection successful.");
    }
    catch (Exception ex)
    {
        ConnectionFailed?.Invoke(this, false, ex.Message);
    }
}
```

@No__t_0 方法作为事件处理程序绑定到 `ConnectAsync` 消息中，如前面的代码所示。 接收到消息时，将调用 `AddNewMessage` 方法，并将消息数据作为 `JObject` 提供。 @No__t_0 方法将 `JObject` 转换为 `Message` 类的实例，然后调用 `NewMessageReceived` 的处理程序（如果已绑定）。 下面的代码演示了 `AddNewMessage` 方法：

```csharp
public void AddNewMessage(JObject message)
{
    Message messageModel = new Message
    {
        Name = message.GetValue("name").ToString(),
        Text = message.GetValue("text").ToString(),
        TimeReceived = DateTime.Now
    };

    NewMessageReceived?.Invoke(this, messageModel);
}
```

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>在 Xamarin 中使用 SignalR 服务类

利用 Xamarin 中的 SignalR 服务类，可以通过绑定 `MainPage` 代码隐藏类中的 `SignalRService` 类事件来实现 Forms。

SignalR 连接成功完成后，将触发 `SignalRService` 类中的 `Connected` 事件。 当 SignalR 连接失败时，将激发 `SignalRService` 类中的 `ConnectionFailed` 事件。 @No__t_0 事件处理程序方法绑定到 `MainPage` 构造函数中的两个事件。 此事件处理程序根据连接 `success` 参数更新 "连接" 和 "发送" 按钮状态，并使用 `AddMessage` 方法将事件提供的消息添加到聊天队列。 下面的代码演示 `SignalR_ConnectionChanged` 事件处理程序方法：

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

从 Azure SignalR 服务收到新消息时，将激发 `SignalRService` 类中的 `NewMessageReceived` 事件。 @No__t_0 事件处理程序方法绑定到了 `MainPage` 构造函数中的 `NewMessageReceived` 事件。 此事件处理程序将传入的 `Message` 对象转换为字符串，并使用 `AddMessage` 方法将其添加到聊天队列。 下面的代码演示 `SignalR_NewMessageReceived` 事件处理程序方法：

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

@No__t_0 方法将新消息作为 `Label` 对象添加到聊天队列。 @No__t_0 方法通常是由主 UI 线程之外的事件处理程序调用的，因此它会强制在主线程上发生 UI 更新以防止出现异常。 下面的代码演示了 `AddMessage` 方法：

```csharp
void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        Label label = new Label
        {
            Text = message,
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        };

        messageList.Children.Add(label);
    });
}
```

## <a name="test-the-application"></a>测试应用程序

可在 iOS、Android 和 UWP 上测试 SignalR chat 应用程序，前提是你拥有：

1. 已创建 Azure SignalR 服务。
1. 已创建 Azure Functions 应用。
1. 已通过 Azure Functions 应用终结点自定义**Constants.cs**文件。

完成这些步骤并运行应用程序后，单击 "**连接**" 按钮即可与 Azure SignalR 服务建立连接。 键入一条消息并单击 "**发送**" 按钮会导致消息显示在任何连接的移动应用程序的聊天队列中。

## <a name="related-links"></a>相关链接

* [用 Xamarin 和 SignalR 编制实时移动应用](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [SignalR 简介](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Azure Functions 简介](/azure/azure-functions/functions-overview)
* [Azure Functions 文档](/azure/azure-functions/)
* [MVVM SignalR chat 示例](https://github.com/lbugnion/sample-xamarin-signalr)
