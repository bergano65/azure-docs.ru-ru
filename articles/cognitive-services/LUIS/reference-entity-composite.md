---
title: Тип композитной сущности - LUIS
titleSuffix: Azure Cognitive Services
description: Композитная сущность состоит из других сущностей, таких как предварительно построенные сущности, простые, регулярные выражения и объекты списка. Эти отдельные сущности формируют единую сущность.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: a5a1ad467074ee0aa55d14d50ae153ac68304e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71695170"
---
# <a name="composite-entity"></a>Составная сущность 

Композитная сущность состоит из других сущностей, таких как предварительно построенные сущности, простые, регулярные выражения и объекты списка. Эти отдельные сущности формируют единую сущность. 

**Эта сущность оптимальна для данных в таких случаях.**

* данные связаны между собой; 
* Связаны между собой в контексте фразы.
* для них используются различные типы сущностей;
* должны быть сгруппированы и обработаны клиентским приложением в качестве единицы информации;
* имеют различные высказывания пользователей, которые требуют машинного обучения.

![составная сущность](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Пример JSON

Рассмотрим составной сущность заранее построенной `number` и `Location::ToLocation` со следующим высказыванием:

`book 2 tickets to cairo`

Обратите внимание, что между `2` (числом) и `cairo` ToLocation находятся слова, которые не входят в какие-либо сущности. Зеленая линия подчеркивания, используемая в помеченном высказывании на веб-сайте [LUIS](luis-reference-regions.md), указывает на составную сущность.

![Составная сущность](./media/luis-concept-data-extraction/composite-entity.png)

#### <a name="v2-prediction-endpoint-response"></a>[Отклик конечной точки прогнозирования V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Отклик конечной точки прогнозирования V3](#tab/V3)

Это JSON, `verbose=false` если установлен в строке запроса:

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

Это JSON, `verbose=true` если установлен в строке запроса:

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
|Предварительно построенное образование - ГеографияV2|"Location::ToLocation"|"cairo"|

## <a name="next-steps"></a>Дальнейшие действия

В этом [учебнике](luis-tutorial-composite-entity.md)добавьте **композитную сущность** в комплект извлеченных данных различных типов в единую содержащую сущность. Клиентское приложение может легко извлекать связанные данные разных типов путем их объединения.
