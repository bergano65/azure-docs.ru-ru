---
title: включить файл
description: включить файл
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: f097479b67a7b48eca4a2710db3bd7eed6ddc982
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013821"
---
Ниже приводится краткое изложение ключевых пределов общей доступности.

### <a name="sku-ingress-rates-and-capacities"></a>SKU входные ставки и мощности

S1 и S2 SKU встраивая ставки и емкости обеспечивают гибкость при настройке новой среды Time Series Insights.

| Емкость S1 SKU | Коэффициент входа | Максимальная емкость хранилища
| --- | --- | --- |
| 1 | 1 ГБ (1 млн событий) | 30 ГБ в месяц (30 млн событий) |
| 10 | 10 ГБ (10 млн событий) | 300 ГБ в месяц (300 млн событий) |

| Емкость S2 SKU | Коэффициент входа | Максимальная емкость хранилища
| --- | --- | --- |
| 1 | 10 ГБ (10 млн событий) | 300 ГБ в месяц (300 млн событий) |
| 10 | 100 ГБ (100 млн событий) | 3 ТБ в месяц (3 млрд событий) |

> [!NOTE]
> Емкость масштабируется линейно, поэтому номер SKU S1 с емкостью 2 поддерживает скорость приема 2 ГБ (2 млн событий) в неделю и 60 ГБ (60 млн событий) в месяц.

Среды S2 SKU поддерживают значительно больше событий в месяц и имеют значительно более высокую пропускную способность.

| номер SKU  | Количество событий в месяц  | Размер события в месяц  | Количество событий в минуту | Размер события в минуту  |
|---------|---------|---------|---------|---------|
| S1     |   30 млн     |  30 ГБ     |  720    |  720 КБ   |
 |S2     |   300 млн    |   300 ГБ   | 7200   | 7200 КБ  |

### <a name="property-limits"></a>Ограничения свойств

Ограничения свойств GA зависят от выбранной среды SKU. Поставляемые свойства событий имеют соответствующие столбцы JSON, CSV и диаграммы, которые можно просматривать в [Explorer Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)

| номер SKU | Максимальные свойства |
| --- | --- |
| S1 | 600 свойств (столбцы) |
| S2 | 800 свойств (столбцов) |

### <a name="event-sources"></a>Источники событий

Поддерживается максимум два источника событий в каждом экземпляре. 

* Узнайте, как [добавить источник концентратора событий.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* Наверстывайте [источник концентратора IoT.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)

### <a name="api-limits"></a>Ограничения API

Ограничения REST API для общей доступности Time Series Insights указаны в [справочной документации REST API.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)