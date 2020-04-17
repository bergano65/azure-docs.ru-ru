---
title: Изменения конечных точек прогнозирования в API V3
description: АДИ прогнозирования прогнозирования запроса V3 изменились. Используйте это руководство, чтобы понять, как перейти на ApIs конечных точек версии 3.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 4b6d28b24ffc6c0a848d1c7a34e863da0606d936
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530391"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Изменения конечных точек прогнозирования для V3

АДИ прогнозирования прогнозирования запроса V3 изменились. Используйте это руководство, чтобы понять, как перейти на ApIs конечных точек версии 3.

**Общедоступный статус** - этот API V3 включает значительные изменения запроса и ответа JSON от V2 API.

API V3 предоставляет следующие новые функции:

* [Внешние субъекты](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Динамические списки](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Предварительно построенная сущность JSON изменяется](#prebuilt-entity-changes)

[Запрос](#request-changes) и ответ [на](#response-changes) конечную точку прогнозирования имеют значительные изменения для поддержки новых функций, перечисленных выше, включая следующие:

* [Изменения объекта реагирования](#top-level-json-changes)
* [Ссылки на имя роли сущности вместо имени сущности](#entity-role-name-instead-of-entity-name)
* [Свойства для обозначения сущностей в высказываниях](#marking-placement-of-entities-in-utterances)

[Справочная документация](https://aka.ms/luis-api-v3) доступна для V3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 изменяется от предварительного просмотра к GA

V3 внес следующие изменения в рамках перехода на GA:

* Следующие предварительно построенные объекты имеют различные ответы JSON:
    * [ОрдиналV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Измеримое имя ключа `units` единицы от к`unit`

* Запрос тела JSON изменения:
    * от `preferExternalEntities` до`preferExternalEntities`
    * дополнительный `score` параметр для внешних сущностей

* Ответ тела JSON изменения:
    * `normalizedQuery`Удалены

## <a name="suggested-adoption-strategy"></a>Предлагаемая стратегия усыновления

Если вы используете Bot Framework, Bing Spell Check V7 или хотите перенести только авторство приложения LUIS, продолжайте использовать конечную точку V2.

Если вы не знаете, что ни одно из ваших клиентских приложений или интеграций (Bot Framework и Bing Spell Check V7) не влияет, и вам удобно мигрировать авторство приложения LUIS и конечную точку прогнозирования одновременно, начните использовать конечную точку прогнозирования V3. Конечная точка прогнозирования V2 будет по-прежнему доступна и является хорошей стратегией резервного копирования.


## <a name="not-supported"></a>Не поддерживается

### <a name="bing-spell-check"></a>API Проверки орфографии Bing

Этот API не поддерживается в конечных точках прогнозирования V3 - продолжайте использовать конечную точку прогнозирования V2 API для коррекции правописания. Если вам нужна коррекция правописания при использовании V3 API, позвоните в клиентское приложение на API [проверки орфографии Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) и измените текст на правильное написание, прежде чем отправить текст на API LUIS.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Клиентские приложения Bot Framework и Службы ботов

Продолжайте использовать конечную точку прогнозирования V2 API до выпуска V4.7 платформы Bot.

## <a name="v2-api-deprecation"></a>V2 API Арфеация

API прогнозирования V2 не будет унижаться, по крайней мере 9 месяцев после предварительного просмотра V3, 8 июня 2020 года.

## <a name="endpoint-url-changes"></a>Изменения URL-адреса конечных точек

### <a name="changes-by-slot-name-and-version-name"></a>Изменения по имени слота и имени версии

Изменился формат вызова V3 ENDpoint HTTP.

Если вы хотите задать запрос по версии, сначала `"directVersionPublish":true`необходимо опубликовать через [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) с . Запрос конечной точки ссылки идентификатор версии вместо имени слота.

|ПРЕДИКЦИЯ API ВЕРСИЯ|METHOD|URL-адрес|
|--|--|--|
|V3|GET|https://<b>«РЕГИОН» (регион)</b>.api.cognitive.microsoft.com/luis/<b>прогноз</b>/<b>v3.0</b>/приложения/<b>«APP-ID»/слоты/</b><b>«SLOT-NAME»/predict?query</b>»<b>(КЕРИЯ)</b>|
|V3|POST|https://<b>«РЕГИОН»</b>.api.cognitive.microsoft.com/luis/<b>прогноз</b>/<b>v3.0</b>/приложения/<b>«APP-ID»/слоты/</b><b>«SLOT-NAME»/прогноз</b>|
|V2|GET|https://<b>«РЕГИОН» (api.cognitive.microsoft.com/luis/</b><b>прогноз</b>/<b>v3.0</b>/приложения/<b>«APP-ID»/версии/</b><b>«ВЕРСИЯ-ID»/прогноз?запрос</b>»<b>»КЕРИ»</b>|
|V2|POST|https://<b>«РЕГИОН» (api.cognitive.microsoft.com/luis/</b><b>прогноз</b>/<b>v3.0</b>/приложения/<b>«APP-ID»/версии/</b><b>«VERSION-ID»/прогноз</b>|

|Действительные значения для`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Запрос изменений

### <a name="query-string-changes"></a>Изменения строки запроса

API V3 имеет различные параметры строки запроса.

|Парам имя|Тип|Версия|По умолчанию|Назначение|
|--|--|--|--|--|
|`log`|Логическое|V2 & V3|false|Храните запрос в файле журнала. Значение по умолчанию — false.|
|`query`|строка|Только версия 3|Нет по умолчанию - это требуется в запросе GET|**В V2**, высказывание предсказать `q` находится в параметре. <br><br>**В V3,** функциональность передается `query` в параметре.|
|`show-all-intents`|Логическое|Только версия 3|false|Верните все намерения с соответствующей оценкой в **объекте prediction.intents.** Намерения возвращаются как объекты `intents` в родительском объекте. Это позволяет программному доступу без необходимости находить `prediction.intents.give`намерения в массиве: . В V2 они были возвращены в массиве. |
|`verbose`|Логическое|V2 & V3|false|**В V2**, когда установить на истину, все предсказанные намерения были возвращены. Если вам нужны все предсказанные намерения, используйте V3 param `show-all-intents`.<br><br>**В V3**этот параметр предоставляет только сведения о метаданных сущности прогнозирования сущности.  |
|`timezoneOffset`|строка|V2|-|Часовой пояс применяется к datetimeV2 субъектов.|
|`datetimeReference`|строка|V3|-|[Часовой пояс](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) применяется к datetimeV2 субъектов. Заменяется `timezoneOffset` на V2.|


### <a name="v3-post-body"></a>V3 POST тела

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Свойство|Тип|Версия|По умолчанию|Назначение|
|--|--|--|--|--|
|`dynamicLists`|массиве|Только версия 3|Необязательно.|[Динамические списки](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) позволяют расширить существующую обучаемую и опубликованную сущность списка, уже в приложении LUIS.|
|`externalEntities`|массиве|Только версия 3|Необязательно.|[Внешние сущности](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) дают приложению LUIS возможность идентифицировать и маркировать объекты во время выполнения, которые могут быть использованы в качестве функций для существующих объектов. |
|`options.datetimeReference`|строка|Только версия 3|Нет значения по умолчанию|Используется для определения [смещения datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Формат для datetimeReference является [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|Логическое|Только версия 3|false|Опознавай, используется ли [внешняя сущность пользователя (с тем же именем, что и существующая сущность)](schema-change-prediction-runtime.md#override-existing-model-predictions) или для прогнозирования используется существующая сущность в модели. |
|`query`|строка|Только версия 3|Обязательный элемент.|**В V2**, высказывание предсказать `q` находится в параметре. <br><br>**В V3,** функциональность передается `query` в параметре.|

## <a name="response-changes"></a>Изменения в ответах

Ответ на запрос JSON изменился, чтобы обеспечить более программный доступ к данным, используемым чаще всего.

### <a name="top-level-json-changes"></a>Изменения верхнего уровня JSON



Лучшие свойства JSON для V2, когда `verbose` установлен на истину, который возвращает `intents` все намерения и их оценки в собственности:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Лучшие свойства JSON для V3:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

Объект `intents` представляет собой неупорядоченный список. Не думайте, что первый ребенок `topIntent`в соответствии `intents` с . Вместо этого `topIntent` используйте значение, чтобы найти счет:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Изменения схемы JSON позволяют:

* Четкое различие между `query`оригинальным высказыванием, и возвращенным предсказанием, `prediction`.
* Упрощенный программный доступ к прогнозируемым данным. Вместо того, чтобы перечислять через массив в V2, вы можете получить доступ к значениям по **имени** как для намерений, так и для сущностей. Для прогнозируемых ролей сущности имя роли возвращается, поскольку оно уникально во всем приложении.
* Типы данных, если они определены, соблюдаются. Число больше не возвращается в виде строк.
* Различие между информацией первого приоритетного прогнозирования `$instance` и дополнительными метаданными, возвращенными в объект.

### <a name="entity-response-changes"></a>Изменения ответа сущности

#### <a name="marking-placement-of-entities-in-utterances"></a>Маркировка субъектов в высказываниях

**В V2**, лица была отмечена `startIndex` в `endIndex`высказывании с и .

**В V3**, сущность `startIndex` `entityLength`отмечена и .

#### <a name="access-instance-for-entity-metadata"></a>Доступ `$instance` к метаданным сущности

Если вам нужны метаданные сущности, строка `verbose=true` запроса должна использовать флаг, `$instance` а ответ содержит метаданные в объекте. Примеры приведены в ответах JSON в следующих разделах.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Каждая предсказанная сущность представлена как массив

Объект `prediction.entities.<entity-name>` содержит массив, потому что каждая сущность может быть предсказана более одного раза в высказывании.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Изменения предпостроенной сущности

Объект ответа V3 включает в себя изменения в предпостроенные объекты. Просмотрите [конкретные заранее построенные объекты,](luis-reference-prebuilt-entities.md) чтобы узнать больше.

#### <a name="list-entity-prediction-changes"></a>Изменения прогнозирования сущности списка

JSON для прогнозирования сущности списка изменился, чтобы быть массивом массивов:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Каждый внутренний массив соответствует тексту внутри высказывания. Внутренний объект представляет собой массив, поскольку один и тот же текст может отображаться в более чем одном подсписке сущности списка.

При отображении между объектом `entities` к объекту `$instance` сохраняется порядок объектов для прогнозов сущности списка.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Имя роли сущности вместо имени сущности

В V2 `entities` массив вернул все предсказанные сущности, а идентификатором является уникальный идентификатор. В V3, если сущность использует роли и прогноз предназначен для роли сущности, основным идентификатором является имя роли. Это возможно, потому что имена ролей сущности должны быть уникальными во всем приложении, включая другие модели (намерения, сущность) имена.

В следующем примере: рассмотрим высказывание, `Yellow Bird Lane`которое включает текст, . Этот текст прогнозируется `Location` как роль пользовательской сущности `Destination`.

|Текст высказывания|Имя сущности|Имя роли|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

В V2 сущность идентифицируется _по имени сущности_ с ролью свойства объекта:

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

В V3 сущность ссылается на _роль сущности,_ если прогноз предназначен для роли:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

В V3, тот же `verbose` результат с флагом, чтобы вернуть метаданные сущности:

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

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Продлить приложение во время прогнозирования

Узнайте [концепции](schema-change-prediction-runtime.md) о том, как расширить приложение во время выполнения прогноза.

## <a name="deprecation"></a>Устаревшее

API V2 не будет унижаться по крайней мере 9 месяцев после предварительного просмотра V3.

## <a name="next-steps"></a>Следующие шаги

Используйте документацию API V3 для обновления существующих вызовов REST в APIs [конечных точек](https://aka.ms/luis-api-v3) LUIS.
