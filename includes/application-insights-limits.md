---
title: включение файла
description: включение файла
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 31ff6ae66b13eb5e5abcde94e8d3df7f6e32f63c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185046"
---
Существуют некоторые ограничения на количество метрики и события каждого приложения, то есть каждого ключа инструментирования. Ограничения зависят от выбранного [ценового плана](https://azure.microsoft.com/pricing/details/application-insights/).

| Ресурс | Ограничение по умолчанию | Примечание
| --- | --- | --- |
| Общий объем данных в день | 100 ГБ | Объем данных можно сократить, задав ограничение. Если требуется больше данных, на портале можно увеличить граничное значение до 1000 ГБ. Для емкостей более 1000 ГБ, отправьте сообщение по адресу AIDataCap@microsoft.com.
| Регулирование | 32 000 событий в секунду | Ограничение измеряется каждую минуту.
| Хранение данных | 90 дней | Этот ресурс используется для [поиска](../articles/azure-monitor/app/diagnostic-search.md), [аналитики](../articles/azure-monitor/app/analytics.md) и [обозревателя метрик](../articles/azure-monitor/app/metrics-explorer.md).
| Хранение подробных результатов [многошагового теста доступности](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) | 90 дней | Этот ресурс предоставляет подробные результаты каждого шага.
| Максимальный размер события | 64 000 |
| Длина имен свойств и метрик | 150 | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Длина строки значения свойства | 8192 | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Длина сообщения трассировки и исключения | 32 768  | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Количество [тестов доступности](../articles/azure-monitor/app/monitor-web-app-availability.md) для одного приложения | 100 |
| Хранение данных [профилировщика](../articles/azure-monitor/app/profiler.md) | 5 дней |
| Отправляемые данные [профилировщика](../articles/azure-monitor/app/profiler.md) в день | 10 ГБ |

Дополнительные сведения см. в статье [Управление ценами и квотами для Application Insights](../articles/azure-monitor/app/pricing.md).