---
title: Простой тип сущности — LUIS
titleSuffix: Azure Cognitive Services
description: Простая сущность описывает одну концепцию из контекста, из которого они изучены. Добавьте список фраз при использовании простой сущности для улучшения результатов.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 27eb4d827c1488b0d051505e62286fd66d6a286b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671518"
---
# <a name="simple-entity"></a>Простые сущности 

Простая сущность — это универсальная сущность, описывающая одно понятие и обучаемая на основе контекста машинного обучения. Так как простые сущности обычно являются именами, например названиями компаний, продуктов или другими категориями имен, добавьте [список фраз](luis-concept-feature.md) для выделения используемых имен. 

**Сущность хорошо подходит в следующих случаях:**

* У данных отсутствует согласованное форматирование, но они означают одно и то же. 

![простая сущность](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Пример JSON

`Bob Jones wants 3 meatball pho`

В предыдущем высказывании `Bob Jones` помечена как простая сущность `Customer`.

Данные, возвращаемые из конечной точки, содержат имя сущности, обнаруженный текст из высказывания, расположение обнаруженного текста, а также оценку:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Это JSON, если в строке запроса задано `verbose=false`:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
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
|Простая сущность|`Customer`|`bob jones`|

## <a name="next-steps"></a>Дальнейшие действия

В этом [руководстве](luis-quickstart-primary-and-secondary-data.md)вы извлечете сведения об имени задания по трудоустройству, полученные от компьютера, из utterance с помощью **простой сущности**. Чтобы увеличить точность извлечения, добавьте [список фраз](luis-concept-feature.md) для терминов, характерных для простой сущности.