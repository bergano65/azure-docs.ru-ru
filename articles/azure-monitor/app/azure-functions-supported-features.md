---
title: Azure Application Insights. Функции, поддерживаемые в службе "Функции Azure" | Документация Майкрософт
description: Поддерживаемые функции Application Insights для службы "Функции Azure"
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 46e7f5b5376622be9a05265942b6aceb38feebac
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372442"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights для функций, поддерживаемых в службе "Функции Azure"

Служба "Функции Azure" предлагает [встроенную интеграцию](../../azure-functions/functions-monitoring.md) с Application Insights, которая доступна через интерфейс ILogger. Ниже приведен список функций, поддерживаемых в настоящее время. Ознакомьтесь с руководством по службе "Функции Azure", чтобы [приступить к работе](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

## <a name="supported-features"></a>Поддерживаемые функции

| Функции Azure                       | V1                | Версия 2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Пакет SDK Application Insights для .NET**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Автоматический сбор**        |                 |                   |               
| &bull; Запросы                     | ДА             | ДА               | 
| &bull; Исключения                   | ДА             | ДА               | 
| &bull; Счетчики производительности         | ДА             | ДА               |
| &bull; Зависимости                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | ДА               | 
| &nbsp;&nbsp;&nbsp;&mdash; Служебная шина|                 | ДА               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | ДА               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | ДА               | 
| | | | 
| **Поддерживаемые функции**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | ДА             | ДА               | 
| &nbsp;&nbsp;&nbsp;&mdash; Безопасный канал управления|                 | ДА               | 
| &bull; Выборка                     | ДА             | ДА               | 
| &bull; Пакет пульса                   |                 | ДА               | 
| | | | 
| **Корреляция**                       |                   |                   |               
| &bull; Служебная шина                     |                   | ДА               | 
| &bull; EventHub                       |                   | ДА               | 
| | | | 
| **Можно настроить**                      |                   |                   |           
| &bull;Полностью настраивается.<br/>Инструкции см. в комментариях [в этой статье](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852).<br/>Сведения обо всех вариантах ASP.NET Core см. в статье [Custom Configuration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) (Настраиваемая конфигурация).               |                   | ДА                   | 


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
