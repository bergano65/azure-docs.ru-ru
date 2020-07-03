---
title: Предварительно созданные сущности с номером телефона — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность phonenumber в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4e2c8e27c6d4195252c6a5b423fa98b2a4247182
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270472"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность номера телефона для приложения LUIS
Сущность `phonenumber` извлекает различные номера телефонов, в том числе код страны. Так как эта сущность уже обучена, добавлять в приложение примеры фраз не нужно. Сущность `phonenumber` поддерживается только в языке `en-us`.

## <a name="types-of-a-phone-number"></a>Типы телефонных номеров
`Phonenumber`управляется из репозитория [распознаваемого текста](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub

## <a name="resolution-for-this-prebuilt-entity"></a>Разрешение для этой предварительно созданной сущности

Для запроса возвращаются следующие объекты сущности:

`my mobile is 1 (800) 642-7676`

#### <a name="v3-response"></a>[V3 ответ](#tab/V3)

Следующий код JSON имеет `verbose` параметр со значением: `false`

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 подробный ответ](#tab/V3-verbose)
Следующий код JSON имеет `verbose` параметр со значением: `true`

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

В следующем примере показано разрешение сущности **builtin.phonenumber**.

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [temperature](luis-reference-prebuilt-temperature.md).
