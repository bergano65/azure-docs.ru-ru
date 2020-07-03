---
title: Кэйфрасе предварительно созданная сущность — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье содержатся сведения о предварительно созданных сущностях keyPhrase в Интеллектуальной службе распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 53be1b13f1e2744e143a4be0777e3a8e3135460e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270527"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность keyPhrase для приложения LUIS
Сущность Кэйфрасе извлекает из utterance различные ключевые фразы. Вам не нужно добавлять в приложение пример фразы продолжительностью, содержащий Кэйфрасе. Сущность Кэйфрасе поддерживается во [многих культурах](luis-language-support.md#languages-supported) как часть функций [анализа текста](../text-analytics/overview.md) .

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Разрешение для предварительно созданной сущности keyPhrase

Для запроса возвращаются следующие объекты сущности:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[V3 ответ](#tab/V3)

Следующий код JSON имеет `verbose` параметр со значением: `false`

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 подробный ответ](#tab/V3-verbose)
Следующий код JSON имеет `verbose` параметр со значением: `true`

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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
#### <a name="v2-response"></a>[Ответ v2](#tab/V2)

В следующем примере показано разрешение для сущности **builtin.keyPhrase**.

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * *

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [age](luis-reference-prebuilt-age.md).
