---
title: Azure Application Insights — поддерживаемые функции функций Azure
description: Поддерживаемые функции Application Insights для службы "Функции Azure"
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655656"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights для функций, поддерживаемых в службе "Функции Azure"

Служба "Функции Azure" предлагает [встроенную интеграцию](../../azure-functions/functions-monitoring.md) с Application Insights, которая доступна через интерфейс ILogger. Ниже приведен список функций, поддерживаемых в настоящее время. Ознакомьтесь с руководством по службе "Функции Azure", чтобы [приступить к работе](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

Дополнительные сведения о версиях среды выполнения функций см. [здесь](../../azure-functions/functions-versions.md).

Дополнительные сведения о совместимых версиях Application Insights см. в разделе [зависимости](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Поддерживаемые функции

| Функции Azure                       | V1                | V2 & v3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Автоматический сбор**        |                 |                   |               
| &bull; Запросы                     | Да             | Да               | 
| &bull; Исключения                   | Да             | Да               | 
| &bull; Счетчики производительности         | Да             | Да               |
| &bull; Зависимости                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Да               | 
| &nbsp;&nbsp;&nbsp;&mdash; Служебная шина|                 | Да               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Да               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Да               | 
| | | | 
| **Поддерживаемые функции**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Да             | Да               | 
| &nbsp;&nbsp;&nbsp;&mdash; Безопасный канал управления|                 | Да               | 
| &bull; Выборка                     | Да             | Да               | 
| &bull; Пакет пульса                   |                 | Да               | 
| | | | 
| **Корреляция**                       |                   |                   |               
| &bull; Служебная шина                     |                   | Да               | 
| &bull; EventHub                       |                   | Да               | 
| | | | 
| **Можно настроить**                      |                   |                   |           
| &bull;Полностью настраивается.<br/>Инструкции см. в комментариях [в этой статье](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852).<br/>Сведения обо всех вариантах ASP.NET Core см. в статье [Custom Configuration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) (Настраиваемая конфигурация).               |                   | Да                   | 


## <a name="performance-counters"></a>Счетчики производительности

Автоматический сбор данных счетчиков производительности работает только на компьютерах Windows.


## <a name="live-metrics--secure-control-channel"></a>Интерактивные метрики и безопасный канал управления

Указываемые вами пользовательские критерии фильтра передаются в компонент Live Metrics в пакете SDK для Application Insights. Фильтры могут содержать конфиденциальные сведения, например идентификаторы клиентов. Вы можете защитить канал, используя секретный ключ API. Инструкции см. в разделе [Защита канала управления](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel).

## <a name="sampling"></a>Выборка

Служба "Функции Azure" включает в своей конфигурации выборку по умолчанию. Дополнительные сведения см. в разделе [о настройке выборки](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Если проект использует зависимость от пакета SDK Application Insights для ручного отслеживания телеметрии, то может наблюдаться странное поведение, если конфигурация выборки отличается от конфигурации выборки функций. 

Мы рекомендуем использовать ту же конфигурацию, что и функции. В **функциях версии 2**можно получить ту же конфигурацию с помощью внедрения зависимостей в конструкторе:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
