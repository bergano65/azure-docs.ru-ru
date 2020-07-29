---
title: Включить имя файла
description: включить файл
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 48080bb4d1e24f7f98d3dfe1fd63b65ba46df35e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289917"
---
### <a name="property-limits"></a>Ограничения свойств

Ограничения свойств "аналитика временных рядов Azure" увеличились до 1 000 с максимальной закрепления 800 в Gen1. Предоставленные свойства событий имеют соответствующие столбцы JSON, CSV и диаграммы, которые можно просмотреть в [обозревателе Gen2 временных рядов Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| номер SKU | Максимальное число свойств |
| --- | --- |
| Gen2 (L1) | 1 000 свойства (столбцы) |
| Gen1 (S1) | 600 свойства (столбцы) |
| Gen1 (S2) | 800 свойства (столбцы) |

### <a name="event-sources"></a>Источники событий

Поддерживается не более двух источников событий на один экземпляр.

* Узнайте, как [Добавить источник концентратора событий](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Настройка [источника центра Интернета вещей](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

По умолчанию [среды Gen2 поддерживают входящие скорости передачи](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingress-throughput-limits) данных до **1 МБ в секунду на каждую среду**. При необходимости клиенты могут масштабировать свои среды до **16 МБ/с** . Кроме того, существует ограничение в **0,5 МБ/с**для каждого раздела.

### <a name="api-limits"></a>Ограничения API

Ограничения REST API для Gen2 Azure Time Series Insights указаны в [справочной документации по REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
