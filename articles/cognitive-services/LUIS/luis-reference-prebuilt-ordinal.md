---
title: Порядковый номер предварительно созданной сущности — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность ordinal в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b2a2d9e78a0b152da14bb737079cf0dfdef0dc05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491241"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность ordinal для приложения LUIS
Порядковый номер — это числовое представление объекта внутри набора: `first`, `second`, `third`. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности ordinal, не нужно. Сущность ordinal поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Типы сущностей ordinal
Управление сущностью ordinal выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45).

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Разрешение для предварительно созданной сущности ordinal

Для запроса возвращаются следующие объекты сущности:

`Order the second option`

#### <a name="v3-responsetabv3"></a>[V3 ответ](#tab/V3)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `false`:

```json
"entities": {
    "ordinal": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 подробный ответ](#tab/V3-verbose)
Следующий код JSON относится к параметру `verbose`, для которого задано значение `true`:

```json
"entities": {
    "ordinal": [
        2
    ],
    "$instance": {
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "second",
                "startIndex": 10,
                "length": 6,
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

#### <a name="v2-responsetabv2"></a>[Ответ v2](#tab/V2)

В следующем примере показано разрешение сущности **builtin.ordinal**.

```json
"entities": [
  {
    "entity": "second",
    "type": "builtin.ordinal",
    "startIndex": 10,
    "endIndex": 15,
    "resolution": {
      "value": "2"
    }
  }
]
```
* * * 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

Сведения о [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [номере телефона](luis-reference-prebuilt-phonenumber.md)и [температуре](luis-reference-prebuilt-temperature.md) . 
