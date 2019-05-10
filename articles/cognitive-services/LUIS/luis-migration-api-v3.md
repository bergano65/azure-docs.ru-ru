---
title: Версии 2 до версии 3 API миграции
titleSuffix: Azure Cognitive Services
description: Конечная точка версии 3, которые были изменены интерфейсы API. Используйте это руководство, чтобы понять, как перенести в конечную точку версии 3 API-интерфейсы.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 6412f0a2e295a19f741c70e7870a4d198ee03b71
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233548"
---
# <a name="preview-migrate-to-api-version-3x--for-luis-apps"></a>Предварительный просмотр: Перенос в API версии 3.x для приложения LUIS

Конечная точка прогнозирующих запросов, которые были изменены интерфейсы API. Используйте это руководство, чтобы понять, как перенести в конечную точку версии 3 API-интерфейсы. 

Этот API V3 предоставляет следующие новые функции, включающие значительные изменения запроса или ответа JSON: 

* [Внешние сущности](#external-entities-passed-in-at-prediction-time)
* [Динамические списки](#dynamic-lists-passed-in-at-prediction-time)
* [Предварительно созданные сущности JSON изменений](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

Конечная точка прогнозирующих запросов [запроса](#request-changes) и [ответа](#response-changes) значительно изменились для поддержки новых функций, перечисленных выше, включая следующие:

* [Изменения объектов ответа](#top-level-json-changes)
* [Ссылки на имя сущности роли вместо имени сущности](#entity-role-name-instead-of-entity-name)
* [Свойства, чтобы пометить сущностей в фразы](#marking-placement-of-entities-in-utterances)

Ниже приведены функции LUIS **не поддерживается** в API версии 3:

* Версии 7 проверки орфографии Bing

[Справочная документация по](https://aka.ms/luis-api-v3) доступна для версии 3.

## <a name="prebuilt-entities-with-new-json"></a>Предварительно созданных сущностей с новый JSON-ФАЙЛ

Изменения объекта ответа V3 включают в себя [предварительно созданных сущностей](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Запрос изменений 

### <a name="query-string-parameters"></a>Параметры строки запроса

V3 API содержит разные значения строки запроса.

|Имя param|type|Version|Назначение|
|--|--|--|--|
|`query`|string|Только версия 3|**В версии 2**, utterance в качестве прогнозируемых находится в `q` параметра. <br><br>**В версии 3**, переданный функции `query` параметра.|
|`show-all-intents`|Логическое|Только версия 3|Возвращает все объекты Intent с соответствующую оценку в **prediction.intents** объекта. Объекты Intent возвращаются в виде объектов в родительской `intents` объекта. Это обеспечивает программный доступ без необходимости поиска цель в массиве: `prediction.intents.give`. В версии 2 они были возвращены в массиве. |
|`verbose`|Логическое|V2 И V3|**В версии 2**, если задано значение true, все прогнозируемые намерения были возвращены. Все прогнозируемые объекты Intent, используйте параметр V3 типа `show-all-intents`.<br><br>**В версии 3**, этот параметр предоставляет только сущности сведения о метаданных сущности прогноза.  |

<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Текст прогнозирующего запроса JSON для `POST` запроса

```JSON
{
    "query":"your utterance here",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

## <a name="response-changes"></a>Изменения ответа

Ответ на запрос JSON изменен, чтобы разрешать больше программный доступ к данным, наиболее часто используемым. 

### <a name="top-level-json-changes"></a>Верхний уровень JSON изменения

Top свойства JSON для версии 2, когда `verbose` имеет значение true, который возвращает все объекты Intent и результаты в `intents` свойство:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Ниже приведены основных свойств JSON для версии 3.

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

`intents` Объект является неупорядоченный список. Не рассчитывайте на первый дочерний элемент в `intents` соответствует `topIntent`. Вместо этого используйте `topIntent` значение для поиска оценка:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Изменения схемы ответа JSON позволяют:

* Очистить различие между исходной utterance `query`и возвращается прогноз, `prediction`.
* Более простой программный доступ к прогнозируемые данные. Вместо перечисления в виде массива в версии 2, значения, доступны **с именем** для намерения и сущности. Для ролей прогнозируемое сущности имя роли возвращается, так как он уникален в пределах всего приложения.
* Типы данных, если определить, учитываются. Числовые значения больше не возвращается в виде строк.
* Различие между первого набора сведений о прогноза приоритет и дополнительные метаданные, возвращаемые в `$instance` объекта. 

### <a name="access-instance-for-entity-metadata"></a>Доступ `$instance` для метаданные сущности

Если вам нужна сущность метаданных, строке запроса должен использовать `verbose=true` флаг и ответ содержит метаданные в `$instance` объекта. Примеры приведены в ответы JSON в следующих разделах.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Каждая сущность прогнозируемое представляется как массив

`prediction.entities.<entity-name>` Содержит массив, так как каждая сущность может быть спрогнозирован более одного раза в utterance. 

### <a name="list-entity-prediction-changes"></a>Список сущностей прогнозирования изменения

JSON для сущности прогноз списка изменилось должен быть массивом массивов:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Каждый внутренний массив соответствует текст внутри utterance. Внутренний объект является массивом, так как же текст может отображаться в более чем один подчиненный список сущностью списка. 

При сопоставлении между `entities` объект `$instance` объекта, порядок объектов будет сохранен для использования списка сущностей прогнозы.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Имя сущности роли, а не имя сущности 

В версии 2 `entities` массив, возвращаемый всех прогнозируемых сущностей с именем сущности, которые уникальный идентификатор. В версии 3 Если сущность использует роли и относится прогноз с ролью сущности основным идентификатором является имя роли. Это возможно, так как имена ролей сущности должно быть уникальным для всего приложения, включая другие имена модели (намерения, entity).

В следующем примере: рассмотрите возможность utterance, содержащий текст, `Yellow Bird Lane`. Этот текст, согласно прогнозу как строка настраиваемого `Location` роль сущности `Destination`.

|Utterance текста|Имя сущности|Имя роли|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

В версии 2, сущность определяется _имя сущности_ с ролью, как свойство объекта:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

В версии 3, ссылается сущность _роль сущности_, если прогноз для роли:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

В версии 3, тот же результат с `verbose` флаг для возвращения метаданных сущности:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Внешние сущности, переданный во время прогноза

Внешние сущности присвойте LUIS приложению возможность идентифицировать и пометить сущности во время выполнения, который можно использовать в качестве функции для существующих сущностей. Это позволяет использовать собственный отдельный и пользовательские средства извлечения перед отправкой запросов к конечной точке прогноза. Так как это делается в конечной точке прогнозирующих запросов, не нужно повторное обучение и опубликовать модель.

Клиентское приложение предоставляет свои собственные средства для извлечения сущностей, управляя сопоставления сущностей и определение расположения в пределах utterance, соответствующей сущности и последующей отправки этих сведений в запрос. 

Внешние сущности — это механизм для расширения любого типа сущности при по-прежнему используется в качестве сигналы с другими моделями как роли, составного и другим пользователям.

Это полезно для объекта, который содержит данные, доступные только во время выполнения прогнозирующих запросов. Примерами таких данных постоянно изменяются данные или определенного пользователя. Вы можете расширить сущности контакта LUIS внешние данные из списка контактов пользователя. 

### <a name="entity-already-exists-in-app"></a>Сущность уже существует в приложении

Значение `entityName` для внешней сущности, переданное в текст POST запроса конечной точки уже должен существовать в обучение и опубликованные приложения во время выполнения запроса. Тип сущности не имеет значения, поддерживаются все типы.

### <a name="first-turn-in-conversation"></a>Во-первых, включите в диалоге

Рассмотрим первый utterance в диалоге bot чата, где пользователь вводит следующие Неполные сведения.

`Send Hazem a new message`

Запрос от чат-бот для LUIS можно передать сведения в тело запроса POST о `Hazem` , она выделяется непосредственно как один из контактов пользователя.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Ответ прогноза включает этой внешней сущности, все другие прогнозируемые сущности, так как оно определяется в запросе.  

### <a name="second-turn-in-conversation"></a>Во-вторых, включить в диалоге

Далее utterance пользователя в чат-бот использует более широкий термин:

`Send him a calendar reminder for the party.`

В предыдущем utterance, использует utterance `him` как ссылка на `Hazem`. Грамматики голосового управления чат-бот, а в тело запроса POST, можно сопоставить `him` сущности значение, извлеченное из первого utterance `Hazem`.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Ответ прогноза включает этой внешней сущности, все другие прогнозируемые сущности, так как оно определяется в запросе.  

#### <a name="resolution"></a>Способы устранения:

_Необязательно_ `resolution` возвращает в ответе прогноза, что позволяет передать в метаданные, связанные с внешней сущности, а затем получать его обратно в ответе. 

Основной целью является расширение предварительно созданных сущностей, но он не ограничивается этого типа сущности. 

`resolution` Свойство может быть число, строка, объект или массив:

* «Dallas»
* {«text»: «value»}
* 12345 
* ["a", "b", "c"]


## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Динамические списки, переданный во время прогноза

Динамические списки позволяют расширять обученной и опубликованы в списке сущность, уже в приложении LUIS. 

Эту функцию можно используйте, когда нужно периодически менять значения объекта в списке. Эта функция позволяет расширить сущности уже получила необходимое обучение и опубликованного списка:

* Во время запроса конечной точки прогнозирующих запросов.
* Для отдельного запроса.

Список сущностей может быть пустым в приложении LUIS, но он должен существовать. Список сущности в приложении LUIS не изменяется, но возможность прогноза на конечной точке расширена для включения до 2 списки с около 1000 элементов.

### <a name="dynamic-list-json-request-body"></a>Динамический список текст запроса JSON

Отправить следующим текстом JSON, чтобы добавить в список новый подчиненный список синонимов и предсказать сущностью списка для текста, `LUIS`, с помощью `POST` прогнозирующего запроса:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Ответ прогноза включает этот объект списка, со всех других прогнозируемых сущностей, так как оно определяется в запросе. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>Переименован в datetimeReference TimezoneOffset

**В версии 2**, `timezoneOffset` [параметр](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) отправляется в запрос прогноза в параметр строки запроса, независимо от того, если запрос отправляется как запрос GET или POST. 

**В версии 3**, ту же функциональность предоставляется с параметром тело POST `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Пометка размещения сущностей в фразы

**В версии 2**, сущности был помечен в utterance с `startIndex` и `endIndex`. 

**В версии 3**, объект помечен с помощью `startIndex` и `entityLength`.


## <a name="next-steps"></a>Дальнейшие действия

Используйте документацию по V3 API для обновления существующего REST вызовы LUIS [конечной точки](https://aka.ms/luis-api-v3) API-интерфейсы. 