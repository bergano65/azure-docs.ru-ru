---
title: Как выполнять устойчивых функций веб-заданий Azure
description: Узнайте, как программировать и настраивать запуск устойчивых функций в веб-заданиях с помощью пакета SDK для веб-заданий.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 39a757900b4307d702a0ce0ce1c20694418aa8dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65872820"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Как выполнять устойчивых функций веб-заданий

По умолчанию устойчивых функций использует среда выполнения функций Azure для взаимодействия узла. Тем не менее могут существовать определенные сценарии, где требуется дополнительный контроль над кодом, который прослушивает события. В этой статье показано, как реализовать согласование с помощью пакета SDK веб-заданий. Более подробное сравнение между функциями и веб-заданий см. в разделе [сравнение функций и веб-задания](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

Расширения [Функций Azure](../functions-overview.md) и [устойчивых функций](durable-functions-overview.md) созданы на основе [пакета SDK для веб-заданий](../../app-service/webjobs-sdk-how-to.md). Узел заданий в пакете SDK веб-заданий — это среда выполнения в функциях Azure. Если вам нужно управлять способами, недоступных для функций Azure, можно разрабатывать и выполнять устойчивых функций с помощью пакета SDK веб-заданий, самостоятельно.

В версии 3.x пакета SDK веб-заданий, узел представляет собой реализацию `IHost`и в версии 2.x, использовании `JobHost` объекта.

Привязываемое примером устойчивой функции доступна в версии 2.x SDK веб-заданий: скачайте или клонируйте [репозиторий устойчивых функций](https://github.com/azure/azure-functions-durable-extension/)и перейдите к *примеры\\webjobssdk\\цепочки* папки.

## <a name="prerequisites"></a>Технические условия

В этой статье предполагается, что вы знакомы с основами пакета SDK для веб-заданий, разработки библиотеки классов C# для Функций Azure и устойчивых функций. Ознакомиться с этими темами вам помогут следующие ресурсы:

* [Начало работы с пакетом SDK WebJobs](../../app-service/webjobs-sdk-get-started.md)
* [Создание первой функции с помощью Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Цепочки функций в устойчивых функциях — пример последовательности Hello](durable-functions-sequence.md)

Чтобы выполнить действия, описанные в этой статье, сделайте следующее:

* [Установка Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) с **разработки Azure** рабочей нагрузки.

  Если вы уже установлена среда Visual Studio, но у этой рабочей нагрузки, добавить рабочую нагрузку, выбрав **средства** > **получить средства и компоненты**.

  (Вместо этого можно использовать [Visual Studio Code](https://code.visualstudio.com/), но некоторые инструкции относятся к Visual Studio.)

* Установите и запустите [эмулятор хранилища Azure](../../storage/common/storage-use-emulator.md) версии 5.2 или выше. Альтернативой является добавление в файл *App.config* строки подключения к службе хранилища Azure.

## <a name="webjobs-sdk-versions"></a>Версии пакета SDK для веб-заданий

В этой статье объясняется, как разрабатывать проект пакета SDK для веб-заданий версии 2.x (эквивалентен Функциям Azure версии 1.x). Дополнительные сведения о версии 3.x см. в разделе [Пакет SDK для веб-заданий версии 3.x](#webjobs-sdk-3x) далее в этой статье.

## <a name="create-a-console-app"></a>Создание консольного приложения

Для выполнения устойчивых функций в качестве веб-заданий, необходимо сначала создать консольное приложение. Проект пакета SDK для веб-заданий — это просто проект консольного приложения с установленными соответствующими пакетами NuGet.

В Visual Studio **новый проект** выберите **классический рабочий стол Windows** > **консольное приложение (.NET Framework)** . В файле проекта `TargetFrameworkVersion` должна иметь значение `v4.6.1`.

Visual Studio также есть шаблон проекта веб-задания, который можно использовать, выбрав **Cloud** > **веб-задание Azure (.NET Framework)** . Этот шаблон устанавливает множество пакетов, некоторые из которых могут не потребоваться.

## <a name="install-nuget-packages"></a>Установка пакетов Nuget

Вам потребуются пакеты NuGet для пакета SDK для веб-заданий, основные привязки, платформа ведения журналов и расширение устойчивых задач. Ниже приведены **консоль диспетчера пакетов** команд для пакетов, начиная с даты, эта статья написана номерам последняя стабильная версия:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Вам также понадобятся регистраторы. Следующие команды установки поставщика Azure Application Insights и `ConfigurationManager`. `ConfigurationManager` позволяет получить ключ инструментирования Application Insights из параметров приложения.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Следующая команда устанавливает поставщик консоли:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Код JobHost

Создать консольное приложение и установить пакеты NuGet необходимо, вы будете готовы использовать устойчивые функции. Вы можете сделать это с помощью кода JobHost.

Чтобы использовать расширение устойчивых функций, вызовите `UseDurableTask` для объекта `JobHostConfiguration` в вашем методе `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Список свойств, которые можно задать в объекте `DurableTaskExtension`, см. в файле [host.json](../functions-host-json.md#durabletask).

Метод `Main` служит также местом для настройки регистраторов. В следующем примере настраивается в консоли и поставщики Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Функции Azure

Устойчивые функции в контексте веб-заданий отличается от устойчивые функции в контексте функций Azure. Это важно учитывать различия при написании кода.

Пакет SDK для веб-заданий не поддерживает следующие возможности Функций Azure:

* [Атрибут FunctionName](#functionname-attribute)
* [триггером HTTP](#http-trigger)
* [API управления HTTP устойчивых функций](#http-management-api)

### <a name="functionname-attribute"></a>Атрибут FunctionName

В проекте пакета SDK для веб-заданий имя метода функции является именем функции. Атрибут `FunctionName` используется только в Функциях Azure.

### <a name="http-trigger"></a>Триггер HTTP

В пакете SDK для веб-заданий нет триггера HTTP. Клиент оркестрации в примере проекта использует триггер таймера:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API управления HTTP

Так как пакет SDK для веб-заданий не содержит ни одного триггера HTTP, в нем нет [API управления HTTP](durable-functions-http-api.md).

В проекте веб-заданий SDK можно вызывать методы в объекте клиента оркестрации, вместо путем отправки HTTP-запросов. Следующие методы соответствуют трем задачам, которые можно выполнить с помощью API управления HTTP.

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Функции клиента оркестрации в образце проекта запускается функция оркестратора и затем переходит в цикл, который вызывает `GetStatusAsync` каждые 2 секунды:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Запуск примера

Вы получили устойчивых функций, настроить для запуска в качестве веб-задания, и теперь у вас есть представление о том, как это будет отличаться от выполнения устойчивых функций как автономные функции Azure. На этом этапе увидеть его в деле выборки может быть полезно.

Этот раздел содержит общие сведения о способах запуска [примера проекта](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Подробные инструкции о том, как запустить проект пакета SDK для веб-заданий локально и развернуть его в веб-задание Azure, см. в разделе [Развертывание в качестве WebJob (веб-задания)](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Локальный запуск

1. Запустите эмулятор хранилища (см. раздел [Предварительные требования](#prerequisites)).

1. Если вы хотите просмотреть журналы в Application Insights при локальном выполнении проекта:

    a. Создайте ресурс Application Insights и используйте **Общие** тип приложения для него.

    2\. Сохраните ключ инструментирования в файл *App.config*.

1. Запустите проект.

### <a name="run-in-azure"></a>Запуск в Azure

1. Создайте веб-приложение и учетную запись хранения.

1. В веб-приложения, сохраните строку подключения к хранилищу в параметре приложения `AzureWebJobsStorage`.

1. Создайте ресурс Application Insights и используйте **Общие** тип приложения для него.

1. Сохраните ключ инструментирования в параметре приложения `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Разверните приложение в качестве веб-задания.

## <a name="webjobs-sdk-3x"></a>Пакет SDK для веб-заданий версии 3.x

В этой статье объясняется, как в процессе разработки проекта веб-заданий SDK 2.x. Если вы разрабатываете [веб-заданий SDK 3.x](../../app-service/webjobs-sdk-get-started.md) проекта, этот раздел поможет вам понять различия.

Основное изменение появился является использование .NET Core вместо .NET Framework. Создание проекта веб-заданий SDK 3.x, инструкции одинаковы, со следующими исключениями:

1. Создайте консольное приложение .NET Core. В Visual Studio **новый проект** выберите **.NET Core** > **консольное приложение (.NET Core)** . Файл проекта указывает, что `TargetFramework` является `netcoreapp2.x`.

1. Выбор версии пакета SDK для веб-заданий 3.x от следующих пакетов:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Задайте строку подключения к хранилищу и ключ инструментирования Application Insights в *appsettings.json* файла с помощью конфигурации платформы .NET Core. Ниже приведен пример:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Изменение `Main` код метода для этого. Ниже приведен пример:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о пакете SDK для веб-заданий см. в статье [Использование пакета SDK WebJobs для фоновой обработки управления событиями](../../app-service/webjobs-sdk-how-to.md).
