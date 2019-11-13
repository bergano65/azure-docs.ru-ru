---
title: Тип сущности, изученной компьютером — LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017222"
---
# <a name="machine-learned-entity"></a>Сущность, занятая компьютером 



**Сущность хорошо подходит, когда текстовые данные:**


![сущность списка](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Пример JSON

Предположим, что в приложении есть список `Cities`, допускающий разные варианты названия городов, включая город аэропорта (Sea-tac), код аэропорта (SEA), почтовый индекс (98101) и телефонный код города (206).

|Элемент списка|Синонимы элемента|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

В предыдущем высказывании слово `paris` сопоставляется с элементом paris как часть сущности списка `Cities`. Сущность списка совпадает с нормализованным именем элемента, а также с синонимами элемента.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Отклик конечной точки прогнозирования V2](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Отклик конечной точки прогнозирования V3](#tab/V3)


Это JSON, если в строке запроса задано `verbose=false`:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Это JSON, если в строке запроса задано `verbose=true`:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
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
|Сущность списка|`Cities`|`paris`|


## <a name="next-steps"></a>Дополнительная информация

Сведения о сущности [списка](reference-entity-list.md) и сущности [регулярного выражения](reference-entity-regular-expression.md) .