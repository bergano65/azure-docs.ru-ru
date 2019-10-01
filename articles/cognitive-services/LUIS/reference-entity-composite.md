---
title: Составной объект типа-LUIS
titleSuffix: Azure Cognitive Services
description: Составная сущность состоит из других сущностей, например готовых сущностей, простых, регулярных выражений и сущностей списка. Эти отдельные сущности формируют единую сущность.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695170"
---
# <a name="composite-entity"></a>Составная сущность 

Составная сущность состоит из других сущностей, например готовых сущностей, простых, регулярных выражений и сущностей списка. Эти отдельные сущности формируют единую сущность. 

**Эта сущность хорошо подходит, когда данные:**

* данные связаны между собой; 
* Связаны между собой в контексте фразы.
* для них используются различные типы сущностей;
* должны быть сгруппированы и обработаны клиентским приложением в качестве единицы информации;
* имеют различные высказывания пользователей, которые требуют машинного обучения.

![составная сущность](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Пример JSON

Рассмотрим составную сущность предварительно построенных `number` и `Location::ToLocation` со следующими utterance:

`book 2 tickets to cairo`

Обратите внимание, что между `2` (числом) и `cairo` ToLocation находятся слова, которые не входят в какие-либо сущности. Зеленая линия подчеркивания, используемая в помеченном высказывании на веб-сайте [LUIS](luis-reference-regions.md), указывает на составную сущность.

![Составная сущность](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

Составные сущности возвращаются в массиве `compositeEntities`, а все сущности в рамках составной возвращаются в массиве `entities`:

```JSON
  "entities": [
    {
      "entity": "2 tickets to cairo",
      "type": "ticketinfo",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.9214487
    },
    {
      "entity": "cairo",
      "type": "builtin.geographyV2.city",
      "startIndex": 18,
      "endIndex": 22
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "subtype": "integer",
        "value": "2"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "ticketinfo",
      "value": "2 tickets to cairo",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "builtin.geographyV2.city",
          "value": "cairo"
        }
      ]
    }
  ]
```    

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Это JSON, если `verbose=false` задан в строке запроса:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ]
        }
    ]
}
```

Это JSON, если `verbose=true` задан в строке запроса:

```json
"entities": {
    "ticketinfo": [
        {
            "number": [
                2
            ],
            "geographyV2": [
                "cairo"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 5,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2.city",
                        "text": "cairo",
                        "startIndex": 18,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "$instance": {
        "ticketinfo": [
            {
                "type": "ticketinfo",
                "text": "2 tickets to cairo",
                "startIndex": 5,
                "length": 18,
                "score": 0.9214487,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 


|Объект данных|Имя сущности|Значение|
|--|--|--|
|Предварительно созданная сущность — число|"builtin.number"|"2"|
|Предварительно созданная сущность — GeographyV2|"Location::ToLocation"|Каиро|

## <a name="next-steps"></a>Следующие шаги

В этом [руководстве](luis-tutorial-composite-entity.md)добавляется **составная сущность** для объединения извлеченных данных различных типов в одну содержащую сущность. Клиентское приложение может легко извлекать связанные данные разных типов путем их объединения.
