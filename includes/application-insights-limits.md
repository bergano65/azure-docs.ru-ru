---
title: включение файла
description: включение файла
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 7bf9c338315f5013be815625b06d633567a801a0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912476"
---
Существует ряд ограничений на количество метрик и событий для каждого приложения, то есть ключа инструментирования. Ограничения зависят от выбранного [ценового плана](https://azure.microsoft.com/pricing/details/application-insights/).

| Resource | Ограничение по умолчанию | Примечание
| --- | --- | --- |
| Общий объем данных в день | 100 ГБ | Объем данных можно сократить, задав ограничение. Если требуется больше данных, на портале можно увеличить граничное значение до 1000 ГБ. Для емкости, превышающей 1 000 ГБ, отправьте сообщение AIDataCap@microsoft.comпо адресу.
| Регулирование | 32 000 событий в секунду | Ограничение измеряется каждую минуту.
| Хранение данных | 90 дней | Этот ресурс используется для [поиска](../articles/azure-monitor/app/diagnostic-search.md), [аналитики](../articles/azure-monitor/app/analytics.md) и [обозревателя метрик](../articles/azure-monitor/app/metrics-explorer.md).
| Хранение подробных результатов [многошагового теста доступности](../articles/azure-monitor/app/availability-multistep.md) | 90 дней | Этот ресурс предоставляет подробные результаты каждого шага.
| Максимальный размер элемента телеметрии | 64 КБ |
| Максимальное число элементов телеметрии в пакете | 64 000 |
| Длина имен свойств и метрик | 150 | См. [схемы типов](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Длина строки значения свойства | 8192  | См. [схемы типов](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Длина сообщения трассировки и исключения | 32 768  | См. [схемы типов](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Количество [тестов доступности](../articles/azure-monitor/app/monitor-web-app-availability.md) для одного приложения | 100 |
| Хранение данных [профилировщика](../articles/azure-monitor/app/profiler.md) | 5 дней |
| Отправляемые данные [профилировщика](../articles/azure-monitor/app/profiler.md) в день | 10 GB |

Дополнительные сведения см. в статье [Управление ценами и квотами для Application Insights](../articles/azure-monitor/app/pricing.md).