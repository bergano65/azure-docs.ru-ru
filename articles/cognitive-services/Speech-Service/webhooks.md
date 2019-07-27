---
title: Веб-перехватчики — служба речи
titleSuffix: Azure Cognitive Services
description: Веб-перехватчики — это обратные вызовы HTTP, оптимальные для оптимизации вашего решения при работе с долгосрочными процессами, такими как импорт, адаптация, проверка точности или транскрипция длительных файлов.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558799"
---
# <a name="webhooks-for-speech-services"></a>Веб-перехватчики для речевых служб

Веб-перехватчики подобны обратным вызовам HTTP, которые позволяют приложению принимать данные от речевых служб, когда они становятся доступными. С помощью веб-перехватчиков можно оптимизировать использование наших API-интерфейсов, устраняя необходимость постоянного опроса ответа. В следующих разделах вы узнаете, как использовать веб-перехватчики со службами речи.

## <a name="supported-operations"></a>Поддерживаемые операции

Службы речи поддерживают веб-перехватчики для всех долго выполняющихся операций. Каждая из перечисленных ниже операций может активировать обратный вызов HTTP после завершения.

* датаимпорткомплетион
* моделадаптатионкомплетион
* аккурацитесткомплетион
* транскриптионкомплетион
* ендпоинтдеплойменткомплетион
* ендпоинтдатаколлектионкомплетион

Теперь создадим веб-перехватчик.

## <a name="create-a-webhook"></a>Создание веб-перехватчика

Давайте создадим веб-перехватчик для автономной транскрипции. Сценарий: у пользователя есть длинный звуковой файл, который транскрипция в асинхронном режиме с помощью API-интерфейса для записи пакетов.

Веб-перехватчики можно создать, выполнив запрос POST к HTTPS://\<Region\>. Cris.AI/API/speechtotext/v2.1/Transcriptions/hooks.

Параметры конфигурации для запроса предоставляются в виде JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Для всех запросов POST к API- `name`интерфейсу записи пакетов требуется. Параметры `description` и`properties` являются необязательными.

`Active` Свойство используется для переключения обратного вызова в URL-адрес без необходимости удаления и повторного создания регистрации веб-перехватчика. Если нужно вызвать только один раз после завершения процесса, удалите веб-перехватчик и переключайте `Active` свойство в значение false.

Тип `TranscriptionCompletion` события указывается в массиве событий. При переходе в состояние терминала (`Succeeded` или `Failed`) будет выполнен обратный вызов к конечной точке. При обратном вызове по зарегистрированному URL-адресу запрос `X-MicrosoftSpeechServices-Event` будет содержать заголовок, содержащий один из зарегистрированных типов событий. Для каждого зарегистрированного типа событий существует один запрос.

Существует один тип события, на который нельзя подписываться. Это тип события `Ping` . Запрос с этим типом отправляется на URL-адрес по завершении создания веб-перехватчика при использовании URL для проверки связи (см. ниже).  

В конфигурации `url` свойство является обязательным. Запросы POST отправляются по этому URL-адресу. `secret` Используется для создания хэш-кода SHA256 полезных данных с секретом в качестве ключа HMAC. Хэш задается в качестве `X-MicrosoftSpeechServices-Signature` заголовка при обратном вызове по зарегистрированному URL-адресу. Этот заголовок кодируется в кодировке Base64.

В этом примере показано, как проверить полезную нагрузку C#с помощью:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
В этом фрагменте `secret` кода код декодирован и проверен. Также обратите внимание, что тип события веб-перехватчика был переключен. В настоящее время существует одно событие для каждой завершенной транскрипции. Код повторяет попытку пять раз для каждого события (с одной задержкой).

### <a name="other-webhook-operations"></a>Другие операции веб-перехватчика

Чтобы получить все зарегистрированные веб-перехватчики, сделайте следующее: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Чтобы получить один конкретный веб-перехватчик, сделайте следующее: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Чтобы удалить один из указанных веб-перехватчиков: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> В приведенном выше примере регионом является "westus". Он должен быть заменен регионом, в котором вы создали ресурс служб речи в портал Azure.

Тело https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping записи: пусто

Отправляет запрос POST по зарегистрированному URL-адресу. Запрос содержит `X-MicrosoftSpeechServices-Event` заголовок со значением ping. Если веб-перехватчик зарегистрирован в секрете, он будет содержать `X-MicrosoftSpeechServices-Signature` заголовок с хэшем SHA256 полезных данных с секретом в качестве ключа HMAC. Хэш кодируется в кодировке Base64.

Тело https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test записи: пусто

Отправляет запрос POST по зарегистрированному URL-адресу, если сущность для типа подписанного события (транскрипция) имеется в системе и находится в соответствующем состоянии. Полезная нагрузка будет сформирована из последней сущности, которая вызвала бы вызов веб-перехватчика. Если сущность отсутствует, запись будет отвечать на 204. Если тестовый запрос может быть выполнен, он будет отвечать на 200. Текст запроса имеет ту же форму, что и в запросе GET для определенной сущности, на которую подписан веб-перехватчик (для транскрипции экземпляра). Запрос будет содержать `X-MicrosoftSpeechServices-Event` заголовки и `X-MicrosoftSpeechServices-Signature` , как описано выше.

### <a name="run-a-test"></a>Выполнение теста

Быстрый тест можно выполнить с помощью веб-сайта https://bin.webhookrelay.com. После этого можно получить URL-адреса обратного вызова для передачи в качестве параметра в HTTP POST для создания веб-перехватчика, описанного ранее в документе.

Щелкните "создать контейнер" и следуйте инструкциям на экране, чтобы получить обработчик. Затем используйте сведения, приведенные на этой странице, чтобы зарегистрировать обработчик в службе распознавания речи. Полезная нагрузка сообщения ретранслятора — в ответ на завершение транскрипции выглядит следующим образом:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
Сообщение содержит URL-адрес записи и модели, используемые для транскрипция этой записи.

## <a name="next-steps"></a>Следующие шаги

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
