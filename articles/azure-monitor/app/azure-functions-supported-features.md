---
title: Azure Application Insights. Функции, поддерживаемые в службе "Функции Azure" | Документация Майкрософт
description: Поддерживаемые функции Application Insights для службы "Функции Azure"
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 101c985178b8269b4ff542b94b057330d0c2652a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902253"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights для функций, поддерживаемых в службе "Функции Azure"

Служба "Функции Azure" предлагает [встроенную интеграцию](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) с Application Insights, которая доступна через интерфейс ILogger. Ниже приведен список функций, поддерживаемых в настоящее время. Ознакомьтесь с руководством по службе "Функции Azure", чтобы [приступить к работе](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Поддерживаемые функции

| Функции Azure                       | V1                | Версия 2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Пакет SDK Application Insights для .NET**   | **2.5.0**       | **2.9.1**         |
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

Если проект зависит от пакета SDK Application Insights делать вручную телеметрии трассировки, возможно появление странное поведение вашей конфигурации выборки отличается от функции конфигурации выборки. 

Мы рекомендуем использовать ту же конфигурацию как функции. С помощью **v2 функции**, вы можете получить ту же конфигурацию с помощью внедрения зависимости в конструкторе:

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
