---
title: Веб-перехватчики — службы распознавания речи
titlesuffix: Azure Cognitive Services
description: Веб-перехватчики — обратных звонков HTTP, которые идеально подходят для оптимизации решения при работе с длительно выполняющихся процессов как imports, адаптации, тестировании точности или транскрипций долго выполняющиеся файлов.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 7b47d4fc3aa4a1a50e441e668a856703c67045ae
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59581017"
---
# <a name="webhooks-for-speech-services"></a>Веб-перехватчики для службы распознавания речи

Веб-перехватчики похожи на обратные вызовы HTTP, которые позволяют приложению принимать данные от служб речи, когда она станет доступной. Использование веб-перехватчиков, можно оптимизировать использование наши интерфейсы REST API, устраняя необходимость в непрерывном опросе ответа. В следующих разделах вы узнаете, как использовать веб-перехватчиков со службами распознавания речи.

## <a name="supported-operations"></a>Поддерживаемые операции

Службы речи поддержка все длительные операции веб-перехватчики. Каждый из указанных ниже операций можно инициировать обратный вызов HTTP, по завершении. 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Теперь давайте создадим веб-перехватчика.

## <a name="create-a-webhook"></a>Создание веб-перехватчика

Давайте создадим веб-перехватчика для автономных транскрипции. Сценарий: пользователь имеет длительную звуковой файл, они хотели транскрипция асинхронно с помощью API пакетной службы расшифровка дикторского текста. 

Параметры конфигурации для запроса, представлены в формате JSON:

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
Требуется во всех запросах POST к API пакетной транскрипцию `name`. `description` И `properties` параметры являются необязательными.

`Active` Свойство используется для переключения обратного вызова в URL-адрес, включение и отключение без необходимости удаления и повторной регистрации веб-перехватчика. Если необходимо только обратного вызова один раз после процесс будет иметь полный, удалите веб-перехватчика и коммутатор `Active` значение false.

Тип события `TranscriptionCompletion` предоставляется в массиве события. Он выполняет обратный вызов к конечной точке при получении транскрипция в конечное состояние (`Succeeded` или `Failed`). Время обратного вызова зарегистрированным URL-адресом, запрос будет содержать `X-MicrosoftSpeechServices-Event` заголовок, содержащий один из зарегистрированных типов событий. Есть один запрос в тип зарегистрированного события. 

Есть один тип событий, который нельзя подписаться на. Это `Ping` тип события. Запрос с этим типом отправляется на URL-адрес, после завершения создания веб-перехватчика при использовании URL-адрес проверки связи (см. ниже).  

В конфигурации `url` свойство является обязательным. Этот URL-адрес отправляются запросы POST. `secret` Используется для создания хэша SHA256 полезных данных, с использованием секрета как ключ HMAC. Хэша устанавливается как `X-MicrosoftSpeechServices-Signature` заголовка во время обратного вызова к зарегистрированным URL-адресом. Этот заголовок является в кодировке Base64.

В этом примере показано, как выполнять проверку полезных данных с помощью C#:

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
В этом фрагменте кода `secret` декодировать и проверены. Также можно заметить, что была переключена тип события веб-перехватчика. В настоящее время есть одно событие в завершенной транскрипции. Код выполняют пять раз для каждого события (с задержкой в одну второй) перед прекращением.

### <a name="other-webhook-operations"></a>Другие операции веб-перехватчика

Чтобы получить все зарегистрированные веб-перехватчиков: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Для получения одного конкретного веб-перехватчика: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Для удаления одного конкретного веб-перехватчика: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> В приведенном выше примере область — «westus». Это должны быть заменены регион, где, вы создали ресурс службы распознавания речи на портале Azure.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping тела: пустой

Отправляет запрос POST к зарегистрированным URL-адресом. Запрос содержит `X-MicrosoftSpeechServices-Event` заголовок со значением сообщения проверки связи. Если веб-перехватчика был зарегистрирован с помощью секрета, он будет содержать `X-MicrosoftSpeechServices-Signature` заголовок с хэш SHA256 с секретным кодом как ключ HMAC полезных данных. Хэш-код — в кодировке Base64. 

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test тела: пустой

Отправляет запрос POST к зарегистрированным URL-адресом, если сущность для типа соответствующего события (транскрибирования) присутствует в системе и в соответствующее состояние. Полезные данные будут созданы из последней сущность, которая бы были активированы веб-перехватчик. Если сущность не существует, POST вернет 204. Если запрос теста может быть выполнено, он будет отвечать с 200. Текст запроса такой же фигуры, указанного в запросе GET для конкретного объекта, что веб-перехватчик подписался на (например расшифровка дикторского текста). Запрос будет иметь `X-MicrosoftSpeechServices-Event` и `X-MicrosoftSpeechServices-Signature` заголовки, как описано выше.

### <a name="run-a-test"></a>Выполнение теста

Можно сделать быстрой проверки на веб-сайте https://bin.webhookrelay.com. После этого вы можете получить вызов резервного URL-адреса, передаваемый в качестве параметра запроса HTTP POST для создания веб-перехватчика, описанные ранее в этом документе.

## <a name="next-steps"></a>Дальнейшие действия

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
