---
title: Расширенная фильтрация - Azure Event Grid IoT Edge Документы Майкрософт
description: Расширенная фильтрация в Event Grid на IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992565"
---
# <a name="advanced-filtering"></a>Расширенная фильтрация
Event Grid позволяет указывать фильтры на любом свойстве в полезной нагрузке json. Эти фильтры моделируются `AND` как набор условий, при `OR` этом каждое внешнее состояние имеет дополнительные внутренние условия. Для `AND` каждого условия вы указываете следующие значения:

* `OperatorType`- Тип сравнения.
* `Key`- Путь Json к свойству, на котором можно применить фильтр.
* `Value`- Эталонное значение, по которому `Values` работает фильтр (или) - набор эталонных значений, по которым работает фильтр.

## <a name="json-syntax"></a>Синтаксис JSON

Синтаксис JSON для продвинутого фильтра:

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

## <a name="filtering-on-array-values"></a>Фильтрация значений массива

Event Grid не поддерживает фильтрацию массива значений сегодня. Если входяльное событие имеет значение массива для ключа расширенного фильтра, операция согласования завершается сбой. Входяльное событие в конечном итоге не соответствует подписке на событие.

## <a name="and-or-not-semantics"></a>AND-OR-NOT семантики

Обратите внимание, что в примере json, приведенном ранее, `AdvancedFilters` есть массив. Думайте о `AdvancedFilter` каждом элементе массива как о состоянии. `AND`

Для операторов, поддерживающих несколько `NumberIn` `NumberNotIn`значений (таких как , `StringIn`и `OR` т.д.), каждое значение рассматривается как условие. Таким `StringBeginsWith("a", "b", "c")` образом, будет соответствовать любой `a` `b` строки значение, которое начинается с либо или . `c`

> [!CAUTION]
> NOT операторов `NumberNotIn` `StringNotIn` - и вести себя как и `Values` условия на каждом значении, данном в этой области.
>
> Невыполнение этого решения сделает фильтр фильтром Accept-All и подорвет цель фильтрации.

## <a name="floating-point-rounding-behavior"></a>Поведение округления плавающей точки

Event Grid `decimal` использует тип .NET для обработки всех числовых значений. Значения числа, указанные в подписке события JSON, не подвержены плавающему поведению округления точек.

## <a name="case-sensitivity-of-string-filters"></a>Чувствительность к корпусу струнных фильтров

Все сравнения строк нечувствительны. Сегодня это поведение изменить не может.

## <a name="allowed-advanced-filter-keys"></a>Разрешено расширенные клавиши фильтра

Свойство может быть либо известным свойством верхнего уровня, либо пути json с несколькими точками, где каждая `Key` точка означает шаг в вложенный объект Json.

Event Grid не имеет особого `$` значения для персонажа в ключе, в отличие от спецификации JSONPath.

### <a name="event-grid-schema"></a>Схема сетки событий

Для событий в схеме Event Grid:

* ID
* Раздел
* Тема
* EventType
* DataVersion;
* Data.Prop1
* Data.Prop-Prop2.Prop2.Prop4.Prop5.Prop5

### <a name="custom-event-schema"></a>Схема пользовательских событий

Нет никаких ограничений `Key` на схему пользовательских событий, так как Event Grid не применяет схему конверта на полезной нагрузке.

## <a name="numeric-single-value-filter-examples"></a>Примеры однозначных фильтров

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

## <a name="numeric-range-value-filter-examples"></a>Примеры фильтров диапазонов

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

## <a name="string-range-value-filter-examples"></a>Примеры фильтров диапазонов строк

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

## <a name="boolean-single-value-filter-examples"></a>Примеры фильтра с одним значением Boolean

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
