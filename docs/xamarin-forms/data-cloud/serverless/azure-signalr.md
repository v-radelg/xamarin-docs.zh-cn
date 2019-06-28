---
title: 使用 Xamarin.Forms azure SignalR 服务
description: Azure SignalR 服务和 Azure Functions 使用 Xamarin.Forms 入门
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: d79ffb844a65c145fd6cb22a5a3e1dfc7a6bfe41
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2019
ms.locfileid: "67418120"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>使用 Xamarin.Forms azure SignalR 服务

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureSignalR)

ASP.NET Core SignalR 是一个应用程序模型，简化了向应用程序添加实时通信的过程。 Azure SignalR 服务允许快速开发和部署可缩放的 SignalR 应用程序。 Azure Functions 是可以组合使用窗体事件驱动、 可缩放的应用程序的生存期较短的无服务器代码方法。

此文章和示例演示如何将 Azure SignalR 服务以及使用 Xamarin.Forms 的 Azure Functions，以将实时消息传递到连接的客户端。

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>创建 Azure SignalR 服务和 Azure Functions 应用

示例应用程序包含三个关键组件： Azure SignalR 服务中心、 Azure Functions 有两个函数实例和可以发送和接收消息的移动应用程序。 这些组件进行交互，如下所示：

1. 移动应用程序调用**Negotiate** Azure 函数来获取有关 SignalR 集线器的信息。
1. 移动应用程序使用的协商信息将自身注册 SignalR hub，并形成连接。
1. 注册后，移动应用程序将消息发布到**讨论**Azure 函数。
1. **讨论**函数将传入的消息传递到 SignalR hub。
1. SignalR hub 广播到所有连接的移动应用程序实例，包括原始发件人的消息。

> [!NOTE]
> **Negotiate**并**通信**可以使用 Visual Studio 2019 和 Azure 运行时工具在本地运行示例应用程序中的函数。 但是，Azure SignalR 服务不能进行本地模拟，并且很难公开本地托管 Azure Functions 添加到物理或虚拟设备以进行测试。 建议将 Azure 函数部署到 Azure 函数应用实例，因为这会使跨平台测试。 有关部署的详细信息，请参阅[使用 Visual Studio 2019 部署 Azure Functions](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-signalr-service"></a>创建 Azure SignalR 服务

Azure SignalR 服务可以通过选择创建**创建资源**在 Azure 门户，然后搜索左上角**SignalR**。 可以在免费层创建 Azure SignalR 服务。 Azure SignalR 服务必须在**无服务器**服务模式。 如果您意外地选择默认值或经典服务模式下，可以更高版本中的 Azure SignalR 服务属性进行更改。

下面的屏幕截图演示如何创建新的 Azure SignalR 服务：

![在 Azure 门户中创建 Azure SignalR 服务的屏幕截图](azure-signalr-images/azure-signalr-create.png "创建 Azure SignalR 服务")

创建后，**密钥**Azure SignalR 服务的部分包含**连接字符串**，用于将 Azure 函数应用连接到 SignalR hub。 下面的屏幕截图显示了在何处可以找到 Azure SignalR 服务中的连接字符串：

![在 Azure 门户中的屏幕截图的 Azure SignalR 连接字符串](azure-signalr-images/azure-signalr-connection-string.png "Azure SignalR 连接字符串")

此连接字符串是用于[使用 Visual Studio 2019 部署 Azure Functions](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-functions-app"></a>创建 Azure 函数应用

若要测试示例应用程序，应在 Azure 门户中创建新的 Azure 函数应用。 请记下的**应用名称**根据示例应用程序中使用此 URL **Constants.cs**文件。 下面的屏幕截图演示如何创建名为"xdocsfunctions"新的 Azure 函数应用：

[![创建 Azure 函数应用的屏幕截图](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Azure functions 可以从 Visual Studio 2019 部署到 Azure 函数应用实例。 以下各节介绍了部署到 Azure 函数应用实例的示例应用程序中的两个函数。

### <a name="build-azure-functions-in-visual-studio-2019"></a>生成 Azure Functions 在 Visual Studio 2019

示例应用程序包含名为类库**ChatServer**，其中包括名为的文件中的两个无服务器 Azure Functions **Negotiate.cs**并**Talk.cs**。

`Negotiate`函数将响应 web 请求`SignalRConnectionInfo`对象，其中包含`AccessToken`属性和一个`Url`属性。 移动应用程序使用这些值将自身注册 SignalR hub。 下面的代码演示`Negotiate`函数：

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

`Talk`函数会响应提供 POST 正文中的消息对象的 HTTP POST 请求。 POST 正文转换为`SignalRMessage`并将其转发到 SignalR hub。 下面的代码演示`Talk`函数：

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

若要了解有关 Azure functions 和 Azure 函数应用的详细信息，请参阅[Azure Functions 文档](/azure/azure-functions/)。

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>部署 Azure Functions 与 Visual Studio 2019

Visual Studio 2019，可将函数部署到 Azure 函数应用。 Azure 托管函数简化跨平台测试通过的所有设备提供可访问的测试终结点。

右键单击示例函数应用并选择**发布**启动对话框以发布到 Azure Functions App 的函数。 如果您遵循前面的步骤设置 Azure 函数应用，可以选择**选择现有**示例应用程序发布到 Azure Functions 应用。 下面的屏幕截图显示了发布对话框选项在 Visual Studio 2019:

![在 Visual Studio 2019 发布选项对话框](azure-signalr-images/vs-publish-target.png "在 Visual Studio 2019 中发布选项")

一旦你已登录到你的 Microsoft 帐户，可以找到并选择你作为发布目标的 Azure 函数应用。 下面的屏幕截图显示了示例 Azure 函数应用在 Visual Studio 2019 发布对话框：

![Visual Studio 2019 发布对话框中的 Azure 函数应用](azure-signalr-images/vs-app-selection.png "Visual Studio 2019 发布对话框中的 Azure 函数应用")

选择 Azure 函数应用后显示实例、 站点 URL、 配置和目标 Azure Functions App 的其他信息。 选择**编辑 Azure 应用服务设置**并输入你的连接字符串中**远程**字段。 通过使用的连接字符串**Negotiate**和**通信**函数以连接到 Azure SignalR 服务，可在**密钥**Azure SignalR 的部分在 Azure 门户中的服务。 有关连接字符串的详细信息，请参阅[创建 Azure SignalR 服务](#create-an-azure-signalr-service)。

输入连接字符串后, 你可以单击**发布**将函数部署到 Azure Functions 应用。 完成后，将在 Azure 门户中的 Azure 函数应用中列出函数。 下面的屏幕截图显示了在 Azure 门户中的已发布的函数：

![在 Azure Functions App 中函数发布](azure-signalr-images/azure-functions-deployed.png "函数发布 Azure 函数应用中")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>将 Azure SignalR 服务与 Xamarin.Forms 集成

Azure SignalR 服务和 Xamarin.Forms 应用程序之间的集成是在实例化一个 SignalR 服务类`MainPage`与分配给三个事件的事件处理程序类。 有关这些事件处理程序的详细信息，请参阅[在 Xamarin.Forms 中使用 SignalR 服务类](#use-the-signalr-service-class-in-xamarinforms)。

示例应用程序包括**Constants.cs**必须使用 Azure Functions 应用的 URL 终结点自定义的类。 值设置`HostName`属性设置为你的 Azure 函数应用的地址。 下面的代码演示**Constants.cs**属性的示例`HostName`值：

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
> `Username`示例应用程序中的属性**Constants.cs**文件使用设备的`RuntimePlatform`作为用户名的值。 这样，可以轻松测试设备的跨平台并确定哪些设备正在发送消息。 在实际应用程序中，此值可能会存在一个唯一用户名、 在登录过程中收集注册或登录过程。

### <a name="the-signalr-service-class"></a>SignalR 服务类

`SignalRService`类中**ChatClient**示例应用程序中的项目显示一个实现，调用 Azure 函数应用连接到 Azure SignalR 服务中的函数。

`SendMessageAsync`中的方法`SignalRService`类用于将消息发送到客户端连接到 Azure SignalR 服务。 此方法执行到 HTTP POST 请求**交流**函数托管在 Azure 函数应用，包括 JSON 的序列化`Message`对象作为 POST 负载。 **讨论**函数为广播到所有已连接的客户端将消息传递到 Azure SignalR 服务。 下面的代码演示了 `SendMessageAsync` 方法：

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

`ConnectAsync`中的方法`SignalRService`类执行 HTTP GET 请求到**Negotiate**函数托管在 Azure 函数应用。 **Negotiate**函数将返回到的实例进行反序列化的 JSON`NegotiateInfo`类。 一次`NegotiateInfo`对象进行检索，它用于直接向 Azure SignalR 服务使用的实例注册`HubConnection`类。

ASP.NET Core SignalR 将转换成消息，打开的连接从传入的数据，并允许开发人员定义的消息类型并将事件处理程序绑定到按类型对传入消息。 `ConnectAsync`方法注册的事件处理程序中的示例应用程序定义的消息名称**Constants.cs**文件，它是默认情况下的"newMessage"。

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

`AddNewMessage`方法中的事件处理程序绑定`ConnectAsync`消息，因为在前面的代码所示。 当收到一条消息时，`AddNewMessage`使用为提供的消息数据调用方法`JObject`。 `AddNewMessage`方法将`JObject`的实例`Message`类，然后调用的处理程序`NewMessageReceived`如果其中一个已绑定。 下面的代码演示了 `AddNewMessage` 方法：

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

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>在 Xamarin.Forms 中使用 SignalR 服务类

使用 Xamarin.Forms 中的 SignalR 服务类通过绑定来实现`SignalRService`类中的事件`MainPage`代码隐藏类。

`Connected`中的事件`SignalRService`类 SignalR 连接是否已成功完成时激发。 `ConnectionFailed`中的事件`SignalRService`类在 SignalR 连接失败时被激发。 `SignalR_ConnectionChanged`事件处理程序方法绑定到这两个事件中`MainPage`构造函数。 此事件处理程序更新基于连接的连接和发送按钮状态`success`自变量，并将添加到聊天队列使用的事件所提供的消息`AddMessage`方法。 下面的代码演示`SignalR_ConnectionChanged`事件处理程序方法：

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

`NewMessageReceived`中的事件`SignalRService`类从 Azure SignalR 服务收到一封新邮件时触发。 `SignalR_NewMessageReceived`事件处理程序方法绑定到`NewMessageReceived`中的事件`MainPage`构造函数。 此事件处理程序将为传入`Message`对象转换为字符串，并将其添加到聊天队列使用`AddMessage`方法。 下面的代码演示`SignalR_NewMessageReceived`事件处理程序方法：

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

`AddMessage`方法将添加新的消息作为`Label`到聊天队列对象。 `AddMessage`方法通常称为由事件处理程序从外部主 UI 线程，因此它会强制 UI 更新以防止异常在主线程上发生。 下面的代码演示了 `AddMessage` 方法：

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

如果您具备，则可以 iOS、 Android 和 UWP 上进行测试 SignalR 聊天应用程序：

1. 创建 Azure SignalR 服务。
1. 创建了一个 Azure 函数应用。
1. 自定义**Constants.cs**文件与 Azure Functions 应用终结点。

完成这些步骤并运行应用程序时，单击后**Connect**按钮窗体的与 Azure SignalR 服务的连接。 键入一条消息，然后单击**发送**按钮使其不显示任何聊天队列中的消息中的结果连接的移动应用程序。

## <a name="related-links"></a>相关链接

* [有关使用 Xamarin 和 SignalR 的实时移动应用程序](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [SignalR 简介](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Azure Functions 简介](/azure/azure-functions/functions-overview)
* [Azure Functions 文档](/azure/azure-functions/)
* [MVVM SignalR 聊天示例](https://github.com/lbugnion/sample-xamarin-signalr)
