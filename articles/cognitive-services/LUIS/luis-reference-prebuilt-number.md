---
title: Номер Предварительно построенного объекта - LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность number в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 85e3589d7467691e2b9a11879510ab980bbd875a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273464"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность number для приложения LUIS
Существует множество способов использования числовых значений для подсчета, выражения и описания фрагментов информации. В этой статье рассматриваются только некоторые возможные примеры. Служба LUIS интерпретирует вариации в фразах пользователя и возвращает согласованные числовые значения. Так как эта сущность уже обучена, добавлять в назначение приложения примеры фраз, содержащие сущность number, не нужно.

## <a name="types-of-number"></a>Типы сущности number
Номер управляется из репозитория [Распознавания-текста](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub

## <a name="examples-of-number-resolution"></a>Примеры разрешения сущности number

| Фраза        | Сущность   | Решение |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      |
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS включает в себя **`builtin.number`** признанную `resolution` ценность сущности в поле ответа JSON, который она возвращает.

## <a name="resolution-for-prebuilt-number"></a>Разрешение для предварительно созданной сущности number

Для запроса возвращаются следующие объекты сущности:

`order two dozen eggs`

#### <a name="v3-response"></a>[Ответ V3](#tab/V3)

Следующий JSON с `verbose` параметром, установленным для: `false`

```json
"entities": {
    "number": [
        24
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 многословный ответ](#tab/V3-verbose)

Следующий JSON с `verbose` параметром, установленным для: `true`

```json
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
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 ответ](#tab/V2)

В следующем примере показан ответ LUIS в формате JSON, который содержит расшифровку высказывания "two dozen" (две дюжины) в значение 24.

```json
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
```
* * *

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

Узнайте больше о сущностях [currency](luis-reference-prebuilt-currency.md), [ordinal](luis-reference-prebuilt-ordinal.md) и [percentage](luis-reference-prebuilt-percentage.md).
