---
title: Процент предварительно построенной сущности — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность percentage в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 31ea1c36139abcb1e102161ad76a203073ba4dfd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270511"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность percentage для приложения LUIS
Процентное соотношение может выражаться в виде дроби (`3 1/2`) или в процентах (`2%`). Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности percentage, не нужно. Сущность percentage поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md).

## <a name="types-of-percentage"></a>Типы сущностей percentage
Управление процентами осуществляется из репозитория [распознавания текста](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub

## <a name="resolution-for-prebuilt-percentage-entity"></a>Разрешение для предварительно созданной сущности percentage

Для запроса возвращаются следующие объекты сущности:

`set a trigger when my stock goes up 2%`

#### <a name="v3-response"></a>[V3 ответ](#tab/V3)

Следующий код JSON имеет `verbose` параметр со значением: `false`

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 подробный ответ](#tab/V3-verbose)
Следующий код JSON имеет `verbose` параметр со значением: `true`

```json
"entities": {
    "percentage": [
        2
    ],
    "$instance": {
        "percentage": [
            {
                "type": "builtin.percentage",
                "text": "2%",
                "startIndex": 36,
                "length": 2,
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

В следующем примере показано разрешение сущности **builtin.percentage**.

```json
"entities": [
    {
        "entity": "2%",
        "type": "builtin.percentage",
        "startIndex": 36,
        "endIndex": 37,
        "resolution": {
        "value": "2%"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

Дополнительные сведения о сущностях [порядковый номер](luis-reference-prebuilt-ordinal.md), [номер](luis-reference-prebuilt-number.md) и [температура](luis-reference-prebuilt-temperature.md).
