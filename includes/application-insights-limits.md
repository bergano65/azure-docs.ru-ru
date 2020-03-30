---
title: включить файл
description: включить файл
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69982610"
---
Существуют некоторые ограничения на количество метрик и событий в приложении, то есть на клавишу приборов. Ограничения зависят от выбранного [ценового плана](https://azure.microsoft.com/pricing/details/application-insights/).

| Ресурс | Ограничение по умолчанию | Примечание
| --- | --- | --- |
| Общий объем данных в день | 100 ГБ | Объем данных можно сократить, задав ограничение. Если требуется больше данных, на портале можно увеличить граничное значение до 1000 ГБ. Для емкости больше чем 1.000 AIDataCap@microsoft.comGB, послание электронной почте к .
| Регулирование | 32 000 событий в секунду | Ограничение измеряется каждую минуту.
| Хранение данных | [от 30 до 730 дней](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | Этот ресурс используется для [поиска](../articles/azure-monitor/app/diagnostic-search.md), [аналитики](../articles/azure-monitor/app/analytics.md) и [обозревателя метрик](../articles/azure-monitor/app/metrics-explorer.md).
| Хранение подробных результатов [многошагового теста доступности](../articles/azure-monitor/app/availability-multistep.md) | 90 дней | Этот ресурс предоставляет подробные результаты каждого шага.
| Максимальный размер элемента телеметрии | 64 КБ |
| Максимальная телеметрия на партию | 64 000 |
| Длина имен свойств и метрик | 150 | См. [схемы типов](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Длина строки значения свойства | 8192  | См. [схемы типов](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Длина сообщения трассировки и исключения | 32,768  | См. [схемы типов](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Количество [тестов доступности](../articles/azure-monitor/app/monitor-web-app-availability.md) для одного приложения | 100 |
| Хранение данных [профилировщика](../articles/azure-monitor/app/profiler.md) | 5 дней |
| Отправляемые данные [профилировщика](../articles/azure-monitor/app/profiler.md) в день | 10 ГБ |

Дополнительные сведения см. в статье [Управление ценами и квотами для Application Insights](../articles/azure-monitor/app/pricing.md).