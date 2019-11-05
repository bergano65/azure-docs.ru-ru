---
title: Предварительно построенные сущности URL-LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность url в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 12831ede2b9d9251f2e02fa396ee7d2fb2d61240
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499499"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность url для приложения LUIS
Сущность URL-адреса извлекает URL-адреса с доменными именами или IP-адресами. Поскольку эта сущность уже обучена, добавлять в приложения примеры фраз, содержащие URL-адреса, не нужно. Сущность URL-адреса поддерживается только для такого языка и региональных параметров: `en-us`. 

## <a name="types-of-urls"></a>Типы URL-адресов
Управление сущностью url выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Разрешение для предварительно созданной сущности URL-адреса

Для запроса возвращаются следующие объекты сущности:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-responsetabv3"></a>[V3 ответ](#tab/V3)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `false`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 подробный ответ](#tab/V3-verbose)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `true`:

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
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

В следующем примере показано разрешение https://www.luis.ai — хороший пример использования искусственного интеллекта.

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * * 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

Дополнительные сведения о сущностях [порядковый номер](luis-reference-prebuilt-ordinal.md), [номер](luis-reference-prebuilt-number.md) и [температура](luis-reference-prebuilt-temperature.md).
