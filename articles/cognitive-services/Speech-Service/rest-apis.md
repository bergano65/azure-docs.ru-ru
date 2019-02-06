---
title: REST API служб распознавания речи — службы распознавания речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать REST API преобразования речи в текст и текста в речь. Из этой статьи вы узнаете о вариантах авторизации, параметрах запроса, структуре запроса и получении ответа.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: f369ab0ec8c460137f7e2b16a7f2696357d84c50
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247448"
---
# <a name="speech-service-rest-apis"></a>REST API службы "Речь"

Служба распознавания речи позволяет преобразовывать речь в текст и текст в речь с помощью набора REST API, не используя [Speech SDK](speech-sdk.md). Каждая доступная конечная точка связана с регионом. Приложению нужен ключ подписки для конечной точки, которую вы планируете использовать.

Прежде чем использовать REST API, учтите:
* Запросы на преобразование речи в текст с помощью REST API могут содержать только 10 секунд записанного звука.
* REST API преобразования речи в текст возвращает только окончательные результаты. Частичные результаты не предоставляются.
* Для вызова REST API преобразования текста в речь требуется заголовок авторизации. Это означает, что для доступа к службе необходимо выполнить обмен маркерами. Дополнительные сведения см. в разделе [Authenticate to the Speech API](#authentication) (Аутентификация в API речи).

## <a name="authentication"></a>Authentication

Для каждого вызова REST API преобразования речи в текст или текста в речь требуется заголовок авторизации. В этой таблице показано, какие заголовки поддерживаются для каждой службы:

| Поддерживаемые заголовки авторизации | Преобразование речи в текст. | Преобразование текста в речь |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Yes | Нет  |
| Авторизация: Носитель | Yes | Yes |

При использовании заголовка `Ocp-Apim-Subscription-Key` необходимо предоставить только ключ подписки. Например: 

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

При использовании заголовка `Authorization: Bearer` необходимо выполнять запрос к конечной точке `issueToken`. В этом запросе ключ подписки обменивается на маркер доступа, действительный в течение 10 минут. В следующих разделах вы узнаете, как получать, использовать и обновлять маркер.

### <a name="how-to-get-an-access-token"></a>Как получить маркер доступа

Чтобы получить маркер доступа, необходимо отправить запрос в конечную точку `issueToken`, используя заголовок `Ocp-Apim-Subscription-Key` и ключ подписки.

Поддерживаются следующие регионы и конечные точки:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Используйте эти примеры, чтобы создать запрос на получение маркера доступа.

#### <a name="http-sample"></a>Пример HTTP

Это простой HTTP-запрос для получения маркера. Замените `YOUR_SUBSCRIPTION_KEY` своим ключом подписки на службу распознавания речи. Если ваша подписка не находится в регионе "Западная часть США", замените заголовок `Host` на имя узла в вашем регионе.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Тело ответа содержит маркер доступа в формате JWT.

#### <a name="powershell-sample"></a>Пример для PowerShell

Это простой сценарий PowerShell для получения маркера доступа. Замените `YOUR_SUBSCRIPTION_KEY` своим ключом подписки на службу распознавания речи. Обязательно используйте правильную конечную точку для региона, который соответствует вашей подписке. В этом примере используется конечная точка для западной части США.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>Пример cURL

cURL — это программа командной строки, доступная в Linux (и в подсистеме Windows для Linux). Эта команда cURL показывает, как получить маркер доступа. Замените `YOUR_SUBSCRIPTION_KEY` своим ключом подписки на службу распознавания речи. Обязательно используйте правильную конечную точку для региона, который соответствует вашей подписке. В этом примере используется конечная точка для западной части США.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Пример на языке C#

Этот класс C# показывает, как получить маркер доступа. Передайте ключ подписки на службу распознавания речи при создании экземпляра класса. Если регион вашей подписки — не западная часть США, измените значение `FetchTokenUri` в соответствии с регионом своей подписки.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>Как использовать маркер доступа

Маркер доступа должен отправляться в службу в виде заголовка `Authorization: Bearer <TOKEN>`. Каждый маркер доступа действителен в течение 10 минут. Вы можете получить новый маркер в любое время, но чтобы уменьшить сетевой трафик и задержку, мы рекомендуем использовать один маркер в течение девяти минут.

Ниже приведен пример HTTP-запроса REST API преобразования текста в речь.

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>Как обновить маркер доступа, используя C#

Этот код C# представляет собой альтернативу для класса, представленного выше. Класс `Authentication` автоматически получает новый маркер доступа каждые девять минут, используя таймер. Такой подход гарантирует, что во время выполнения программы всегда доступен допустимый маркер.

> [!NOTE]
> Вместо использования таймера вы можете хранить метки времени, когда был получен последний маркер. Затем вы можете запрашивать новый, только в случае приближения окончания срока действия. Такой подход позволяет избежать ненужных запросов новых маркеров и может оказаться более подходящим для программ, редко отправляющих запросы речи.

Как и раньше, убедитесь, что значение `FetchTokenUri` соответствует вашему региону подписки. Передайте ключ подписки при создании экземпляра класса.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>API преобразования речи в текст

REST API преобразования речи в текст поддерживает только короткие фразы. Запросы могут содержать аудиозаписи длиной до 10 секунд общей продолжительностью 14 секунд. REST API возвращает только окончательные результаты (не частичные и не промежуточные).

Если в вашем приложении требуется отправлять более длинные аудиозаписи, используйте [Speech SDK](speech-sdk.md) или [пакетное транскрибирование](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Регионы и конечные точки

Для транскрибирования речи в текст с помощью REST API поддерживаются следующие регионы. Выберите конечную точку, которая соответствует региону подписки.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Параметры запроса

Следующие параметры можно включать в строку запроса REST.

| Параметр | ОПИСАНИЕ | Обязательный или необязательный |
|-----------|-------------|---------------------|
| `language` | Определяет распознаваемую устную речь. См. сведения о [поддерживаемых языках](language-support.md#speech-to-text). | Обязательно |
| `format` | Указывает формат результатов. Допустимые значения: `simple` и `detailed`. К простым результатам относятся `RecognitionStatus`, `DisplayText`, `Offset` и `Duration`. К подробным ответам относятся сложные результаты со значениями достоверности и четыре различных представления. Значение по умолчанию — `simple`. | Необязательно |
| `profanity` | Указывает, как обрабатывать ненормативную лексику в результатах распознавания. Допустимые значения: `masked` — заменяет ненормативную лексику звездочками, `removed` — удаляет всю ненормативную лексику из результата, `raw` — включает ненормативную лексику в результат. Значение по умолчанию — `masked`. | Необязательно |

### <a name="request-headers"></a>Заголовки запросов

В этой таблице перечислены обязательные и необязательные заголовки для запросов на преобразование речи в текст.

|Заголовок| ОПИСАНИЕ | Обязательный или необязательный |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Ключ подписки на службу распознавания речи. | Обязательный, если не предоставлен заголовок `Authorization`. |
| `Authorization` | Маркеру авторизации предшествует слово `Bearer`. Дополнительные сведения см. в разделе [Authenticate to the Speech API](#authentication) (Аутентификация в API речи). | Обязательный, если не предоставлен заголовок `Ocp-Apim-Subscription-Key`. |
| `Content-type` | Описывает формат и кодек предоставленных аудиоданных. Допустимые значения: `audio/wav; codecs=audio/pcm; samplerate=16000` и `audio/ogg; codecs=opus`. | Обязательно |
| `Transfer-Encoding` | Указывает, что отправляются фрагментированные аудиоданные, а не один файл. Используйте этот заголовок только при фрагментации аудиоданных. | Необязательно |
| `Expect` | Если используется блочная передача (в предыдущем параметре задано chunked), отправьте `Expect: 100-continue`. Служба распознавания речи подтверждает первоначальный запрос и ожидает передачи дополнительных данных.| Обязательный, если отправляются фрагментированные аудиоданные. |
| `Accept` | Если указан, этот заголовок должен иметь значение `application/json`. Служба распознавания речи предоставляет результаты в формате JSON. Некоторые платформы веб-запроса предоставляют несовместимое значение по умолчанию, если оно не указано, поэтому рекомендуется всегда включать `Accept`. | Необязательный, но рекомендуется включать. |

### <a name="audio-formats"></a>Форматы аудио

Аудио отправляется в тексте HTTP-запроса `POST`. Аудиопоток должен иметь один из форматов, приведенных в следующей таблице:

| Формат | Кодек | Bitrate | Частота выборки |
|--------|-------|---------|-------------|
| WAV | PCM | 16-разрядный | 16 кГц, моно |
| OGG | OPUS | 16-разрядный | 16 кГц, моно |

>[!NOTE]
>Указанные выше форматы поддерживаются в службе "Речь" с помощью REST API и WebSocket. [Пакет SDK службы "Речь"](speech-sdk.md) в настоящее время поддерживает только формат WAV кодека PCM.

### <a name="sample-request"></a>Пример запроса

Это типичный HTTP-запрос. Приведенный ниже пример включает имя узла и обязательные заголовки. Важно отметить, что служба также ожидает передачи аудиоданных, которые не включены в этом примере. Как упоминалось ранее, фрагментация рекомендуется, но не обязательна.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>Коды состояния HTTP

Код состояния HTTP для каждого ответа указывает на успешное выполнение или возникновение распространенных ошибок.

| HTTP status code (Код состояния HTTP) | ОПИСАНИЕ | Возможная причина |
|------------------|-------------|-----------------|
| 100 | Continue | Первоначальный запрос принят. Перейдите к отправке данных, которые остались. (Используется в случае блочной передачи.) |
| 200 | ОК | Запрос выполнен успешно; текст ответа представляет собой объект JSON. |
| 400 | Недопустимый запрос | Код языка не указан или не поддерживается; недопустимый звуковой файл. |
| 401 | Не авторизовано | Ключ подписки или маркер авторизации является недопустимым в указанном регионе, или недопустимая конечная точка. |
| 403 | Запрещено | Отсутствует ключ подписки или маркер авторизации. |

### <a name="chunked-transfer"></a>Блочная передача

Фрагментированная передача (`Transfer-Encoding: chunked`) может помочь снизить задержки распознавания, так как она позволяет службе распознавания речи начать обработку звукового файла в процессе его передачи. REST API не поддерживает частичные или промежуточные результаты. Этот параметр предназначен исключительно для повышения скорости реагирования.

Этот пример кода показывает, как отправлять фрагментированное аудио. Только первый фрагмент данных должен содержать заголовок звукового файла. `request` — это объект HTTPWebRequest, подключенный к соответствующей конечной точке REST. `audioFile` — путь к звуковому файлу на диске.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Параметры ответа

Результаты предоставляются в формате JSON. Формат `simple` включает эти поля верхнего уровня.

| Параметр | ОПИСАНИЕ  |
|-----------|--------------|
|`RecognitionStatus`|Состояние, например, `Success` в случае успешного распознавания. Ознакомьтесь со следующей таблицей.|
|`DisplayText`|Распознанный текст после расстановки прописных букв, знаков препинания, обратной нормализации текста (преобразование произносимого текста в более короткие формы, например 200 вместо "двести" или "проф. Преображенский" вместо "профессор Преображенский") и маскировки ненормативной лексики. Появляется только в случае успешного распознавания.|
|`Offset`|Время (в единицах 100 нс), с которого в звуковом потоке начинается распознанная речь.|
|`Duration`|Длительность (в единицах 100 нс) распознанной речи в звуковом потоке.|

Поле `RecognitionStatus` может содержать следующие значения:

| Status | ОПИСАНИЕ |
|--------|-------------|
| `Success` | Речь распознана, и присутствует поле `DisplayText`. |
| `NoMatch` | В аудиопотоке был обнаружена речь, но не были сопоставлены слова в целевом языке. Обычно означает, что язык распознавания — это не тот язык, на котором разговаривает пользователь. |
| `InitialSilenceTimeout` | Начало аудиопотока содержит только тишину, и время ожидания на появление речи в службе истекло. |
| `BabbleTimeout` | Начало аудиопотока содержит только шум, и время ожидания на появление речи в службе истекло. |
| `Error` | Служба распознавания обнаружила внутреннюю ошибку и не может продолжить работу. Повторите попытку, если это возможно. |

> [!NOTE]
> Если звуковой файл содержит только ненормативную лексику, а параметр запроса `profanity` имеет значение `remove`, служба не возвращает результат распознавания речи.

Формат `detailed` включает те же данные, что и формат `simple`, а также `NBest`, список альтернативных интерпретаций одного результата распознавания речи. Эти результаты располагаются в порядке от наиболее вероятного до наименее вероятного. Первая запись совпадает с основным результатом распознавания.  При использовании формата `detailed` параметр `DisplayText` указывается как `Display` для каждого результата в списке `NBest`.

Каждый объект в списке `NBest` включает:

| Параметр | ОПИСАНИЕ |
|-----------|-------------|
| `Confidence` | Оценка достоверности записи, от 0,0 (недостоверно) до 1,0 (полная достоверность) |
| `Lexical` | Лексическая форма распознанного текста: конкретные распознанные слова. |
| `ITN` | Форма распознанного текста после обратной нормализации ("каноническая" форма) с номерами телефонов, числами, сокращениями ("профессор Преображенский" до "проф. Преображенский") и другими выполненными преобразованиями. |
| `MaskedITN` | Форма ITN с применением маскировки ненормативной лексики, если запрашивалась. |
| `Display` | Отображаемая форма распознанного текста с расстановкой знаков препинания и прописных букв. Этот параметр совпадает с параметром `DisplayText`, если указан формат `simple`. |

### <a name="sample-responses"></a>Образцы ответов

Ниже приведен типичный ответ для распознавания `simple`.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Ниже приведен типичный ответ для распознавания `detailed`.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech-api"></a>API преобразования текста в речь

REST API преобразования текста в речь поддерживает нейронные модели и стандартные голосовые модели для преобразования текста в речь. Каждая из таких моделей поддерживает определенный язык и диалект, определяемые языковым стандартом.

* Полный список голосовых моделей см. в разделе о [поддерживаемых языках](language-support.md#text-to-speech).
* Сведения о доступности по регионам см. на [этой странице](regions.md#text-to-speech).

> [!IMPORTANT]
> Стандартные, пользовательские и нейронные голосовые модели предлагаются по разным ценам. Дополнительные сведения см. на странице [цен](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="request-headers"></a>Заголовки запросов

В этой таблице перечислены обязательные и необязательные заголовки для запросов на преобразование речи в текст.

| Заголовок | ОПИСАНИЕ | Обязательный или необязательный |
|--------|-------------|---------------------|
| `Authorization` | Маркеру авторизации предшествует слово `Bearer`. Дополнительную информацию см. в разделе [Аутентификация](#authentication). | Обязательно |
| `Content-Type` | Указывает тип контента для предоставленного текста. Допустимое значение: `application/ssml+xml`. | Обязательно |
| `X-Microsoft-OutputFormat` | Указывает формат аудиосигнала. Полный список допустимых значений см. в разделе [Аудиосигналы](#audio-outputs). | Обязательно |
| `User-Agent` | Имя приложения. Должно быть короче 255 символов. | Обязательно |

### <a name="audio-outputs"></a>Аудиосигналы

Это список поддерживаемых аудиоформатов, которые отправляются в каждом запросе в виде заголовка `X-Microsoft-OutputFormat`. Каждый включает в себя скорость и тип кодирования. Служба "Речь" поддерживает выходные аудиоданные с частотой дискретизации 24 кГц, 16 кГц и 8 кГц.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Если у выбранного голоса и формата вывода будет разная скорость передачи звука, то при необходимости звук будет обработан повторно. Тем не менее голоса частотой 24 кГц не поддерживают форматы выходных данных `audio-16khz-16kbps-mono-siren` и `riff-16khz-16kbps-mono-siren`.

### <a name="request-body"></a>Тело запроса

Текст каждого запроса `POST` отправляется с соблюдением синтаксиса [языка SSML](speech-synthesis-markup.md). SSML позволяет выбрать голос и язык для синтезированной речи, возвращаемой службой преобразования текста в речь. Полный список поддерживаемых голосовых моделей см. в разделе о [поддерживаемых языках](language-support.md#text-to-speech).

> [!NOTE]
> Если применяется пользовательская голосовая модель, основной текст запроса можно отправить в формате обычного текста (ASCII или UTF-8).

### <a name="sample-request"></a>Пример запроса

В этом HTTP-запросе используется SSML для указания голоса и языка. Тело должно содержать не более 1000 символов.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>Коды состояния HTTP

Код состояния HTTP для каждого ответа указывает на успешное выполнение или возникновение распространенных ошибок.

| HTTP status code (Код состояния HTTP) | ОПИСАНИЕ | Возможная причина |
|------------------|-------------|-----------------|
| 200 | ОК | Запрос выполнен успешно; текст ответа представляет собой звуковой файл. |
| 400 | Ошибка запроса | Обязательный параметр отсутствует, пустой или его значение равно нулю. Или переданное либо обязательному, либо необязательному параметру значение является недопустимым. Распространенной проблемой является слишком длинный заголовок. |
| 401 | Не авторизовано | Запрос не авторизован. Убедитесь, что ваш ключ подписки или маркер являются допустимыми и находятся в правильных регионах. |
| 413 | Размер запрашиваемой сущности слишком большой | Входные данные SSM превышают длину 1024 символа. |
| 429 | Слишком много запросов | Вы превысили квоту или частоту запросов, разрешенных для вашей подписки. |
| 502 | Недопустимый шлюз | Проблема с сетью или сервером. Может также указывать на недопустимые заголовки. |

Если состояние HTTP — `200 OK`, текст ответа содержит звуковой файл в затребованном формате. Этот файл можно воспроизводить при передаче, сохранении в буфер или сохранении в файл.

## <a name="next-steps"></a>Дополнительная информация

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
