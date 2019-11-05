---
title: Предварительно созданная сущность температуры — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность temperature в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: f2ea08694419caaaf54e4fed45c7c1589be2473d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499522"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность temperature для приложения LUIS
Сущность temperature извлекает различные типы температурных данных. Так как эта сущность уже обучена, добавлять в приложение примеры фраз, содержащие сущности temperature, не нужно. Сущность temperature поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Типы сущностей temperature
Управление сущностью temperature выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Разрешение для предварительно созданной сущности temperature

Для запроса возвращаются следующие объекты сущности:

`set the temperature to 30 degrees`


#### <a name="v3-responsetabv3"></a>[V3 ответ](#tab/V3)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `false`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 подробный ответ](#tab/V3-verbose)
Следующий код JSON относится к параметру `verbose`, для которого задано значение `true`:

```json
"entities": {
    "temperature": [
        {
            "number": 30,
            "units": "Degree"
        }
    ],
    "$instance": {
        "temperature": [
            {
                "type": "builtin.temperature",
                "text": "30 degrees",
                "startIndex": 23,
                "length": 10,
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

В следующем примере показано разрешение сущности **builtin.temperature**.

```json
"entities": [
    {
        "entity": "30 degrees",
        "type": "builtin.temperature",
        "startIndex": 23,
        "endIndex": 32,
        "resolution": {
        "unit": "Degree",
        "value": "30"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [age](luis-reference-prebuilt-age.md). 
