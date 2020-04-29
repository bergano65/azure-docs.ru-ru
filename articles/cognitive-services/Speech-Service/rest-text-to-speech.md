---
title: Справочник по API преобразования текста в речь (RESTFUL) — служба речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать REST API преобразования текста в речь. Из этой статьи вы узнаете о вариантах авторизации, параметрах запроса, структуре запроса и получении ответа.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 77bba9433052c00df671caf73198ff75356b1c9a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400167"
---
# <a name="text-to-speech-rest-api"></a>REST API преобразования текста в речь.

Служба распознавания речи позволяет [преобразовывать текст в синтезированные речевые функции](#convert-text-to-speech) и [получать список поддерживаемых голосов](#get-a-list-of-voices) для региона с помощью набора интерфейсов API. Каждая доступная конечная точка связана с регионом. Требуется ключ подписки для конечной точки или региона, который планируется использовать.

REST API преобразования текста в речь поддерживает нейронные модели и стандартные голосовые модели для преобразования текста в речь. Каждая из таких моделей поддерживает определенный язык и диалект, определяемые языковым стандартом.

* Полный список голосовых моделей см. в разделе о [поддерживаемых языках](language-support.md#text-to-speech).
* Сведения о доступности по регионам см. на [этой странице](regions.md#text-to-speech).

> [!IMPORTANT]
> Стандартные, пользовательские и нейронные голосовые модели предлагаются по разным ценам. Дополнительные сведения см. на странице [цен](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Перед использованием этого API изучите следующие сведения:

* Для вызова REST API преобразования текста в речь требуется заголовок авторизации. Это означает, что для доступа к службе необходимо выполнить обмен маркерами. Дополнительные сведения см. в разделе [Authenticate to the Speech API](#authentication) (Аутентификация в API речи).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Получение списка голосов

`voices/list` Конечная точка позволяет получить полный список голосов для определенного региона или конечной точки.

### <a name="regions-and-endpoints"></a>Регионы и конечные точки

| Регион | Конечная точка |
|--------|----------|
| Восточная Австралия | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Южная Бразилия | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Центральная Канада | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Центральная часть США | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Восточная Азия | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Восточная часть США | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| восточная часть США 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Центральная Франция | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Центральная Индия | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Восточная Япония | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Республика Корея, центральный регион | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Центрально-северная часть США | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Северная Европа | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Центрально-южная часть США | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Юго-Восточная Азия | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| южная часть Соединенного Королевства | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Западная Европа | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| западная часть США | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| западная часть США 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>Заголовки запроса

В этой таблице перечислены обязательные и необязательные заголовки для запросов преобразования текста в речь.

| Заголовок | Описание | Обязательный или необязательный |
|--------|-------------|---------------------|
| `Authorization` | Маркеру авторизации предшествует слово `Bearer`. Дополнительные сведения см. в разделе [Authenticate to the Speech API](#authentication) (Аутентификация в API речи). | Обязательный |

### <a name="request-body"></a>Тело запроса

Для `GET` запросов к этой конечной точке тело не требуется.

### <a name="sample-request"></a>Пример запроса

Для этого запроса требуется только заголовок Authorization.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Пример ответа

Этот ответ был усечен, чтобы продемонстрировать структуру ответа.

> [!NOTE]
> Доступность голоса зависит от региона или конечной точки.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES",
        "SampleRateHertz": "16000",
        "VoiceType": "Standard"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)",
        "ShortName": "zh-CN-XiaoxiaoNeural",
        "Gender": "Female",
        "Locale": "zh-CN",
        "SampleRateHertz": "24000",
        "VoiceType": "Neural"
    },

    ...
]
```

### <a name="http-status-codes"></a>Коды состояния HTTP

Код состояния HTTP для каждого ответа указывает на успешное выполнение или возникновение распространенных ошибок.

| HTTP status code (Код состояния HTTP) | Описание | Возможная причина |
|------------------|-------------|-----------------|
| 200 | OK | Запрос выполнен успешно. |
| 400 | Ошибка запроса | Обязательный параметр отсутствует, пустой или его значение равно нулю. Или переданное либо обязательному, либо необязательному параметру значение является недопустимым. Распространенной проблемой является слишком длинный заголовок. |
| 401 | Не авторизовано | Запрос не авторизован. Убедитесь, что ваш ключ подписки или маркер являются допустимыми и находятся в правильных регионах. |
| 429 | Слишком много запросов | Вы превысили квоту или частоту запросов, разрешенных для вашей подписки. |
| 502 | Недопустимый шлюз    | Проблема с сетью или сервером. Может также указывать на недопустимые заголовки. |


## <a name="convert-text-to-speech"></a>Преобразование текста в речь

`v1` Конечная точка позволяет преобразовать текст в речь с помощью [языка разметки речи (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Регионы и конечные точки

Для преобразования текста в речь с помощью REST API поддерживаются следующие регионы. Выберите конечную точку, которая соответствует региону подписки.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Заголовки запроса

В этой таблице перечислены обязательные и необязательные заголовки для запросов преобразования текста в речь.

| Заголовок | Описание | Обязательный или необязательный |
|--------|-------------|---------------------|
| `Authorization` | Маркеру авторизации предшествует слово `Bearer`. Дополнительные сведения см. в разделе [Authenticate to the Speech API](#authentication) (Аутентификация в API речи). | Обязательный |
| `Content-Type` | Указывает тип контента для предоставленного текста. Допустимое значение: `application/ssml+xml`. | Обязательный |
| `X-Microsoft-OutputFormat` | Указывает формат аудиосигнала. Полный список допустимых значений см. в разделе [Аудиосигналы](#audio-outputs). | Обязательный |
| `User-Agent` | Имя приложения. Указанное значение должно содержать менее 255 символов. | Обязательный |

### <a name="audio-outputs"></a>Аудиосигналы

Это список поддерживаемых аудиоформатов, которые отправляются в каждом запросе в виде заголовка `X-Microsoft-OutputFormat`. Каждый включает в себя скорость и тип кодирования. Служба распознавания речи поддерживает звуковые выходы 24 кГц, 16 кГц и 8 кГц.

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
> Если у выбранного голоса и формата вывода будет разная скорость передачи звука, то при необходимости звук будет обработан повторно. Однако в 24 кГц голоса не поддерживаются `audio-16khz-16kbps-mono-siren` форматы `riff-16khz-16kbps-mono-siren` вывода.

### <a name="request-body"></a>Тело запроса

Текст каждого запроса `POST` отправляется с соблюдением синтаксиса [языка SSML](speech-synthesis-markup.md). SSML позволяет выбрать голос и язык для синтезированной речи, возвращаемой службой преобразования текста в речь. Полный список поддерживаемых голосовых моделей см. в разделе о [поддерживаемых языках](language-support.md#text-to-speech).

> [!NOTE]
> Если применяется пользовательская голосовая модель, основной текст запроса можно отправить в формате обычного текста (ASCII или UTF-8).

### <a name="sample-request"></a>Пример запроса

В этом HTTP-запросе используется SSML для указания голоса и языка. Если длина текста слишком велика и полученный звук превышает 10 минут, он усекается до 10 минут. Иными словами, длина аудио не может превышать 10 минут.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Примеры для конкретного языка см. в наших кратких руководствах.

* [.NET Core, C #](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Коды состояния HTTP

Код состояния HTTP для каждого ответа указывает на успешное выполнение или возникновение распространенных ошибок.

| HTTP status code (Код состояния HTTP) | Описание | Возможная причина |
|------------------|-------------|-----------------|
| 200 | OK | Запрос выполнен успешно; текст ответа представляет собой звуковой файл. |
| 400 | Ошибка запроса | Обязательный параметр отсутствует, пустой или его значение равно нулю. Или переданное либо обязательному, либо необязательному параметру значение является недопустимым. Распространенной проблемой является слишком длинный заголовок. |
| 401 | Не авторизовано | Запрос не авторизован. Убедитесь, что ваш ключ подписки или маркер являются допустимыми и находятся в правильных регионах. |
| 413 | Размер запрашиваемой сущности слишком большой | Входные данные SSM превышают длину 1024 символа. |
| 415 | Неподдерживаемый тип носителя | Возможно, указано неправильное `Content-Type` значение. `Content-Type`необходимо задать значение `application/ssml+xml`. |
| 429 | Слишком много запросов | Вы превысили квоту или частоту запросов, разрешенных для вашей подписки. |
| 502 | Недопустимый шлюз    | Проблема с сетью или сервером. Может также указывать на недопустимые заголовки. |

Если состояние HTTP — `200 OK`, текст ответа содержит звуковой файл в затребованном формате. Этот файл можно воспроизводить при передаче, сохранении в буфер или сохранении в файл.

## <a name="next-steps"></a>Следующие шаги

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services)
- [Асинхронное синтез для больших аудио-форм](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Начало работы с набором средств "Пользовательский голос"](how-to-custom-voice.md)
