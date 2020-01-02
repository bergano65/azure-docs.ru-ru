---
title: Тип сущности регулярного выражения — LUIS
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841223"
---
# <a name="regular-expression-entity"></a>Сущность регулярного выражения

Сущность регулярного выражения извлекает сущность на основе предоставленного шаблона регулярного выражения.

Регулярное выражение является оптимальным вариантом при использовании необработанного текста высказывания. Оно не учитывает регистр и игнорирует региональный алфавит.  Сопоставление регулярных выражений применяется на уровне символов, а не на уровне маркеров, после внесения изменений при проверке орфографии. Если регулярное выражение слишком сложное, например в нем используется много квадратных скобок, вы не сможете добавить выражение в модель. Использует часть, но не всю библиотеку [.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) .

**Сущность хорошо подходит в следующих случаях:**

* Данные согласованно отформатированы с любыми также согласованными вариантами.
* У регулярного выражения может быть не более двух уровней вложенности.

![Сущность регулярного выражения](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Особенности использования

Регулярные выражения могут соответствовать большему значению, чем предполагается. Примером этого является числовое совпадение слов, например `one` и `two`. Примером является следующее регулярное выражение, которое соответствует числу `one` вместе с другими числами:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Это выражение регулярного выражения также соответствует всем словам, которые заканчиваются этими числами, например `phone`. Чтобы устранить подобные проблемы, убедитесь, что регулярное выражение учитывает границы слов. Регулярное выражение для использования границ слов в этом примере используется в следующем регулярном выражении:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Пример JSON

При использовании `kb[0-9]{6}`в качестве определения сущности регулярного выражения следующий ответ JSON представляет собой пример utterance с возвращаемыми сущностями регулярных выражений для запроса:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Отклик конечной точки прогнозирования V2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Отклик конечной точки прогнозирования V3](#tab/V3)


Это JSON, если в строке запроса задано `verbose=false`:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Это JSON, если в строке запроса задано `verbose=true`:

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

В этом [руководстве](tutorial-regex-entity.md)вы создадите приложение для извлечения согласованных данных из utterance с помощью сущности **регулярного выражения** .