---
title: Создание и запуск пользовательских тестов доступности с помощью функций Azure
description: Этот док будет охватывать, как создать функцию Azure с TrackAvailability(), которая будет работать периодически в соответствии с конфигурацией, приведенной в функции TimerTrigger. Результаты этого теста будут отправлены на ресурс Application Insights, где вы сможете запросить и предупредить о результатах доступности. Индивидуальные тесты позволят вам писать более сложные тесты доступности, чем это возможно с помощью uI портала, контролировать приложение внутри вашего Azure VNET, изменить адрес конечной точки или создать тест доступности, если он недоступен в вашем регионе.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665805"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Создание и запуск пользовательских тестов доступности с помощью функций Azure

В этой статье будет освещаться, как создать функцию Azure с TrackAvailability(), которая будет периодически работать в соответствии с конфигурацией, приведенной в функции TimerTrigger с вашей собственной бизнес-логикой. Результаты этого теста будут отправлены на ресурс Application Insights, где вы сможете запросить и предупредить о результатах доступности. Это позволяет создавать индивидуальные тесты, аналогичные тому, что вы можете сделать с помощью [мониторинга доступности](../../azure-monitor/app/monitor-web-app-availability.md) на портале. Индивидуальные тесты позволят вам писать более сложные тесты доступности, чем это возможно с помощью uI портала, контролировать приложение внутри вашего Azure VNET, изменить адрес конечной точки или создать тест доступности, даже если эта функция недоступна в вашем регионе.

> [!NOTE]
> Этот пример разработан исключительно для того, чтобы показать вам механику того, как вызов TrackAvailability () API работает в функции Azure. Не как написать базовый код теста HTTP/бизнес-логики, которая потребуется, чтобы превратить это в полнофункциональный тест доступности. По умолчанию, если вы проходите через этот пример, вы будете создавать тест доступности, который всегда будет генерировать сбой.

## <a name="create-timer-triggered-function"></a>Создание функции срабатывания таймер

- Если у вас есть ресурс Insights приложений:
    - По умолчанию Azure Functions создает ресурс Application Insights, но если вы хотите использовать один из уже созданных ресурсов, вам нужно будет указать это во время создания.
    - Следуйте инструкциям о том, как [создать ресурс Azure Functions и функцию Тимер(остановка](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) перед очисткой) со следующими вариантами.
        -  Выберите вкладку **Мониторинг** в верхней части.

            ![ Создание приложения Azure Functions с помощью собственного ресурса App Insights](media/availability-azure-functions/create-function-app.png)

        - Выберите окно выпадения приложений Insights и введите или выберите имя вашего ресурса.

            ![Выбор существующего ресурса Application Insights](media/availability-azure-functions/app-insights-resource.png)

        - Выберите **Обзор и создайте**
- Если у вас еще нет ресурса Application Insights, созданного для функции срабатывания таймера:
    - По умолчанию при создании приложения Azure Functions создается ресурс Application Insights для вас.
    - Следуйте инструкциям о том, как [создать ресурс Azure Functions и функцию срабатывания Timer](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (остановка перед очисткой).

## <a name="sample-code"></a>Образец кода

Скопируйте код ниже в файл run.csx (это заменит уже существующий код). Для этого зайдите в приложение Azure Functions и выберите функцию триггера таймер слева.

>[!div class="mx-imgBorder"]
>![Функция Azure run.csx на портале Azure](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Для адреса конечных точек `EndpointAddress= https://dc.services.visualstudio.com/v2/track`вы бы использовали: . Если ваш ресурс не находится в таком регионе, как Azure Government или Azure China, в этом случае проконсультируйтесь с этой статьей о [переопределение конечных точек по умолчанию](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) и выберите соответствующую конечную точку Telemetry Channel для вашего региона.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
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
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Справа под файлами представления выберите **Добавить**. Вызовите новую **функцию файла.proj** со следующей конфигурацией.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Справа выберите, добавьте. Назовите функцию файла.proj](media/availability-azure-functions/addfile.png)

Справа под файлами представления выберите **Добавить**. Позвоните в новый файл **runAvailabilityTest.csx** со следующей конфигурацией.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Проверка доступности

Чтобы убедиться, что все работает, вы можете посмотреть на график в вкладке Доступность вашего ресурса Application Insights.

> [!NOTE]
> Если вы реализовали свою собственную бизнес-логику в runAvailabilityTest.csx, то вы увидите успешные результаты, как на скриншотах ниже, если вы этого не сделали, то вы увидите неудачные результаты.

>[!div class="mx-imgBorder"]
>![Вкладка доступности с успешными результатами](media/availability-azure-functions/availtab.png)

При настройке теста с помощью Функций Azure, которые вы заметите, что в отличие от использования **теста Add** во вкладке «Доступность» имя теста не будет отображаться, и вы не сможете взаимодействовать с ним. Результаты визуализированы, но вы получаете сводное представление вместо того же подробного представления, что и при создании теста доступности через портал.

Чтобы увидеть сквозные детали транзакции, выберите **Успешный** или **Неудачный** под сверло в, а затем выберите образец. Вы также можете получить до конца-к-конец детали транзакции, выбрав точку данных на графике.

>[!div class="mx-imgBorder"]
>![Выберите тест доступности образца](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Детали сквозной транзакции](media/availability-azure-functions/end-to-end.png)

Если вы запустили все как есть (без добавления бизнес-логики), то вы увидите, что тест не удался.

## <a name="query-in-logs-analytics"></a>Запрос в журналах (аналитика)

Для просмотра результатов доступности, зависимостей и т.д. можно использовать журналы (аналитику). Чтобы узнать больше о журналах, посетите [обзор запроса журнала](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Результаты доступности](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Зависимости](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
- [Диагностика транзакции](../../azure-monitor/app/transaction-diagnostics.md)
