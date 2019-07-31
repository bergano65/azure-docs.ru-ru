---
title: Миграция API v2 на v3
titleSuffix: Azure Cognitive Services
description: Интерфейсы API конечной точки версии 3 изменились. В этом руководство вы узнаете, как выполнить миграцию на конечные точки API версии 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: a61f196a509c3e84b518fffb4eb78f5f7430cb28
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667582"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Предварительный просмотр: Переход на API версии 3. x для приложений LUIS

API конечной точки прогнозирования запросов изменились. В этом руководство вы узнаете, как выполнить миграцию на конечные точки API версии 3. 

Этот API V3 предоставляет следующие новые функции, которые включают значительные изменения запросов и/или ответов JSON: 

* [Внешние сущности](#external-entities-passed-in-at-prediction-time)
* [Динамические списки](#dynamic-lists-passed-in-at-prediction-time)
* [Изменения предварительно созданных сущностей JSON](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

[Запрос](#request-changes) и [ответ](#response-changes) конечной точки прогноза запросов имеют значительные изменения для поддержки новых функций, перечисленных выше, включая следующие:

* [Изменения объекта ответа](#top-level-json-changes)
* [Имя роли сущности ссылки вместо имени сущности](#entity-role-name-instead-of-entity-name)
* [Свойства для пометки сущностей в фразы продолжительностью](#marking-placement-of-entities-in-utterances)

Следующие функции LUIS **не поддерживаются** в API V3:

* Проверка орфографии Bing версии 7

[Справочная документация](https://aka.ms/luis-api-v3) доступна для версии 3.

## <a name="endpoint-url-changes-by-slot-name"></a>Изменение URL-адреса конечной точки по имени слота

Изменился формат HTTP-вызова конечной точки версии v3.

|METHOD|URL|
|--|--|
|GET|https://<b>{регион}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0-Preview</b>/АППС/<b>{App-ID}</b>/Слотс/<b>{имя слота}</b>/предикт? запрос =<b>{запрос}</b>|
|ПОМЕСТИТЬ|https://<b>{регион}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0-Preview</b>/АППС/<b>{App-ID}</b>/Слотс/<b>{слот-Name}</b>/предикт|
|||

Допустимые значения для слотов:

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>Изменение URL-адреса конечной точки по ИДЕНТИФИКАТОРу версии

Если вы хотите выполнить запрос по версии, сначала необходимо [ОПУБЛИКОВАТЬ API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) с `"directVersionPublish":true`помощью. Запросите конечную точку, ссылающуюся на идентификатор версии, а не на имя слота.


|METHOD|URL|
|--|--|
|GET|https://<b>{регион}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0-Preview</b>/АППС/<b>{App-ID}</b>/версионс/<b>{версия-ID}</b>/предикт? запрос =<b>{Query}</b>|
|ПОМЕСТИТЬ|https://<b>{регион}</b>. API.Cognitive.Microsoft.com/Luis/<b>v 3.0-Preview</b>/АППС/<b>{App-ID}</b>/версионс/<b>{версия-ID}</b>/предикт|
|||

## <a name="prebuilt-entities-with-new-json"></a>Предварительно созданные сущности с новым JSON

Изменения объекта ответа v3 включают предварительно [созданные сущности](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Запрос изменений 

### <a name="query-string-parameters"></a>Параметры строки запроса

API V3 имеет разные параметры строки запроса.

|Имя параметра|Тип|Version|Значение по умолчанию|Цель|
|--|--|--|--|--|
|`log`|Логический|V2 & V3|False|Сохранить запрос в файле журнала.| 
|`query`|строка|Только версия 3|Нет значения по умолчанию — оно требуется в запросе GET.|**В версии 2**utterance для прогнозирования находится в `q` параметре. <br><br>**В версии 3**функции передаются в `query` параметре.|
|`show-all-intents`|Логический|Только версия 3|False|Возврат всех целей с соответствующим показателем в объекте **PREDICTION.** Fors. Объекты возвращаются в виде объектов в родительском `intents` объекте. Это позволяет программному доступу без необходимости находить цель в массиве: `prediction.intents.give`. В версии 2 они были возвращены в массиве. |
|`verbose`|Логический|V2 & V3|False|**В версии 2**, если задано значение true, возвращаются все прогнозируемые намерения. Если требуются все прогнозируемые намерения, используйте параметр `show-all-intents`v3 параметра.<br><br>**В версии 3**этот параметр предоставляет только метаданные сущности "прогнозирование сущностей".  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Текст JSON прогноза запроса для `POST` запроса

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Свойство|Тип|Version|Значение по умолчанию|Цель|
|--|--|--|--|--|
|`dynamicLists`|array|Только версия 3|Необязательно.|[Динамические списки](#dynamic-lists-passed-in-at-prediction-time) позволяют расширить существующую подготовленную и опубликованную сущность списка, уже находящиеся в приложении Luis.|
|`externalEntities`|array|Только версия 3|Необязательно.|[Внешние сущности](#external-entities-passed-in-at-prediction-time) предоставляют приложению Luis возможность определять и отмечать сущности во время выполнения, которые можно использовать в качестве функций для существующих сущностей. |
|`options.datetimeReference`|строка|Только версия 3|Нет значения по умолчанию|Используется для определения [смещения datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Формат для Датетимереференце — [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.overridePredictions`|Логический|Только версия 3|False|Указывает, используется ли [внешняя сущность пользователя (с тем же именем, что и существующая сущность)](#override-existing-model-predictions) , или существующая сущность в модели используется для прогнозирования. |
|`query`|строка|Только версия 3|Обязательный.|**В версии 2**utterance для прогнозирования находится в `q` параметре. <br><br>**В версии 3**функции передаются в `query` параметре.|



## <a name="response-changes"></a>Изменения ответа

Формат JSON ответа на запрос изменен, чтобы обеспечить более частое программный доступ к данным, используемым чаще всего. 

### <a name="top-level-json-changes"></a>Изменения верхнего уровня JSON

Верхние свойства JSON для v2 —, если `verbose` для `intents` свойства задано значение true, которое возвращает все способы и их оценки в свойстве:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Верхние свойства JSON для V3:

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

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

`intents` Объект является неупорядоченным списком. Не считайте, что первый дочерний `intents` элемент в соответствует `topIntent`. Вместо этого используйте `topIntent` значение, чтобы найти оценку:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Изменения схемы JSON ответа позволяют:

* Снимите различие между исходным utterance `query`, и возвращенным прогнозом,. `prediction`
* Упрощенный программный доступ к прогнозируемым данным. Вместо перечисления по массиву в v2 можно получить доступ к значениям с **именем** для целей и сущностей. Для прогнозируемых ролей сущности возвращается имя роли, так как оно уникально для всего приложения.
* Типы данных, если они определены, учитываются. Числовые значения больше не возвращаются в виде строк.
* Различие между данными прогноза первого приоритета и дополнительными метаданными `$instance` , возвращаемыми в объекте. 

### <a name="access-instance-for-entity-metadata"></a>Доступ `$instance` для метаданных сущности

Если требуются метаданные сущности, то в строке запроса необходимо использовать `verbose=true` флаг, а ответ содержит метаданные `$instance` в объекте. Примеры показаны в ответах JSON в следующих разделах.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Каждая прогнозируемая сущность представлена в виде массива

`prediction.entities.<entity-name>` Объект содержит массив, так как каждая сущность может быть предсказана более одного раза в utterance. 

### <a name="list-entity-prediction-changes"></a>Перечисление изменений прогнозов сущностей

JSON для прогнозирования сущностей списка был изменен на массив массивов:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Каждый внутренний массив соответствует тексту внутри utterance. Внутренний объект является массивом, поскольку один и тот же текст может присутствовать в нескольких подсписках сущности списка. 

При сопоставлении `entities` объекта `$instance` с объектом порядок объектов сохраняется для прогнозов объекта List.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Имя роли сущности вместо имени сущности 

В версии 2 `entities` массив вернул все прогнозируемые сущности с именем сущности, которое является уникальным идентификатором. В версии 3, если сущность использует роли, а прогноз предназначен для роли сущности, первичный идентификатор — это имя роли. Это возможно, поскольку имена ролей сущности должны быть уникальными во всем приложении, включая имена других моделей (намерения, сущности).

В следующем примере: рассмотрим utterance, включающий текст, `Yellow Bird Lane`. Этот текст прогнозируется как `Location` `Destination`роль пользовательской сущности.

|Utterance текст|Имя сущности|Имя роли|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

В версии 2 сущность определяется _именем сущности_ и ролью в качестве свойства объекта:

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

В v3 сущность ссылается _ролью сущности_, если прогноз предназначен для роли:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

В v3 тот же результат с `verbose` флагом для возврата метаданных сущности:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Внешние сущности, переданные во время прогнозирования

Внешние сущности предоставляют приложению LUIS возможность определять и отмечать сущности во время выполнения, которые можно использовать в качестве функций для существующих сущностей. Это позволяет использовать собственные пользовательские средства извлечения сущностей перед отправкой запросов в конечную точку прогнозирования. Так как это делается на конечной точке прогнозирования запросов, вам не нужно переучить и публиковать модель.

Клиентское приложение предоставляет собственное средство извлечения сущностей путем управления сопоставлением сущностей и определением расположения в utterance соответствующей сущности и последующей отправкой этой информации с запросом. 

Внешние сущности — это механизм расширения любого типа сущности, который по-прежнему используется как сигналы для других моделей, таких как роли, составные и другие.

Это полезно для сущности, имеющей данные, доступные только в среде выполнения прогнозирующих запросов. Примеры данных такого типа постоянно изменяют данные или зависят от отдельных пользователей. Вы можете расширить сущность LUIS Contact с помощью внешней информации из списка контактов пользователя. 

### <a name="entity-already-exists-in-app"></a>Сущность уже существует в приложении

Значение `entityName` для внешней сущности, переданное в теле запроса конечной точки, должно уже существовать в обученном и опубликованном приложении во время выполнения запроса. Тип сущности не имеет значения, поддерживаются все типы.

### <a name="first-turn-in-conversation"></a>Первое включение диалога

Рассмотрим первый utterance в диалоговом окне чата, когда пользователь вводит следующую неполную информацию:

`Send Hazem a new message`

Запрос от Bot-робота к Luis может передавать сведения в тексте сообщения о том `Hazem` , что он напрямую соответствует одному из контактов пользователя.

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

Прогнозирующий запрос включает эту внешнюю сущность со всеми другими прогнозируемыми сущностями, поскольку она определена в запросе.  

### <a name="second-turn-in-conversation"></a>Второе включение диалога

Следующий пользователь, utterance в роботе Chat, использует более неясный термин:

`Send him a calendar reminder for the party.`

В предыдущем utterance utterance использует `him` в качестве ссылки на. `Hazem` Робот разговора в теле сообщения может сопоставляться `him` со значением сущности, извлеченным из первого utterance,. `Hazem`

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

Прогнозирующий запрос включает эту внешнюю сущность со всеми другими прогнозируемыми сущностями, поскольку она определена в запросе.  

### <a name="override-existing-model-predictions"></a>Переопределить прогнозы существующих моделей

Свойство `overridePredictions` options указывает, что если пользователь отправляет внешнюю сущность, которая пересекается с прогнозируемой сущностью с тем же именем, Luis выбирает переданную сущность или сущность, существующую в модели. 

Например, рассмотрим запрос `today I'm free`. Luis обнаруживает `today` как datetimeV2 со следующим ответом:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Если пользователь отправляет внешнюю сущность:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Если параметр `false`имеет значение, Luis возвращает ответ, как если бы внешняя сущность не была отправлена. `overridePredictions` 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

`overridePredictions` Если для`true`задано значение, Luis возвращает ответ, в том числе:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Разрешение

Необязательное `resolution` свойство возвращает в ответе прогноза, что позволяет передать метаданные, связанные с внешней сущностью, а затем получить ее обратно в ответе. 

Основной целью является расширение предварительно созданных сущностей, но оно не ограничивается этим типом сущности. 

`resolution` Свойство может быть числом, строкой, объектом или массивом:

* Офиса
* {"Text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Динамические списки, переданные во время прогнозирования

Динамические списки позволяют расширить существующую подготовленную и опубликованную сущность списка, уже находящиеся в приложении LUIS. 

Используйте эту функцию, если необходимо периодически менять значения сущностей списка. Эта функция позволяет расширить уже подготовленный и опубликованный объект списка.

* Во время запроса конечной точки прогноза запроса.
* Для одного запроса.

Сущность списка может быть пустой в приложении LUIS, но она должна существовать. Сущность списка в приложении LUIS не изменилась, но возможности прогнозирования в конечной точке расширены для включения в них до 2 списков с 1 000 элементами.

### <a name="dynamic-list-json-request-body"></a>Текст запроса JSON динамического списка

Отправить в следующем тексте JSON, чтобы добавить новый вложенный список с синонимами в список и спрогнозировать сущность списка для текста `LUIS` `POST` с запросом прогноза запроса:

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

Прогнозирующий запрос включает в себя эту сущность List со всеми другими прогнозируемыми сущностями, поскольку она определена в запросе. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset переименован в Датетимереференце

**В версии 2** `timezoneOffset` [параметр](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) отправляется в прогнозирующий запрос как параметр строки запроса, независимо от того, отправляется ли запрос как запрос GET или POST. 

**В версии 3**те же функциональные возможности предоставляются с параметром текста POST, `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Маркировка размещения сущностей в фразы продолжительностью

**В версии 2**сущность была помечена в utterance с `startIndex` помощью и. `endIndex` 

**В версии 3**сущность помечена `startIndex` и. `entityLength`

## <a name="deprecation"></a>Устаревшее 

API v2 не будет считаться устаревшим по меньшей мере через 9 месяцев после предварительной версии v3. 

## <a name="next-steps"></a>Следующие шаги

Используйте документацию по API V3 для обновления существующих вызовов RESTFUL к API-интерфейсам [конечной точки](https://aka.ms/luis-api-v3) Luis. 