---
title: Анализ тональности — LUIS
titleSuffix: Azure Cognitive Services
description: Если настроен анализ тональности, он входит в ответ JSON LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270424"
---
# <a name="sentiment-analysis"></a>Анализ тональности
Если настроен анализ тональности, он входит в ответ JSON LUIS. Дополнительные сведения об анализе тональности см. в документации по [анализу текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


## <a name="resolution-for-sentiment"></a>Разрешение для настроения

Данные тональности представляют собой оценку между 1 и 0, означающую положительную (ближе к 1) или отрицательную (ближе к 0) тональность данных.

#### <a name="english-language"></a>[Английский язык](#tab/english)

При использовании языка и региональных параметров `en-us` выводится следующий ответ:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Другие языки](#tab/other-languages)

Для всех других языков и региональных параметров выводится ответ:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

