---
title: DatetimeV2 предварительно построенные сущности — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность datetimeV2 в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: c4e83ed15c2b15ccb3339ff775b08c8d2dab4c32
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932519"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность datetimeV2 для приложения LUIS

Предварительно созданная сущность **datetimeV2** извлекает значения даты и времени. Эти значения имеют стандартный формат, за счет чего их могут использовать клиентские программы. Если во фразе указана неполная дата или время, LUIS возвращает в ответе конечной точки _прошедшие и будущие значения_ . Так как эта сущность уже обучена, добавлять примеры фраз, содержащие сущность datetimeV2 в приложение, не нужно. 

## <a name="types-of-datetimev2"></a>Типы сущностей datetimeV2
Управление сущностью datetimeV2 выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml).

## <a name="example-json"></a>Пример JSON 
В следующем примере ответа JSON приведена сущность `datetimeV2` с подтипом `datetime`. Подтипы сущностей datetimeV2 см. в [этом разделе](#subtypes-of-datetimev2)</a>.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Описания свойств JSON

|Имя свойства |Тип и описание свойства|
|---|---|
|Сущность|**Строка.** Текст, извлеченный из фразы, с типом даты, времени, диапазоном дат или времени.|
|type|**Строка.** Один из [подтипов сущности datetimeV2](#subtypes-of-datetimev2).
|startIndex|**Целое число.** Индекс во фразе, в которой начинается сущность.|
|endIndex|**Целое число.** Индекс во фразе, в которой заканчивается сущность.|
|resolution|Имеет массив `values` с одним, двумя или четырьмя [значениями разрешения](#values-of-resolution).|
|end|Конечное значение времени или диапазона дат в том же формате, что и значение (`value`). Используется, только если свойство `type` имеет значение `daterange`, `timerange` или `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Подтипы сущности datetimeV2

Предварительно созданная сущность **datetimeV2** имеет следующие подтипы (примеры каждого из них приведены в таблице ниже):
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Значения разрешения
* Массив содержит один элемент, если дата или время во фразе полностью указаны и однозначны.
* Массив содержит два элемента, если значение datetimeV2 неоднозначное. Неоднозначность означает отсутствие года, времени и диапазона времени. Примеры см. в разделе [Неоднозначные даты](#ambiguous-dates). Если неоднозначно указано время до полудня или после полудня, включаются оба значения.
* Массив содержит четыре элемента, если фраза содержит два неоднозначных элемента. К неоднозначным относятся элементы, в которых:
  * в дате или диапазоне дат указан неоднозначно год;
  * во времени или диапазоне времени указано неоднозначно время до или или после полудня (например, 3 апреля 3:00).

Ниже приведены поля, которые может иметь каждый элемент массива `values`. 

|Имя свойства|Описание свойства|
|--|--|
|timex|Время, дата или диапазон дат, выраженные в формате TIMEX, который соответствует [стандарту ISO 8601](https://en.wikipedia.org/wiki/ISO_8601), и в атрибутах TIMEX3 заметки с помощью языка TimeML. Эта заметка описана в [рекомендациях TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|type|Подтип может иметь один из следующих элементов: `datetime`, `date`, `time`, `daterange` `datetimerange` `timerange`,,, `duration`, `set`.|
|value|**Необязательный параметр.** Объект datetime в формате гггг:ММ:дд (дата), ЧЧ:мм:сс (время) гггг:ММ:дд ЧЧ:мм:сс (дата и время). Если свойство `type` имеет значение `duration`, в качестве значения указывается количество секунд (длительность). <br/> Используется, только если свойство `type` имеет значение `datetime` или `date`, `time` или `duration.|

## <a name="valid-date-values"></a>Допустимые значения даты

Сущность **datetimeV2** поддерживает даты в следующих диапазонах:

| Min | Max |
|----------|-------------|
| 1 января 1900 г.   | 31 декабря 2099 г. |

## <a name="ambiguous-dates"></a>Неоднозначные даты

Если дата может быть в прошлом или будущем, LUIS предоставляет оба значения. В качестве примера можно привести фразу с датой и месяцем без года.  

Например, 2 мая.
* Если сегодня 3 мая 2017 года, LUIS возвращает два значения: 2017-05-02 и 2018-05-02. 
* Если сегодня 1 мая 2017 года, LUIS возвращает два значения: 2016-05-02 и 2017-05-02.

В следующем примере показано разрешение сущности "may 2nd". Это разрешение предполагает, что сегодняшняя дата — это дата между 2 мая 2017 г. и 1 мая 2018 г.
Поля с `X` в поле `timex` — это части даты, которые явно не указаны во фразе.

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Примеры разрешения диапазона числовых дат

Сущность `datetimeV2` извлекает диапазоны дат и времени. Поля `start` и `end` определяют начало и конец диапазона. Если имеется фраза "May 2nd to May 5th", LUIS предоставляет значения **daterange** за текущий и будущий год. В поле `timex` значения `XXXX` указывают неоднозначность года. Значение `P3D` указывает на трехдневный период времени.

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Примеры разрешения диапазона дат для дня недели

В следующем примере показано, как с помощью сущности **datetimeV2** LUIS обрабатывает фразу "Tuesday to Thursday". В этом примере ниже текущей датой является 19 июня. LUIS включает значения **daterange** обоих диапазонов дат перед и после текущей даты.

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Неоднозначное время
Массив значений имеет два элемента времени, если время или диапазон времени неоднозначны. Если время указано неоднозначно, возвращается значение до и после полудня .

## <a name="time-range-resolution-example"></a>Пример разрешения диапазона времени

В следующем примере показано, как с помощью сущности **datetimeV2** LUIS обрабатывает фразу с диапазон времени.

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

## <a name="preview-api-version-3x"></a>Предварительная версия API версии 3. x

В API V3 изменен ответ DatetimeV2 JSON. 

Изменения из API версии 2:
* `datetimeV2.timex.type`свойство больше не возвращается, так как оно возвращается на родительском уровне, `datetimev2.type`. 
* Свойство было переименовано в `datetimeV2.value`. `datetimeV2.timex`

Для utterance `8am on may 2nd 2017`версия DatetimeV2 версии 3 имеет следующие преимущества:

```JSON
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    }
}
```

Следующий код JSON имеет `verbose` параметр со `false`значением:

```json
{
    "query": "8am on may 2nd 2017",
    "prediction": {
        "normalizedQuery": "8am on may 2nd 2017",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6826963
            }
        },
        "entities": {
            "datetimeV2": [
                {
                    "type": "datetime",
                    "values": [
                        {
                            "timex": "2017-05-02T08",
                            "value": "2017-05-02 08:00:00"
                        }
                    ]
                }
            ],
            "$instance": {
                "datetimeV2": [
                    {
                        "type": "builtin.datetimeV2.datetime",
                        "text": "8am on may 2nd 2017",
                        "startIndex": 0,
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="deprecated-prebuilt-datetime"></a>Устаревшая предварительно созданная сущность datetime

Устаревшая предварительно созданная сущность `datetime` заменяется сущностью **datetimeV2**. 

Чтобы заменить сущность `datetime` на `datetimeV2` в приложении LUIS, сделайте следующее:

1. Откройте область **Сущности** веб-интерфейса LUIS. 
2. Удалите предварительно созданную сущность **datetime**.
3. Щелкните **Add prebuilt entity** (Добавить предварительно созданную сущность).
4. Выберите **datetimeV2** и нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о сущностях [dimension](luis-reference-prebuilt-dimension.md), [email](luis-reference-prebuilt-email.md) и [number](luis-reference-prebuilt-number.md). 

