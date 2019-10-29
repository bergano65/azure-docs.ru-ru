---
title: Расширенная фильтрация в службе "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Расширенная фильтрация в сетке событий на IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992565"
---
# <a name="advanced-filtering"></a>Расширенная фильтрация
Сетка событий позволяет задавать фильтры для любого свойства в полезных данных JSON. Эти фильтры моделируются в виде набора условий `AND`, при этом каждое внешнее условие имеет необязательные внутренние `OR` условия. Для каждого `AND` условия задаются следующие значения:

* `OperatorType` — тип сравнения.
* `Key` — путь JSON к свойству, к которому применяется фильтр.
* `Value` — ссылочное значение, для которого выполняется фильтр (или) `Values` — набор ссылочных значений, по которому выполняется фильтр.

## <a name="json-syntax"></a>Синтаксис JSON

Синтаксис JSON для расширенного фильтра выглядит следующим образом:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Фильтрация по значениям массива

Сейчас сетка событий не поддерживает фильтрацию по массиву значений. Если входящее событие имеет значение массива для ключа расширенного фильтра, операция сопоставления завершается ошибкой. Входящее событие не соответствует подписке на события.

## <a name="and-or-not-semantics"></a>Семантика AND-AND-NOT

Обратите внимание, что в примере JSON, заданном ранее, `AdvancedFilters` является массивом. Придумайте каждый элемент массива `AdvancedFilter` как `AND` условие.

Для операторов, которые поддерживают несколько значений (таких как `NumberIn`, `NumberNotIn`, `StringIn`и т. д.), каждое значение рассматривается как условие `OR`. Таким образом, `StringBeginsWith("a", "b", "c")` будет соответствовать любому строковому значению, которое начинается с `a` или `b` или `c`.

> [!CAUTION]
> Операторы NOT — `NumberNotIn` и `StringNotIn` действуют как условия и для каждого значения, заданного в поле `Values`.
>
> Если этого не сделать, фильтр принять-ALL будет отменять назначение фильтрации.

## <a name="floating-point-rounding-behavior"></a>Поведение округления с плавающей точкой

Для работы со всеми числовыми значениями в сетке событий используется тип `decimal` .NET. Числовые значения, указанные в JSON подписки на события, не действуют в соответствии с режимом округления с плавающей запятой.

## <a name="case-sensitivity-of-string-filters"></a>Чувствительность к регистру строковых фильтров

Все сравнения строк не учитывают регистр. Это поведение можно изменить уже сегодня.

## <a name="allowed-advanced-filter-keys"></a>Разрешенные дополнительные ключи фильтра

Свойство `Key` может быть хорошо известным свойством верхнего уровня или представлять собой путь JSON с несколькими точками, где каждая точка обозначает шаг с заходом в вложенный объект JSON.

Сетка событий не имеет особого смысла для `$` символа в ключе, в отличие от спецификации JSONPath.

### <a name="event-grid-schema"></a>Схема сетки событий

Для событий в схеме сетки событий:

* ИД
* Раздел
* Тема
* EventType
* DataVersion;
* Data. Prop1
* Data. prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Пользовательская схема событий

На `Key` в схеме пользовательских событий нет ограничений, так как сетка событий не применяет схему конвертов в полезных данных.

## <a name="numeric-single-value-filter-examples"></a>Примеры однозначных фильтров с одним значением

* NumberGreaterThan;
* NumberGreaterThanOrEquals;
* NumberLessThan;
* NumberGreaterThanOrEquals;

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Числовые примеры фильтра значений диапазона

* NumberIn;
* NumberNotIn.

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Примеры фильтров значений строкового диапазона

* StringContains;
* StringBeginsWith;
* StringEndsWith;
* StringIn;
* StringNotIn.

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Примеры логических фильтров с одним значением

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
