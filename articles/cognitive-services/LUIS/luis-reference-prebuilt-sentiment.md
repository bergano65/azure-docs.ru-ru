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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270424"
---
# <a name="sentiment-analysis"></a>Анализ мнений
Если настроен анализ тональности, он входит в ответ JSON LUIS. Дополнительные сведения об анализе тональности см. в документации по [анализу текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


## <a name="resolution-for-sentiment"></a>Решение для тональности

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

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

