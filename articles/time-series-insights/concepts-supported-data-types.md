---
title: Поддерживаемые типы данных — Azure Time Series Insights | Документация Майкрософт
description: Сведения о поддерживаемых типах данных в предварительной версии службы "аналитика временных рядов Azure".
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 1a1ab9fa19956341e930c85d13d7067be4449cea
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050103"
---
# <a name="supported-data-types"></a>Поддерживаемые типы данных

В следующей таблице перечислены типы данных, поддерживаемые службой "аналитика временных рядов".

| Тип данных | Описание | Пример | Имя столбца свойств в Parquet
|---|---|---|---|
| **bool**; | Тип данных, имеющий одно из двух состояний: `true` или `false`. | "сомнительный": true | isQuestionable_bool
| **datetime** | Представляет текущее время, обычно выраженное как дата и время суток. Выражено в формате [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). Свойства DateTime всегда хранятся в формате UTC. Смещения часового пояса, если они правильно отформатированы, будут применены, а затем получится значение, хранящееся в формате UTC. Дополнительные сведения о свойстве timestamp среды и смещениях DateTime см. в [этом](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) разделе. | "Евентпроцесседлокалтиме": "2020-03-20T09:03:32.8301668 Z" | eventProcessedLocalTime_datetime 
| **double** | 64-разрядное число двойной точности  | "значение": 31,0482941 | value_double
| **long** | 64-разрядное целое число со знаком  | "значение": 31 | value_long
| **string** | Текстовые значения должны состоять из допустимых UTF-8. |  "сайт": "DIM_MLGGG" | site_string
| **dynamic** | Сложный (не являющийся примитивом) тип, состоящий из массива или контейнера свойств (Dictionary). В настоящее время только переведенные массивы JSON примитивов или массивов объектов, не содержащих идентификатор TS или соответствующую отметку времени (ий), будут храниться как динамические. Прочтите эту [статью](./concepts-json-flattening-escaping-rules.md) , чтобы понять, как будут сведены объекты, а также отменить их откат. |  "values": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * Среда TSI является строго типизированной. Если устройства или теги отправляют как целочисленные, так и Нецелочисленные данные, значения свойств устройства будут храниться в двух разделенных столбцах типа Double и Long, а [функция объединения ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) должна использоваться при вызове API и определении выражений переменной модели временных рядов.

#### <a name="objects-and-arrays"></a>Объекты и массивы

В качестве части полезных данных события можно передавать сложные типы, такие как объекты и массивы. Вложенные объекты будут плоскими, а массивы будут храниться как `dynamic` или сведены для создания нескольких событий в зависимости от конфигурации среды и фигуры JSON. Дополнительные сведения о [правилах плоской обработки и экранирования JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Дальнейшие шаги

* Ознакомьтесь с [правилами преобразования и экранирования JSON](./concepts-json-flattening-escaping-rules.md) , чтобы понять, как будут храниться события. 

* Общие сведения об [ограничениях пропускной способности](concepts-streaming-throughput-limitations.md) среды

* Сведения об [источниках событий](concepts-streaming-ingestion-event-sources.md) для приема потоковых данных.
