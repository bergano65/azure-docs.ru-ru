---
title: включить файл
description: включить файл
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287425"
---
### <a name="property-limits"></a>Ограничения свойств

Ограничения свойств "аналитика временных рядов Azure" увеличились до 1 000 с максимальной закрепления 800 в Gen1. Предоставленные свойства событий имеют соответствующие столбцы JSON, CSV и диаграммы, которые можно просмотреть в [обозревателе Gen2 временных рядов Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Номер SKU | Максимальное число свойств |
| --- | --- |
| Gen2 (L1) | 1 000 свойства (столбцы) |
| Gen1 (S1) | 600 свойства (столбцы) |
| Gen1 (S2) | 800 свойства (столбцы) |

### <a name="streaming-ingestion"></a>Потоковый прием

* Для каждой среды существует не более двух [источников событий](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) .

* Рекомендации и общие рекомендации для источников событий можно найти [здесь](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) .

* По умолчанию служба "аналитика временных рядов Azure" Gen2 может принимать входящие данные с частотой **до 1 МБ в секунду (Мбит/с) на среду Gen2 "аналитика временных рядов Azure"**. Существуют дополнительные ограничения [для каждой секции центра](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Для отправки запроса в службу поддержки через портал Azure можно использовать скорости до 8 Мбит/с. Дополнительные сведения см. в статье [ограничения пропускной способности приема потоковой передачи](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>Ограничения API

Ограничения REST API для Gen2 Azure Time Series Insights указаны в [справочной документации по REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
