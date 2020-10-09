---
title: Стилесобжект для динамических Azure Maps
description: Справочное руководство по схеме JSON и синтаксису для Стилесобжект, используемого при создании в динамическом Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4b085fbc6e330d38b59fce0c494f672b00c712b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85120504"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>Справочник по схеме Стилесобжект для динамических карт

Эта статья представляет собой справочное руководство по схеме JSON и синтаксису для `StylesObject` . `StylesObject`Представляет собой массив, представляющий стили наборов `StyleObject` состояний. Используйте [службу состояния компонента](https://docs.microsoft.com/rest/api/maps/featurestate) Azure Maps Creator, чтобы применить стили оформления к функциям данных карт. После создания стилей оформления и связывания их с функциями карт можно использовать их для создания динамических карт. Дополнительные сведения о создании динамических карт см. в статье [Реализация динамического стиля для карт создателя](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>стилеобжект

`StyleObject`— Либо в виде, [`BooleanTypeStyleRule`](#booleantypestylerule) либо в [`NumericTypeStyleRule`](#numerictypestylerule) .

В следующем примере JSON показаны `BooleanTypeStyleRule` именованные `occupied` и `NumericTypeStyleRule` именованные имена `temperature` .

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>нумериктипестилеруле

 A `NumericTypeStyleRule` —  [`StyleObject`](#styleobject) и состоит из следующих свойств:

| Свойство | Type | Описание | Обязательно |
|-----------|----------|-------------|-------------|
| `keyName` | строка | Имя *состояния* или динамического свойства. `keyName`Должен быть уникальным внутри `StyleObject` массива.| Да |
| `type` | строка | Значение "numeric". | Да |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Массив числовых диапазонов стилей со связанными цветами. Каждый диапазон определяет цвет, который будет использоваться, когда значение *состояния* соответствует диапазону.| Да |

### <a name="numberruleobject"></a>нумберрулеобжект

Объект `NumberRuleObject` состоит из [`RangeObject`](#rangeobject) и `color` Свойства. Если значение *состояния* попадает в диапазон, его цвет для отображения будет иметь цвет, заданный в `color` свойстве.

Если вы определили несколько перекрывающихся диапазонов, выбранный цвет будет соответствовать цвету, определенному в первом диапазоне.

В следующем примере JSON оба диапазона будут иметь значение true, если *состояние* находится в диапазоне от 50-60. Тем не менее цвет, который будет использоваться, — это `#343deb` первый диапазон в списке, который был удовлетворен.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Свойство | Type | Описание | Обязательно |
|-----------|----------|-------------|-------------|
| `range` | [ранжеобжект](#rangeobject) | [Ранжеобжект](#rangeobject) определяет набор условий логического диапазона, который, если `true` , изменяет цвет отображения *состояния* на цвет, указанный в `color` свойстве. Если `range` параметр не указан, всегда будет использоваться цвет, определенный в `color` свойстве.   | Нет |
| `color` | строка | Цвет, используемый, когда значение состояния попадает в диапазон. `color`Свойство является строкой JSON в одном из следующих форматов: <ul><li> Шестнадцатеричные значения в стиле HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> ХСЛА ("хсла (100, 50%, 50%, 1)")</li><li> Предопределенные имена HTML-цветов, например желтые и синие.</li></ul> | Да |

### <a name="rangeobject"></a>ранжеобжект

`RangeObject`Определяет числовое значение диапазона [`NumberRuleObject`](#numberruleobject) . Чтобы значение *состояния* попадало в диапазон, все определенные условия должны содержать значение true. 

| Свойство | Type | Описание | Обязательно |
|-----------|----------|-------------|-------------|
| `minimum` | double | Все цифры x, которые ≥ x `minimum` .| Нет |
| `maximum` | double | Все цифры x, x ≤ `maximum` . | Нет |
| `exclusiveMinimum` | double | Все цифры x, указанные x > `exclusiveMinimum` .| Нет |
| `exclusiveMaximum` | double | Все цифры x, указанные x < `exclusiveMaximum` .| Нет |

### <a name="example-of-numerictypestylerule"></a>Пример Нумериктипестилеруле

Следующий код JSON иллюстрирует `NumericTypeStyleRule` *состояние* с именем `temperature` . В этом примере элемент [`NumberRuleObject`](#numberruleobject) содержит два определенных диапазона температуры и связанные с ними стили цвета. Если диапазон температуры равен 50-69, то на дисплее должен использоваться цвет `#343deb` .  Если диапазон температуры равен 31-70, то на дисплее должен использоваться цвет `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>булеантипестилеруле

A `BooleanTypeStyleRule` — [`StyleObject`](#styleobject) и состоит из следующих свойств:

| Свойство | Type | Описание | Обязательно |
|-----------|----------|-------------|-------------|
| `keyName` | строка |  Имя *состояния* или динамического свойства.  `keyName`Должен быть уникальным внутри массива Style.| Да |
| `type` | строка |Значение "Boolean". | Да |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)одного| Логическая пара с цветами для `true` `false` значений *состояния* и.| Да |

### <a name="booleanruleobject"></a>булеанрулеобжект

`BooleanRuleObject`Определяет цвета для `true` значений и `false` .

| Свойство | Type | Описание | Обязательно |
|-----------|----------|-------------|-------------|
| `true` | строка | Цвет, используемый, если значение *состояния* равно `true` . `color`Свойство является строкой JSON в одном из следующих форматов: <ul><li> Шестнадцатеричные значения в стиле HTML </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> ХСЛА ("хсла (100, 50%, 50%, 1)")</li><li> Предопределенные имена HTML-цветов, например желтые и синие.</li></ul>| Да |
| `false` | строка | Цвет, используемый, если значение *состояния* равно `false` . | Да |

### <a name="example-of-booleantypestylerule"></a>Пример Булеантипестилеруле

Следующий код JSON иллюстрирует `BooleanTypeStyleRule` *состояние* с именем `occupied` . [`BooleanRuleObject`](#booleanruleobject)Определяет цвета для `true` значений и `false` .

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```
