---
title: Android 作业计划程序
description: 本指南讨论如何使用 Android 作业计划程序 API 计划后台工作。
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 4d28b80b32ff0d20afbe643d9c000f301a8ea582
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027810"
---
# <a name="android-job-scheduler"></a>Android 作业计划程序

_本指南讨论如何使用 Android 作业计划程序 API （在运行 Android 5.0 （API 级别21）及更高版本的 Android 设备上提供）来计划后台工作。_

## <a name="overview"></a>概述 

使 Android 应用程序对用户进行响应的最佳方式之一是确保在后台执行复杂的或长时间运行的工作。 但重要的是，后台工作不会对用户对设备的体验产生负面影响。 

例如，后台作业可能每三或四分钟轮询一次网站，以查询特定数据集的更改。 这似乎是良性的，但会对电池寿命产生灾难性影响。 应用程序将重复唤醒设备，将 CPU 提升到较高的电源状态，启动无线电收发器，使网络请求，然后处理结果。 这种情况更糟，因为设备不会立即断电并返回到低功耗空闲状态。 计划不良的后台工作可能会意外地使设备处于一种不必要和过多电源要求的状态。 此看似合法的活动（轮询网站）会使设备在相对较短的时间内不可用。

Android 提供以下 Api，可帮助在后台执行工作，但其本身并不是用于智能作业计划的。 

- **[意向服务](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; 意向服务非常适合用于执行工作，不过，它们无法计划工作。
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; 这些 api 只允许计划工作，但不能实际执行工作。 此外，AlarmManager 只允许基于时间的约束，这意味着在特定时间或经过一段时间后发出警报。 
- Android 应用 &ndash; 的 **[广播接收](~/android/app-fundamentals/broadcast-receivers.md)** 方可以设置广播接收器，以便在响应系统范围内的事件或意向时执行工作。 但是，广播接收器不提供对作业运行时间的任何控制。 同时，Android 操作系统中的更改将限制广播接收方的工作时间或它们可以响应的工作类型。 

有效地执行后台工作（有时称为_后台作业_或_作业_）有两个主要功能：

1. **智能地计划工作**&ndash; 重要的是，当应用程序在后台执行工作时，这一点非常重要。 理想情况下，应用程序不应要求运行作业。 相反，应用程序应指定在作业可以运行时必须满足的条件，然后使用满足条件时将执行工作的操作系统计划该作业。 这允许 Android 运行作业，以确保设备上的最高效率。 例如，可能会批处理网络请求同时运行全部，以充分利用网络所涉及的开销。
2. **封装工作**&ndash; 用于执行后台工作的代码应封装在独立于用户界面的离散组件中，如果无法完成某些任务的工作，则可以相对容易地重新安排这些工作在于.

Android 作业计划程序是一种内置于 Android 操作系统的框架，可提供 Fluent API 来简化计划后台工作。  Android 作业计划程序包含以下类型：

- `Android.App.Job.JobScheduler` 是一种系统服务，用于计划、执行和代表 Android 应用程序取消作业。
- `Android.App.Job.JobService` 是必须使用将在应用程序的主线程上运行作业的逻辑进行扩展的抽象类。 这意味着 `JobService` 负责以异步方式执行工作。
- `Android.App.Job.JobInfo` 对象包含在作业应运行时指导 Android 的条件。

若要计划使用 Android 作业计划程序，Xamarin Android 应用程序必须将扩展 `JobService` 类的类中的代码封装。 `JobService` 具有三种可在作业的生存期内调用的生命周期方法：

- **Bool OnStartJob （JobParameters parameters）** &ndash; 此方法由 `JobScheduler` 调用以执行工作，并在应用程序的主线程上运行。 `JobService` 需要以异步方式执行工作，并在剩余工作时 `true`，或在完成工作时 `false`。
    
    当 `JobScheduler` 调用此方法时，它将在作业期间请求并保留 Android 中的 wakelock。 当作业完成时，`JobService` 通过调用 `JobFinished` 方法（如下所述）来告知此事实的 `JobScheduler`。

- **JobFinished （JobParameters parameters，Bool needsReschedule）** &ndash; 必须由 `JobService` 调用此方法，以通知 `JobScheduler` 工作完成。 如果未调用 `JobFinished`，`JobScheduler` 将不会删除 wakelock，从而导致不必要的电池排出。 

- **Bool OnStopJob （JobParameters parameters）** &ndash; 在 Android 提前停止作业时调用此。 如果应根据重试条件重新安排作业（更详细地讨论），则应返回 `true`。

可以指定_约束_或_触发器_，以便控制作业的运行时间和运行时间。 例如，可以限制作业，使其仅在设备充电时运行，或在拍摄照片时启动作业。

本指南将详细讨论如何实现 `JobService` 类并使用 `JobScheduler`进行安排。

## <a name="requirements"></a>要求

Android 作业计划程序需要 Android API 级别21（Android 5.0）或更高版本。 

## <a name="using-the-android-job-scheduler"></a>使用 Android 作业计划程序

使用 Android JobScheduler API 有三个步骤：

1. 实现 JobService 类型以封装工作。
2. 使用 `JobInfo.Builder` 对象创建 `JobInfo` 对象，该对象将包含用于运行作业的 `JobScheduler` 的条件。 
3. 使用 `JobScheduler.Schedule`安排作业。

### <a name="implement-a-jobservice"></a>实现 JobService

Android 作业计划程序库执行的所有工作都必须在一个扩展 `Android.App.Job.JobService` 抽象类的类型中完成。 创建 `JobService` 非常类似于使用 Android framework 创建 `Service`： 

1. 扩展 `JobService` 类。
2. 使用 `ServiceAttribute` 修饰子类，并将 `Name` 参数设置为一个字符串，该字符串由包名称和类名组成（请参见以下示例）。
3. 将 `ServiceAttribute` 上的 `Permission` 属性设置为字符串 `android.permission.BIND_JOB_SERVICE`。
4. 重写 `OnStartJob` 方法，并添加用于执行工作的代码。 Android 将在应用程序的主线程上调用此方法以运行作业。 需要在线程上执行几毫秒时间，以避免阻塞应用程序的工作。
5. 完成工作后，`JobService` 必须调用 `JobFinished` 方法。 此方法是 `JobService` 告诉 `JobScheduler` 完成工作的方式。 未能调用 `JobFinished` 将导致 `JobService` 在设备上施加不必要的需求，缩短电池寿命。 
6. 最好还是重写 `OnStopJob` 方法。 此方法由 Android 在作业完成之前关闭，并为 `JobService` 提供了正确释放任何资源的机会。 如果需要重新计划作业，则此方法应返回 `true`，如果不 desireable 重新运行作业，则应 `false`。

下面的代码示例是应用程序的最简单的 `JobService`，使用 TPL 异步执行一些工作：

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>创建 JobInfo 以计划作业

Xamarin Android 应用程序不直接实例化 `JobService`，而是将 `JobInfo` 对象传递到 `JobScheduler`。 `JobScheduler` 将实例化请求的 `JobService` 对象，根据 `JobInfo`中的元数据来计划和运行 `JobService`。 `JobInfo` 对象必须包含以下信息：

- **JobId** &ndash; 这是用于标识 `JobScheduler`作业的 `int` 值。 重复使用此值将更新所有现有作业。 对于应用程序，该值必须是唯一的。 
- **JobService** &ndash; 此参数是显式标识 `JobScheduler` 用于运行作业的类型的 `ComponentName`。 

此扩展方法演示如何使用 Android `Context`创建 `JobInfo.Builder`，例如活动：

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Android 作业计划程序的一项强大功能是能够控制作业的运行时间或在什么条件下运行作业。 下表介绍 `JobInfo.Builder` 上的一些方法，这些方法允许应用程序在作业运行时影响：  

|  方法 | 说明   |
|---|---|
| `SetMinimumLatency`  | 指定在运行作业之前应观察的延迟（以毫秒为单位）。 |
| `SetOverridingDeadline`  | 声明作业在此时间之前必须运行（以毫秒为单位）。 |
| `SetRequiredNetworkType`  | 指定作业的网络要求。 |
| `SetRequiresBatteryNotLow` | 仅当设备不向用户显示 "电池电量不足" 警告时，作业才能运行。 |
| `SetRequiresCharging` | 作业只能在电池充电时运行。 |
| `SetDeviceIdle` | 当设备忙时将运行该作业。 |
| `SetPeriodic` | 指定应定期运行作业。 |
| `SetPersisted` | 作业应跨设备重新启动 perisist。 | 

`SetBackoffCriteria` 提供了有关 `JobScheduler` 在尝试再次运行作业之前应等待的时间的一些指导。 回退条件有两个部分：延迟（以毫秒为单位，默认值为30秒）和应使用的后端类型（有时称为 "_回退策略_" 或 "_重试策略_"）。 这两个策略封装在 `Android.App.Job.BackoffPolicy` 枚举中：

- `BackoffPolicy.Exponential` &ndash; 指数回退策略会在每次发生故障后以指数方式增加初始回退值。 第一次作业失败时，库将等待在重新计划作业之前指定的初始间隔–示例30秒。 第二次作业失败时，库将等待至少60秒，然后再尝试运行作业。 第三次尝试失败后，库将等待120秒，依此类推。 这是默认值。
- `BackoffPolicy.Linear` &ndash; 此策略是一种线性回退，应将作业重新安排为按设置的时间间隔运行（直到成功）。 线性回退最适用于必须尽快完成的工作，或者用于快速解决自身问题的问题。 

有关创建 `JobInfo` 对象的更多详细信息，请参阅[Google 的有关 `JobInfo.Builder` 类的文档](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html)。

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>通过 JobInfo 将参数传递给作业

通过创建与 `Job.Builder.SetExtras` 方法一起传递的 `PersistableBundle`，将参数传递给作业：

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

`PersistableBundle` 是通过 `JobService`的 `OnStartJob` 方法中的 `Android.App.Job.JobParameters.Extras` 属性访问的：

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>计划作业

若要计划作业，Xamarin Android 应用程序将获取对 `JobScheduler` 系统服务的引用，并使用上一步中创建的 `JobInfo` 对象调用 `JobScheduler.Schedule` 方法。 `JobScheduler.Schedule` 将立即返回以下两个整数值之一：

- **JobScheduler. ResultSuccess** &ndash; 已成功安排作业。 
- **JobScheduler. ResultFailure** &ndash; 无法计划作业。 这通常是由 `JobInfo` 参数冲突引起的。

此代码是计划作业并向用户通知计划尝试结果的示例：

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
    snackBar.Show();
}
else
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
    snackBar.Show();
}
```

### <a name="cancelling-a-job"></a>取消作业

可以取消已计划的所有作业，或者只是使用 `JobsScheduler.CancelAll()` 方法或 `JobScheduler.Cancel(jobId)` 方法的单个作业：

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>总结

本指南讨论了如何使用 Android 作业计划程序在后台智能地执行工作。 本指南讨论了如何封装要作为 `JobService` 执行的工作，以及如何使用 `JobScheduler` 来计划工作、使用 `JobTrigger` 指定条件以及如何使用 `RetryStrategy`处理失败。

## <a name="related-links"></a>相关链接

- [智能作业-计划](https://developer.android.com/topic/performance/scheduling.html)
- [JobScheduler API 参考](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [使用 JobScheduler 按 pro 安排作业](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android 电池和内存优化-Google i/o 2016 （视频）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
