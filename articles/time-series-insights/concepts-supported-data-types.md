---
title: Поддерживаемые типы данных — Azure Time Series Insights Gen2 | Документация Майкрософт
description: Узнайте о поддерживаемых типах данных в Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 9d29d83ed92ee0fa2217bb1c27fbf6c2fbb3584c
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170868"
---
# <a name="supported-data-types"></a>Поддерживаемые типы данных

В следующей таблице перечислены типы данных, поддерживаемые службой "аналитика временных рядов Azure" Gen2

| Тип данных | Описание | Пример | Имя столбца свойств в Parquet
|---|---|---|---|
| **bool**; | Тип данных, имеющий одно из двух состояний: `true` или `false`. | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | Представляет текущее время, обычно выраженное как дата и время суток. Выражено в формате [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). Свойства DateTime всегда хранятся в формате UTC. Смещения часового пояса, если они правильно отформатированы, будут применены, а затем получится значение, хранящееся в формате UTC. Дополнительные сведения о свойстве timestamp среды и смещениях DateTime см. в [этом](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) разделе. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | 64-разрядное число двойной точности  | `"value": 31.0482941` | value_double
| **long** | 64-разрядное целое число со знаком  | `"value" : 31` | value_long
| **строка** | Текстовые значения должны состоять из допустимых UTF-8. Значения NULL и пустые строки считаются одинаковыми. |  `"site": "DIM_MLGGG"` | site_string
| **dynamic** | Сложный (не являющийся примитивом) тип, состоящий из массива или контейнера свойств (Dictionary). Сейчас только переведенные массивы JSON примитивов или массивов объектов, не содержащих идентификатор TS или свойство timestamp, будут храниться как динамические. Ознакомьтесь с этой [статьей](./concepts-json-flattening-escaping-rules.md) , чтобы понять, как объекты будут сведены, а массивы можно будет отменить. Свойства полезных данных, хранящиеся в этом типе, доступны через обозреватель Gen2 временных рядов Azure и `GetEvents`   API запросов. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>Отправка смешанных типов данных

Среда Gen2 "аналитика временных рядов Azure" является строго типизированной. Если устройства или теги отправляют данные различных типов для свойства устройства, значения будут храниться в двух отдельных столбцах, а [функция объединения ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions) должна использоваться при определении выражений переменных временных рядов в вызовах API.

Обозреватель службы "аналитика временных рядов Azure" предлагает способ автоматического объединения отдельных столбцов одного и того же свойства устройства. В приведенном ниже примере датчик отправляет `PresentValue` свойство, которое может быть одновременно длинным или двойным. Чтобы запросить все сохраненные значения (независимо от типа данных) `PresentValue` свойства, выберите, `PresentValue (Double | Long)` и столбцы будут объединены.

[![Автоматическое объединение в обозревателе](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Объекты и массивы

В качестве части полезных данных события можно передавать сложные типы, такие как объекты и массивы. Вложенные объекты будут плоскими, а массивы будут храниться как `dynamic` или сведены для создания нескольких событий в зависимости от конфигурации среды и фигуры JSON. Дополнительные сведения о [правилах плоской обработки и экранирования JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Дальнейшие шаги

* Ознакомьтесь с [правилами преобразования и экранирования JSON](./concepts-json-flattening-escaping-rules.md) , чтобы понять, как будут храниться события.

* Общие сведения об [ограничениях пропускной способности](./concepts-streaming-ingress-throughput-limits.md) среды

* Сведения об [источниках событий](concepts-streaming-ingestion-event-sources.md) для приема потоковых данных.
