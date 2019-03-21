---
title: Предварительно созданная сущность url
titleSuffix: Azure
description: В этой статье описана готовая сущность url в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 5fb62c38bde98d946694790adb860240eaa59fa9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530183"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность url для приложения LUIS
Сущность URL-адреса извлекает URL-адреса с доменными именами или IP-адресами. Поскольку эта сущность уже обучена, добавлять в приложения примеры фраз, содержащие URL-адреса, не нужно. Сущность URL-адреса поддерживается только для такого языка и региональных параметров: `en-us`. 

## <a name="types-of-urls"></a>Типы URL-адресов
Управление сущностью url выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Разрешение для предварительно созданной сущности URL-адреса
В следующем примере показано разрешение сущности **builtin.url**.

```json
{
  "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "https://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сущностях [порядковый номер](luis-reference-prebuilt-ordinal.md), [номер](luis-reference-prebuilt-number.md) и [температура](luis-reference-prebuilt-temperature.md).
