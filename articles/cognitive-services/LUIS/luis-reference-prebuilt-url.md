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
ms.topic: reference
ms.date: 10/04/2019
ms.author: diberry
ms.openlocfilehash: 49f145ce3e9022826abad5d274dd611bb2cc6530
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270346"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность url для приложения LUIS
Сущность URL-адреса извлекает URL-адреса с доменными именами или IP-адресами. Поскольку эта сущность уже обучена, добавлять в приложения примеры фраз, содержащие URL-адреса, не нужно. Сущность URL-адреса поддерживается только для такого языка и региональных параметров: `en-us`.

## <a name="types-of-urls"></a>Типы URL-адресов
Управление URL-адресом осуществляется из репозитория [распознаваемого текста](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub

## <a name="resolution-for-prebuilt-url-entity"></a>Разрешение для предварительно созданной сущности URL-адреса

Для запроса возвращаются следующие объекты сущности:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[V3 ответ](#tab/V3)

Следующий код JSON имеет `verbose` параметр со значением: `false`

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 подробный ответ](#tab/V3-verbose)

Следующий код JSON имеет `verbose` параметр со значением: `true`

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
#### <a name="v2-response"></a>[Ответ v2](#tab/V2)

В следующем примере показано решение проблемы, https://www.luis.ai которая представляет собой хороший пример использования искусственных служб.

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

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

Дополнительные сведения о сущностях [порядковый номер](luis-reference-prebuilt-ordinal.md), [номер](luis-reference-prebuilt-number.md) и [температура](luis-reference-prebuilt-temperature.md).
