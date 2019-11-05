---
title: Порядковый номер V2 предварительно созданная сущность — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье содержатся сведения о предварительно построенных сущностях с порядковым номером v2 в Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 02bc6657126cb1cf241c2ca4668e62bd49608d4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491254"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Порядковый номер V2 предварительно созданной сущности для приложения LUIS
Порядковый номер V2 расширяет [порядковый](luis-reference-prebuilt-ordinal.md) номер для предоставления относительных ссылок, таких как `next`, `last`и `previous`. Они не извлекаются с использованием порядкового номера предварительно построенной сущности.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Разрешение для предварительно построенной сущности с порядковым номером v2

Для запроса возвращаются следующие объекты сущности:

`what is the second to last choice in the list`

#### <a name="v3-responsetabv3"></a>[V3 ответ](#tab/V3)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `false`:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 подробный ответ](#tab/V3-verbose)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `true`:

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
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

В следующем примере показано разрешение **встроенной сущности. ordinalV2** .

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

Сведения о [процентах](luis-reference-prebuilt-percentage.md), [номере телефона](luis-reference-prebuilt-phonenumber.md)и [температуре](luis-reference-prebuilt-temperature.md) . 
