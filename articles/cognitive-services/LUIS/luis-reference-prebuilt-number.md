---
title: Предварительно созданная сущность number
titleSuffix: Azure
description: В этой статье описана готовая сущность number в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 83f7cc7c0da2682244fa9c4e0e2b153aff2e2380
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339306"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность number для приложения LUIS
Существует множество способов использования числовых значений для подсчета, выражения и описания фрагментов информации. В этой статье рассматриваются только некоторые возможные примеры. Служба LUIS интерпретирует вариации в фразах пользователя и возвращает согласованные числовые значения. Так как эта сущность уже обучена, добавлять в назначение приложения примеры фраз, содержащие сущность number, не нужно. 

## <a name="types-of-number"></a>Типы сущности number
Управление сущностью number выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml).

## <a name="examples-of-number-resolution"></a>Примеры разрешения сущности number

| Фраза        | Сущность   | Способы устранения: |
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

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о сущностях [currency](luis-reference-prebuilt-currency.md), [ordinal](luis-reference-prebuilt-ordinal.md) и [percentage](luis-reference-prebuilt-percentage.md). 
