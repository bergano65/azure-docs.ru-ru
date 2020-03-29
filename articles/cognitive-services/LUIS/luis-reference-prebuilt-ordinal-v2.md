---
title: Ординальное V2 заранее построенное лицо - LUIS
titleSuffix: Azure Cognitive Services
description: Эта статья содержит обычное V2 заранее структурированной информации о сущности в языке Понимание (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 5e852313db75e598da647ea0f985e2ee18af16de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270487"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Ordinal V2 заранее построенная сущность для приложения LUIS
Ordinal V2 номер расширяет [Ordinal](luis-reference-prebuilt-ordinal.md) предоставить `next`относительные ссылки, такие как , `last`и `previous`. Они не извлекаются с использованием ординаторской заранее построенной сущности.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Разрешение для заранее построенного ординаторного V2 объекта

Для запроса возвращаются следующие объекты сущности:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Ответ V3](#tab/V3)

Следующий JSON с `verbose` параметром, установленным для: `false`

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

#### <a name="v3-verbose-response"></a>[V3 многословный ответ](#tab/V3-verbose)

Следующий JSON с `verbose` параметром, установленным для: `true`

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
#### <a name="v2-response"></a>[V2 ответ](#tab/V2)

Ниже приводится разрешение **объекта builtin.ordinalV2.**

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

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

Узнайте о [процентном соотношении,](luis-reference-prebuilt-percentage.md) [номере телефона](luis-reference-prebuilt-phonenumber.md)и [температурных](luis-reference-prebuilt-temperature.md) объектах.
