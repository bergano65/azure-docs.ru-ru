---
title: Персоннаме предварительно созданная сущность — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана предварительно созданная сущность PersonName в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9777c62d97c70d4f6a0d0a4d912dea3fa8decd23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499553"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность PersonName для приложения LUIS
Предварительно созданная сущность PersonName определяет имена людей. Так как эта сущность уже обучена, добавлять в намерения приложения примеры высказываний, содержащие сущности PersonName, не нужно. Сущность personName поддерживается для английского и китайского [языков и региональных параметров](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Разрешение для сущности personName

Для запроса возвращаются следующие объекты сущности:

`Is Jill Jones in Cairo?`


#### <a name="v3-responsetabv3"></a>[V3 ответ](#tab/V3)


Следующий код JSON относится к параметру `verbose`, для которого задано значение `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 подробный ответ](#tab/V3-verbose)
Следующий код JSON относится к параметру `verbose`, для которого задано значение `true`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-responsetabv2"></a>[Ответ v2](#tab/V2)

В следующем примере показано разрешение сущности **builtin.PersonName**.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * * 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

См. дополнительные сведения о сущностях [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) и [ordinal](luis-reference-prebuilt-ordinal.md). 
