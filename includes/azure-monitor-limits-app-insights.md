---
title: Включить имя файла
description: включить файл
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 32a04518d3cd097a02ec3045da891237fc0e405a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334909"
---
Существует ряд ограничений на количество метрик и событий для каждого приложения, то есть ключа инструментирования. Ограничения зависят от выбранного [ценового плана](https://azure.microsoft.com/pricing/details/application-insights/).

| Ресурс | Ограничение | Примечание
| --- | --- | --- |
| Общий объем данных в день | 100 ГБ | Объем данных можно сократить, задав ограничение. Если требуется больше данных, на портале можно увеличить граничное значение до 1000 ГБ. Для емкости, превышающей 1 000 ГБ, отправьте сообщение по адресу AIDataCap@microsoft.com .
| Регулирование | 32 000 событий в секунду | Ограничение измеряется каждую минуту.
| Хранение данных | 90 дней | Этот ресурс используется для [поиска](../articles/azure-monitor/app/diagnostic-search.md), [аналитики](../articles/azure-monitor/app/analytics.md) и [обозревателя метрик](../articles/azure-monitor/app/metrics-explorer.md).
| Хранение подробных результатов [многошагового теста доступности](../articles/azure-monitor/app/availability-multistep.md) | 90 дней | Этот ресурс предоставляет подробные результаты каждого шага.
| Максимальный размер события | 64 000 000 байт |
| Длина имен свойств и метрик | 150 | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Длина строки значения свойства | 8192 | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Длина сообщения трассировки и исключения | 32,768  | См. [схемы типов](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Количество [тестов доступности](../articles/azure-monitor/app/monitor-web-app-availability.md) для одного приложения | 100 |
| Хранение данных [профилировщика](../articles/azure-monitor/app/profiler.md) | 5 дней |
| Отправляемые данные [профилировщика](../articles/azure-monitor/app/profiler.md) в день | 10 ГБ |

Дополнительные сведения см. в статье [Управление ценами и квотами для Application Insights](../articles/azure-monitor/app/pricing.md).
