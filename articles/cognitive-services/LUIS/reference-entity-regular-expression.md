---
title: Регулярный тип сущности выражения - LUIS
titleSuffix: Azure Cognitive Services
description: Регулярное выражение является оптимальным вариантом при использовании необработанного текста высказывания. Оно не учитывает регистр и игнорирует региональный алфавит.  Сопоставление регулярных выражений применяется на уровне символов, а не на уровне маркеров, после внесения изменений при проверке орфографии.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841223"
---
# <a name="regular-expression-entity"></a>Сущность регулярного выражения

Регулярная сущность выражения извлекает сущность на основе предоставляемого вами шаблона регулярного выражения.

Регулярное выражение является оптимальным вариантом при использовании необработанного текста высказывания. Оно не учитывает регистр и игнорирует региональный алфавит.  Сопоставление регулярных выражений применяется на уровне символов, а не на уровне маркеров, после внесения изменений при проверке орфографии. Если регулярное выражение слишком сложное, например в нем используется много квадратных скобок, вы не сможете добавить выражение в модель. Использует часть, но не всю библиотеку [.NET Regex.](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)

**Сущность оптимальна для данных в таких случаях.**

* Данные согласованно отформатированы с любыми также согласованными вариантами.
* У регулярного выражения может быть не более двух уровней вложенности.

![Сущность регулярного выражения](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Особенности использования

Регулярные выражения могут совпадать больше, чем вы ожидаете, чтобы соответствовать. Примером этого является числовое `one` слово `two`соответствия, такие как и . Примером может быть следующий regex, который соответствует номеру `one` вместе с другими числами:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Это выражение regex также соответствует любым словам, `phone`которые заканчиваются этими числами, такими как . Для того, чтобы исправить проблемы, как это, убедитесь, что regex матчей принимает во внимание границы слов. Regex для использования границ слов для этого примера используется в следующем regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Пример JSON

При `kb[0-9]{6}`использовании , как обычное определение сущности выражения, следующий ответ JSON является примером высказывания с возвращенными регулятивными сущностями выражения для запроса:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[Отклик конечной точки прогнозирования V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[Отклик конечной точки прогнозирования V3](#tab/V3)


Это JSON, `verbose=false` если установлен в строке запроса:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Это JSON, `verbose=true` если установлен в строке запроса:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Дальнейшие действия

В этом [учебнике](tutorial-regex-entity.md)создайте приложение для извлечения последовательно форматированных данных из высказывания с помощью сущности **Regular Expression.**