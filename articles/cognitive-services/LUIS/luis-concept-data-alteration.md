---
title: Изменение данных — LUIS
titleSuffix: Azure Cognitive Services
description: Узнайте, как изменить данные перед прогнозированием в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 734389c92ede88d336df60a1a79a738d2abcfa92
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703166"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Изменение данных высказываний до или во время прогнозирования
LUIS предоставляет способы управления высказыванием до или во время прогнозирования. Сюда входит [исправление орфографических](luis-tutorial-bing-spellcheck.md)ошибок и устранение проблем с часовым поясом для предварительно созданной [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Исправление орфографических ошибок в высказывании
Для исправления орфографических ошибок в высказывании LUIS использует [API проверки орфографии Bing версии 7](../Bing-Spell-Check/overview.md). LUIS требуется ключ, связанный с этой службой. Создайте ключ, а затем добавьте его в качестве параметра QueryString в [конечную точку](https://go.microsoft.com/fwlink/?linkid=2092356). 

Орфографические ошибки можно также исправить на панели **Test** (Тестирование) путем [ввода ключа](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Ключ хранится в виде переменной сеанса в браузере для панели тестирования. Добавляйте ключ на панель тестирования в каждом сеансе браузера, где необходимо исправить орфографические ошибки. 

При применении ключа на панели тестирования и в конечной точке учитывается квота на [использование ключа](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/). LUIS реализует ограничения проверки орфографии Bing на длину текста. 

Чтобы исправления орфографических ошибок вступили в силу, конечной точке требуется два параметра.

|Параметр|Значение|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|Ключ конечной точки [API проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Когда [API проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) обнаруживает ошибку, из конечной точки возвращаются исходное выражение, исправленное выражение и прогнозирование.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>Список разрешенных слов
API проверки орфографии Bing, используемый в LUIS, не поддерживает список игнорируемых слов во время изменений проверки орфографии. Если необходимо разрешить список слов или акронимов, обработайте utterance в клиентском приложении перед отправкой utterance в LUIS для прогнозирования намерения.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Изменение часового пояса предварительно созданной сущности datetimeV2
Когда приложение LUIS использует предварительно созданную сущность [datetimeV2](luis-reference-prebuilt-datetimev2.md) , в ответе прогноза может возвращаться значение DateTime. Часовой пояс запроса используется для определения правильного возвращаемого значения даты и времени. Если запрос поступает от бота или другого централизованного приложения до передачи в LUIS, исправьте часовой пояс, используемый LUIS. 

### <a name="endpoint-querystring-parameter"></a>Параметр QueryString конечной точки
Часовой пояс исправляется путем добавления часового пояса пользователя к [конечной точке](https://go.microsoft.com/fwlink/?linkid=2092356) с помощью параметра `timezoneOffset`. Чтобы изменить время, значение `timezoneOffset` должно быть положительным или отрицательным числом в минутах.  

|Параметр|Значение|
|--|--|
|`timezoneOffset`|положительное или отрицательное число в минутах|

### <a name="daylight-savings-example"></a>Пример перехода на летнее время
Чтобы скорректировать возвращенную предварительно созданную сущность datetimeV2 для перехода на летнее время, следует использовать параметр QueryString `timezoneOffset` со значением +/– в минутах для запроса [конечной точки](https://go.microsoft.com/fwlink/?linkid=2092356).

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Запрос конечной точки прогнозирования v2](#tab/V2)

Добавьте 60 минут: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Удалите 60 минут: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 запрос конечной точки прогнозирования](#tab/V3)

Добавьте 60 минут:

HTTPS://{регион}. API. turn. Microsoft. com/Luis/v 3.0-Предварительная версия/приложения/{appId}/слоты/рабочий/прогноз? запрос = включить индикаторы? **timezoneOffset = 60**& проверки орфографии = {boolean} & Bing-правописание-Check-Subscription-Key = {строка} & log = {Boolean}

Удалите 60 минут: 

HTTPS://{регион}. API. turn. Microsoft. com/Luis/v 3.0-Предварительная версия/приложения/{appId}/слоты/рабочий/прогноз? запрос = включить индикаторы? **timezoneOffset =-60 & проверки**орфографии = {boolean} & Bing-орфографические проверки-подписка — ключ = {строка} & log = {Boolean}

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Код на C# определяет правильное значение timezoneOffset
В следующем коде на C# для определения правильного значения `timezoneOffset` на основе системного времени используется метод [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) класса [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo):

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Исправление орфографических ошибок с помощью этого руководства](luis-tutorial-bing-spellcheck.md)
