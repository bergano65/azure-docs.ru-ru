---
title: Создание и запуск пользовательских тестов доступности с помощью функций Azure
description: В этом документе рассматривается создание функции Azure с TrackAvailability (), которая будет выполняться периодически в соответствии с конфигурацией, заданной в функции TimerTrigger. Результаты этого теста будут отправлены в ресурс Application Insights, где вы сможете запрашивать данные о результатах доступности и получать оповещения о них. Настроенные тесты позволяют создавать более сложные тесты доступности, чем возможно с помощью пользовательского интерфейса портала, мониторинга приложения в виртуальной сети Azure, изменения адреса конечной точки или создания теста доступности, если он недоступен в вашем регионе.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: 900228e1f9bdf9d367fa37b9ec90a6148faec656
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880248"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Создание и запуск пользовательских тестов доступности с помощью функций Azure

В этой статье рассматривается создание функции Azure с TrackAvailability (), которая будет выполняться периодически в соответствии с конфигурацией, заданной в функции TimerTrigger. Результаты этого теста будут отправлены в ресурс Application Insights, где вы сможете запрашивать данные о результатах доступности и получать оповещения о них. Это позволяет создавать настраиваемые тесты, аналогичные тому, что можно сделать с помощью [мониторинга доступности](../../azure-monitor/app/monitor-web-app-availability.md) на портале. Настроенные тесты позволяют создавать более сложные тесты доступности, чем возможно с помощью пользовательского интерфейса портала, мониторинга приложения в виртуальной сети Azure, изменения адреса конечной точки или создания теста доступности, даже если эта функция недоступна в вашем регионе.


## <a name="create-timer-triggered-function"></a>Создание функции, активируемой с таймером

- Если у вас есть Application Insights ресурс:
    - По умолчанию функции Azure создают ресурс Application Insights, но если вы хотите использовать один из уже созданных ресурсов, необходимо указать это во время создания.
    - Следуйте инструкциям, чтобы [создать ресурс функций Azure и функцию, активируемую с помощью таймера](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) ("закончить перед очисткой") со следующими параметрами.
        -  Щелкните раздел Application Insights перед нажатием кнопки **создать**.

            ![ Создание приложения "функции Azure" с собственным ресурсом App Insights](media/availability-azure-functions/create-function-app.png)

        - Щелкните **выбрать существующий ресурс** и введите имя ресурса. Нажмите кнопку **Применить** .

            ![Выбор существующего Application Insights ресурса](media/availability-azure-functions/app-insights-resource.png)

        - Нажмите кнопку **Создать**
- Если у вас еще нет созданного ресурса Application Insights для функции, активируемой с помощью таймера:
    - По умолчанию при создании приложения "функции Azure" будет создан ресурс Application Insights.
    - Следуйте инструкциям, чтобы [создать ресурс функций Azure и функцию, активируемую с помощью таймера](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (до завершения очистки).

## <a name="sample-code"></a>Пример кода

Скопируйте приведенный ниже код в файл run. CSX (это заменит уже существующий код). Для этого перейдите в приложение функций Azure и выберите функцию триггера таймера слева.

![Функция Azure Run. CSX в портал Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Для адреса конечной точки, который будет использоваться: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Если ресурс не находится в регионе, например Azure для государственных организаций или Azure для Китая, в этом случае обратитесь к этой статье, чтобы узнать о [переопределении конечных точек по умолчанию](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) и выбрать соответствующую конечную точку канала телеметрии для вашего региона.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        //throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

Справа в разделе Просмотр файлов выберите **Добавить**. Вызовите новый файл **Function. proj** со следующей конфигурацией.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![Справа выберите Добавить. Назовите файл function. proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Проверить доступность

Чтобы убедиться, что все работает, можно просмотреть диаграмму на вкладке доступность Application Insights ресурса.

![Вкладка "доступность" с успешными результатами](media/availability-azure-functions/availtab.png)

При настройке теста с помощью функций Azure вы заметите, что в отличие от использования команды **добавить тест** на вкладке доступность имя теста не отображается, и вы не сможете взаимодействовать с ним. Результаты являются визуально, но вы получаете представление сводки, а не то же подробное представление, которое вы получаете при создании теста доступности с помощью портала.

Чтобы просмотреть сведения о сквозной транзакции, выберите **успех** или **сбой** в разделе Детализация, а затем выберите пример. Можно также перейти к подробным сведениям о транзакции, выбрав на диаграмме точку данных.

![Выбор примера теста доступности](media/availability-azure-functions/sample.png)

![Сведения о сквозной транзакции](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Запрос в журналах (аналитика)

С помощью журналов (Analytics) можно просматривать результаты доступности, зависимости и многое другое. Дополнительные сведения о журналах см. в статье [Обзор запросов журналов](../../azure-monitor/log-query/log-query-overview.md).

![Результаты доступности](media/availability-azure-functions/availabilityresults.png)

![Зависимости](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
- [Диагностика транзакций](../../azure-monitor/app/transaction-diagnostics.md)
