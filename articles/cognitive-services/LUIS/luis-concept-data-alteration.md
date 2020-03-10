---
title: Изменение данных — LUIS
description: Узнайте, как изменить данные перед прогнозированием в службе "Распознавание речи" (LUIS).
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 5547724a6333d248a7ba4e9aeecaaa8f331feb7d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361187"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Изменение данных высказываний до или во время прогнозирования
LUIS предоставляет способы управления высказыванием до или во время прогнозирования. Сюда входит [исправление орфографических](luis-tutorial-bing-spellcheck.md)ошибок и устранение проблем с часовым поясом для предварительно созданной [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Исправление орфографических ошибок в высказывании


### <a name="v3-runtime"></a>Среда выполнения v3

Предварительная обработка текста для исправления правописания перед отправкой utterance в LUIS. Используйте пример фразы продолжительностью с правильным написанием, чтобы убедиться, что вы получаете правильные прогнозы.

Используйте [Проверка орфографии Bing](../bing-spell-check/overview.md) , чтобы исправить текст перед отправкой в Luis.

### <a name="prior-to-v3-runtime"></a>До версии 3 среды выполнения

Для исправления орфографических ошибок в высказывании LUIS использует [API проверки орфографии Bing версии 7](../Bing-Spell-Check/overview.md). LUIS требуется ключ, связанный с этой службой. Создайте ключ, а затем добавьте его в качестве параметра QueryString в [конечную точку](https://go.microsoft.com/fwlink/?linkid=2092356).

Чтобы исправления орфографических ошибок вступили в силу, конечной точке требуется два параметра.

|Параметр|Значение|
|--|--|
|`spellCheck`|Логическое|
|`bing-spell-check-subscription-key`|Ключ конечной точки [API проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Когда [API проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) обнаруживает ошибку, из конечной точки возвращаются исходное выражение, исправленное выражение и прогнозирование.

#### <a name="v2-prediction-endpoint-response"></a>[Отклик конечной точки прогнозирования V2](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[Отклик конечной точки прогнозирования V3](#tab/V3)

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

#### <a name="v2-prediction-endpoint-request"></a>[Запрос конечной точки прогнозирования V2](#tab/V2)

Добавьте 60 минут:

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Удалите 60 минут:

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

#### <a name="v3-prediction-endpoint-request"></a>[Запрос конечной точки прогнозирования V3](#tab/V3)

Добавьте 60 минут:

HTTPS://{регион}. API. turn. Microsoft. com/Luis/v 3.0-Предварительная версия/приложения/{appId}/слоты/рабочий/прогноз? запрос = включить индикаторы? **timezoneOffset = 60**& проверки орфографии = {boolean} & Bing-правописание-Check-Subscription-Key = {строка} & log = {Boolean}

Удалите 60 минут:

HTTPS://{регион}. API. turn. Microsoft. com/Luis/v 3.0-Предварительная версия/приложения/{appId}/слоты/рабочий/прогноз? запрос = включить индикаторы? **timezoneOffset =-60 & проверки**орфографии = {boolean} & Bing-орфографические проверки-подписка — ключ = {строка} & log = {Boolean}

Дополнительные сведения о [конечной точке прогнозирования V3](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Код на C# определяет правильное значение timezoneOffset
В следующем коде на C# для определения правильного значения [ на основе системного времени используется метод ](https://docs.microsoft.com/dotnet/api/system.timezoneinfo)FindSystemTimeZoneById[ класса ](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples)TimeZoneInfo`timezoneOffset`:

```csharp
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
