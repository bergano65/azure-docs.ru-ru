---
title: Номер предварительно созданной сущности — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность number в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 88f36fb6d73e2ec88940e7eb53d982824e194074
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560192"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность number для приложения LUIS
Существует множество способов использования числовых значений для подсчета, выражения и описания фрагментов информации. В этой статье рассматриваются только некоторые возможные примеры. Служба LUIS интерпретирует вариации в фразах пользователя и возвращает согласованные числовые значения. Так как эта сущность уже обучена, добавлять в назначение приложения примеры фраз, содержащие сущность number, не нужно. 

## <a name="types-of-number"></a>Типы сущности number
Управление сущностью number выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml).

## <a name="examples-of-number-resolution"></a>Примеры разрешения сущности number

| Фраза        | Сущность   | Разрешение |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


Служба LUIS включает распознанное значение сущности **`builtin.number`** в поле `resolution` возвращаемого ответа в формате JSON.

## <a name="resolution-for-prebuilt-number"></a>Разрешение для предварительно созданной сущности number


### <a name="api-version-2x"></a>API версии 2. x

В следующем примере показан ответ LUIS в формате JSON, который содержит расшифровку высказывания "two dozen" (две дюжины) в значение 24.

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Предварительная версия API версии 3. x

Следующий код JSON имеет `verbose` параметр со `false`значением:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ]
        }
    }
}
```

Следующий код JSON имеет `verbose` параметр со `true`значением:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "two dozen",
                        "startIndex": 6,
                        "length": 9,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о сущностях [currency](luis-reference-prebuilt-currency.md), [ordinal](luis-reference-prebuilt-ordinal.md) и [percentage](luis-reference-prebuilt-percentage.md). 
