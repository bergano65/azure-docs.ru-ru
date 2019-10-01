---
title: Извлечение данных — LUIS
titleSuffix: Azure Cognitive Services
description: Извлечение данных из utterance текста с помощью целей и сущностей. Сведения о том, какие типы данных можно извлечь из Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: ff0a9838d1fcc9db3b6cc25b47c840e01056e6cd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703149"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Извлечение данных из utterance текста с помощью целей и сущностей
Служба LUIS дает возможность получать информацию из высказываний на естественном языке пользователя. Информация извлекается таким образом, который позволяет использовать ее программой, приложением или чат-ботом для выполнения определенных действий. В следующих разделах содержатся сведения о данных, возвращаемых из намерений и сущностей, с примерами JSON.

Сложнее всего извлекаются данные, прошедшие машинное обучение, так как они не являются точным текстовым совпадением. Извлечение данных из прошедших машинное обучение [сущностей](luis-concept-entity-types.md) должно осуществляться в ходе [цикла разработки](luis-concept-app-iteration.md) до тех пор, пока вы не будете уверены, что получаете ожидаемые данные.

## <a name="data-location-and-key-usage"></a>Расположение данных и использование ключа
LUIS предоставляет данные из опубликованной [конечной точки](luis-glossary.md#endpoint). **Запрос HTTPS** (POST или GET) содержит высказывание, а также некоторые дополнительные конфигурации, например промежуточные или рабочие среды.

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Запрос конечной точки прогнозирования v2](#tab/V2)

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 запрос конечной точки прогнозирования](#tab/V3)

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * * 

`appID` доступен на странице **Параметры** приложения LUIS, а также в составе URL-адреса (после `/apps/`) при редактировании этого приложения LUIS. `subscription-key` — это ключ конечной точки, используемый для запросов приложения. При использовании бесплатного ключа разработки или начального ключа во время обучения LUIS необходимо изменить ключ конечной точки на ключ, который поддерживает [ожидаемое использование LUIS](luis-boundaries.md#key-limits). Единица `timezoneOffset` в минутах.

**Ответ HTTPS** содержит все сведения о намерении и сущности, которые служба LUIS может определять на основе текущей опубликованной модели промежуточной или рабочей конечной точки. URL-адрес конечной точки указан на веб-сайте [LUIS](luis-reference-regions.md), раздел **Управление**, страница **Ключи и конечные точки**.

## <a name="data-from-intents"></a>Данные из намерений
Основными данными является **имя намерения** с самой высокой оценкой. Ответ конечной точки:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * * 

|Объект данных|Тип данных|Расположение данных|Значение|
|--|--|--|--|
|Намерение|Строковое|topScoringIntent.intent|"GetStoreInfo"|

Если приложение, вызывающее чат-бот или LUIS, принимает решение на основе более чем одной оценки намерения, возвратите все оценки.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

Задайте параметр QueryString, `verbose=true`. Ответ конечной точки:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Задайте параметр QueryString, `show-all-intents=true`. Ответ конечной точки:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * * 

Намерения упорядочены от имеющего самую высокую оценку до имеющего самую низкую оценку.

|Объект данных|Тип данных|Расположение данных|Значение|Оценке|
|--|--|--|--|:--|
|Намерение|Строковое|intents[0].intent|"GetStoreInfo"|0,984749258|
|Намерение|Строковое|intents[1].intent|"None"|0,0168218873|

При добавлении готовых предметных областей имя намерения указывает предметную область, такую как `Utilties` или `Communication`, а также намерение:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * * 

|Домен|Объект данных|Тип данных|Расположение данных|Значение|
|--|--|--|--|--|
|Коммунальные службы|Намерение|Строковое|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Связь|Намерение|Строковое|intents[1].intent|<b>Communication</b>.StartOver"|
||Намерение|Строковое|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Данные из сущностей
Большинству чат-ботов и приложений требуется не только имя намерения. Эти дополнительные данные поступают из сущностей, обнаруживаемых в высказывании. Каждый тип сущности возвращает различные сведения о совпадении.

Одно слово или фраза в высказывании может соответствовать нескольким сущностям. В этом случае каждая совпадающая сущность возвращается с оценкой.

Все сущности возвращаются в массиве **сущностей** ответа от конечной точки:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

```JSON
"entities": {
    "name":["bob jones"],
    "number": [3]
}
```
Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * * 

## <a name="tokenized-entity-returned"></a>Возвращенная размеченная сущность
Несколько [языков](luis-language-support.md#tokenization) возвращают объект сущности с [размеченным](luis-glossary.md#token) значением `entity`. startIndex и endIndex, возвращаемые службой LUIS в объекте сущности, не сопоставляются с новым размеченным значением. Они сопоставляются с исходным запросом, чтобы вы могли программно извлекать необработанные сущности. 

Например, на немецком языке слово `das Bauernbrot` размечается в `das bauern brot`. Возвращается размеченное значение `das bauern brot`, а исходное значение можно определить программно из startIndex и endIndex исходного запроса, в итоге получив `das Bauernbrot`.

## <a name="simple-entity-data"></a>Данные простой сущности

[Простая сущность](reference-entity-simple.md) — это значение, прошедшее машинное обучение. Это может быть слово или фраза.

## <a name="composite-entity-data"></a>Данные составной сущности

[Составная сущность](reference-entity-composite.md) состоит из других сущностей, например готовых сущностей, простых, регулярных выражений и сущностей списка. Эти отдельные сущности формируют единую сущность. 

## <a name="list-entity-data"></a>Данные сущности списка

[Список сущностей](reference-entity-list.md) представляет собой фиксированный, закрытый набор связанных слов вместе с их синонимами. LUIS не обнаруживает дополнительные значения для сущностей списка. Посмотреть предложения для новых слов на основе текущего списка можно с помощью функции **Рекомендовать**. Если имеется несколько сущностей списка с одним и тем же значением, в запросе конечной точки возвращается каждая сущность. 

## <a name="prebuilt-entity-data"></a>Данные предварительно созданной сущности
Обнаружение [предварительно созданных](luis-concept-entity-types.md) сущностей выполняется на основе совпадения регулярного выражения с помощью с проекта [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) с открытым исходным кодом. Предварительно созданные сущности возвращаются в массиве сущностей и используют имя типа с префиксом `builtin::`. Ниже приводится пример высказывания с возвращаемыми предварительно созданными сущностями.

`Dec 5th send to +1 360-555-1212`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Без параметра QueryString `verbose=true`:

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ]
}
```

С параметром QueryString `verbose=true`:

```json

"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-12-05",
                    "value": "2018-12-05"
                },
                {
                    "timex": "XXXX-12-05",
                    "value": "2019-12-05"
                }
            ]
        }
    ],
    "ordinal": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "ordinalV2": [
        {
            "offset": 5,
            "relativeTo": "start"
        }
    ],
    "number": [
        1360,
        555,
        1212
    ],
    "phonenumber": [
        "1 360-555-1212"
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "Dec 5th",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinal": [
            {
                "type": "builtin.ordinal",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2",
                "text": "5th",
                "startIndex": 4,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "number": [
            {
                "type": "builtin.number",
                "text": "1 360",
                "startIndex": 17,
                "length": 5,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "555",
                "startIndex": 23,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.number",
                "text": "1212",
                "startIndex": 27,
                "length": 4,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 360-555-1212",
                "startIndex": 17,
                "length": 14,
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

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * * 
## <a name="regular-expression-entity-data"></a>Данные сущности регулярного выражения

[Сущность регулярного выражения](reference-entity-regular-expression.md) извлекает сущность на основе предоставленного шаблона регулярного выражения.

## <a name="extracting-names"></a>Извлечение имен
Получение имен из высказывания является сложной задачей, имя может быть практически любым сочетанием букв и слов. В зависимости от того, какой тип имени извлекается, вы можете использовать один из подходящих вариантов. Следующие предложения являются не правилами, а дополнительными рекомендациями.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Добавление предварительно созданных сущностей PersonName и GeographyV2

Сущности [PersonName](luis-reference-prebuilt-person.md) и [GeographyV2](luis-reference-prebuilt-geographyV2.md) доступны для некоторых [языков и региональных параметров](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Имена людей

В зависимости от языка и региональных параметров формат имен людей может незначительно отличаться. Используйте предварительно созданную сущность **[персоннаме](luis-reference-prebuilt-person.md)** или **[простую сущность](luis-concept-entity-types.md#simple-entity)** с [ролями](luis-concept-roles.md) с именами и фамилиями. 

Если вы используете простую сущность, не забудьте предоставить примеры, в которых используются имя и фамилия в различных частях utterance, в фразы продолжительностью с различной длиной и фразы продолжительностью по всем смыслу, включая намерение None. Регулярно [просматривайте](luis-how-to-review-endoint-utt.md) высказывания конечной точки, чтобы помечать имена, которые были неправильно спрогнозированы.

### <a name="names-of-places"></a>Имена мест

Имена расположений задаются и называются городами, подсчетами, Штатами, провинции, странами и регионами. Используйте предварительно созданную сущность **[geographyV2](luis-reference-prebuilt-geographyv2.md)** для извлечения сведений о расположении.

### <a name="new-and-emerging-names"></a>Новые имена

Некоторые приложения должны иметь возможность поиска новых имен, например имен продуктов и компаний. Эти типы имен являются наиболее сложным типом извлечения данных. Начните с **[простой сущности](luis-concept-entity-types.md#simple-entity)** и добавьте [список фраз](luis-concept-feature.md). Регулярно [просматривайте](luis-how-to-review-endoint-utt.md) высказывания конечной точки, чтобы помечать имена, которые были неправильно спрогнозированы.

## <a name="pattern-roles-data"></a>Данные ролей шаблонов
Роли — это контекстно-зависимые различия сущностей.


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

Имя сущности — `Location`, с двумя ролями `Origin` и `Destination`.

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Employee",
    "startIndex": 5,
    "endIndex": 13,
    "score": 0.922820568,
    "role": ""
  },
  {
    "entity": "seattle",
    "type": "Location",
    "startIndex": 20,
    "endIndex": 26,
    "score": 0.948008537,
    "role": "Origin"
  },
  {
    "entity": "redmond",
    "type": "Location",
    "startIndex": 31,
    "endIndex": 37,
    "score": 0.7047979,
    "role": "Destination"
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

В версии 3 **имя роли** является основным именем объекта. 

Имя сущности — `Location`, с двумя ролями `Origin` и `Destination`.

Без параметра QueryString `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "Origin": [
        "seattle"
    ],
    "Destination": [
        "redmond"
    ]
}
```

С параметром QueryString `verbose=true`:

```json
"entities": {
    "Employee": [
        "bob jones"
    ],
    "LocationOrigin": [
        "seattle"
    ],
    "LocationDestination": [
        "redmond"
    ],
    "$instance": {
        "Employee": [
            {
                "type": "Employee",
                "text": "bob jones",
                "startIndex": 5,
                "length": 9,
                "score": 0.982873261,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Origin": [
            {
                "role": "Origin",
                "type": "Location",
                "text": "seattle",
                "startIndex": 20,
                "length": 7,
                "score": 0.9913306,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "redmond",
                "startIndex": 31,
                "length": 7,
                "score": 0.898179531,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]

}
```

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * *

## <a name="patternany-entity-data"></a>Данные сущности Pattern.any

[Pattern. Any](reference-entity-pattern-any.md) — это заполнитель переменной длины, используемый только в шаблоне шаблона utterance для обозначения места начала и окончания сущности.  

## <a name="sentiment-analysis"></a>Анализ тональности
Если настроен анализ тональности, он входит в ответ JSON LUIS. Дополнительные сведения об анализе тональности см. в документации по [анализу текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

### <a name="sentiment-data"></a>Данные тональности
Данные тональности представляют собой оценку между 1 и 0, означающую положительную (ближе к 1) или отрицательную (ближе к 0) тональность данных.

При использовании языка и региональных параметров `en-us` выводится следующий ответ:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Для всех других языков и региональных параметров выводится ответ:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Данные сущности извлечения ключевой фразы
Сущность извлечения ключевой фразы возвращает ключевые фразы в высказывании, предоставленном службой [анализа текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).


#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

Без параметра QueryString `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ]
}
```

С параметром QueryString `verbose=true`:

```json
"entities": {
    "keyPhrase": [
        "map of places",
        "beautiful views",
        "favorite trail"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "map of places",
                "startIndex": 11,
                "length": 13,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "beautiful views",
                "startIndex": 30,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "favorite trail",
                "startIndex": 51,
                "length": 14,
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

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * *


## <a name="data-matching-multiple-entities"></a>Данные, соответствующие нескольким сущностям

LUIS возвращает все сущности, обнаруженные в высказывании. В результате чат-боту может потребоваться принять решение на основе результатов. Высказывание может содержать много сущностей:

`book me 2 adult business tickets to paris tomorrow on air france`

Конечная точка LUIS может обнаруживать одни и те же данные в разных сущностях.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Без `verbose=true` в качестве параметра QueryString.

```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ]
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ]
}
```

С `verbose=true` в качестве параметра QueryString.


```json
"entities": {
    "TicketsOrder": [
        {
            "number": [
                2
            ],
            "PassengerCategory": [
                "adult"
            ],
            "TravelClass": [
                "business"
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "2",
                        "startIndex": 8,
                        "length": 1,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "PassengerCategory": [
                    {
                        "type": "PassengerCategory",
                        "text": "adult",
                        "startIndex": 10,
                        "length": 5,
                        "score": 0.9503733,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "TravelClass": [
                    {
                        "type": "TravelClass",
                        "text": "business",
                        "startIndex": 16,
                        "length": 8,
                        "score": 0.950095,
                        "modelTypeId": 3,
                        "modelType": "Hierarchical Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    ],
    "Location::LocationTo": [
        "paris"
    ],
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "2019-09-28",
                    "value": "2019-09-28"
                }
            ]
        }
    ],
    "Airline": [
        "air france"
    ],
    "$instance": {
        "TicketsOrder": [
            {
                "type": "TicketsOrder",
                "text": "2 adult business",
                "startIndex": 8,
                "length": 16,
                "score": 0.942183256,
                "modelTypeId": 4,
                "modelType": "Composite Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Location::LocationTo": [
            {
                "type": "Location::LocationTo",
                "text": "paris",
                "startIndex": 36,
                "length": 5,
                "score": 0.9905354,
                "modelTypeId": 3,
                "modelType": "Hierarchical Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "tomorrow",
                "startIndex": 42,
                "length": 8,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
        "Airline": [
            {
                "type": "Airline",
                "text": "air france",
                "startIndex": 54,
                "length": 10,
                "score": 0.9455415,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * *

## <a name="data-matching-multiple-list-entities"></a>Данные, соответствующие нескольким сущностям списка

Если слово или фраза соответствует нескольким сущностям списка, запрос конечной точки возвращает каждую сущность списка.

Если имеется запрос `when is the best time to go to red rock?` и слово `red` приложения находится в нескольких списках, LUIS распознает все сущности и возвратит массив сущностей в составе ответа JSON конечной точки: 

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```



#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Без `verbose=true` в строке запроса:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ]
        }
    }
}
```


С `verbose=true` в строке запроса:

```JSON
{
    "query": "when is the best time to go to red rock",
    "prediction": {
        "normalizedQuery": "when is the best time to go to red rock",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "Colors": [
                [
                    "red"
                ]
            ],
            "Cities": [
                [
                    "Destinations"
                ]
            ],
            "$instance": {
                "Colors": [
                    {
                        "type": "Colors",
                        "text": "red",
                        "startIndex": 31,
                        "length": 3,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ],
                "Cities": [
                    {
                        "type": "Cities",
                        "text": "red rock",
                        "startIndex": 31,
                        "length": 8,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * *

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о добавлении сущностей в приложение LUIS см. в статье [Добавление сущностей](luis-how-to-add-entities.md).
