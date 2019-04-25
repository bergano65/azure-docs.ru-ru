---
title: Преобразования текста в речь Справочник по API (REST) — службы распознавания речи
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать REST API преобразования текста в речь. Из этой статьи вы узнаете о вариантах авторизации, параметрах запроса, структуре запроса и получении ответа.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 71710cd940aad3a56dae6c19d4d52a5b141b3d80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480549"
---
# <a name="text-to-speech-rest-api"></a>REST API преобразования текста в речь.

Службы речи позволяют [преобразования текста в синтезированную речь](#convert-text-to-speech) и [получить список поддерживаемых голоса](#get-a-list-of-voices) для области с помощью набора интерфейсов API REST. Каждый доступную конечную точку, связанный с областью. Ключ подписки для конечной точки или региона, которые вы планируете использовать является обязательным.

REST API преобразования текста в речь поддерживает нейронные модели и стандартные голосовые модели для преобразования текста в речь. Каждая из таких моделей поддерживает определенный язык и диалект, определяемые языковым стандартом.

* Полный список голосовых моделей см. в разделе о [поддерживаемых языках](language-support.md#text-to-speech).
* Сведения о доступности по регионам см. на [этой странице](regions.md#text-to-speech).

> [!IMPORTANT]
> Стандартные, пользовательские и нейронные голосовые модели предлагаются по разным ценам. Дополнительные сведения см. на странице [цен](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Прежде чем использовать этот API, понять:

* Для вызова REST API преобразования текста в речь требуется заголовок авторизации. Это означает, что для доступа к службе необходимо выполнить обмен маркерами. Дополнительные сведения см. в разделе [Authenticate to the Speech API](#authentication) (Аутентификация в API речи).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Получение списка голоса

`voices/list` Конечная точка позволяет получить полный список голоса для определенного региона или конечную точку.

### <a name="regions-and-endpoints"></a>Регионы и конечные точки

| Регион | Конечная точка |
|--------|----------|
| Восточная часть Австралии | https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Южная часть Бразилии | https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Центральная Канада | https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Центральный регион США | https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Восточная Азия | https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Восточная часть США | https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Восток США 2 | https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Центральная Франция | https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Центральная Индия | https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Восточная часть Японии | https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Центральная Корея | https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Центрально-северная часть США | https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Северная Европа | https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Центрально-южная часть США | https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Юго-Восточная Азия | https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Южная часть Великобритании | https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Западная Европа | https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Запад США | https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Западный регион США 2 | https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list |

### <a name="request-headers"></a>Заголовки запросов

В этой таблице перечислены обязательные и необязательные заголовки для запросов преобразования текста в речь.

| Верхний колонтитул | ОПИСАНИЕ | Обязательный или необязательный |
|--------|-------------|---------------------|
| `Authorization` | Маркеру авторизации предшествует слово `Bearer`. Дополнительные сведения см. в разделе [Authenticate to the Speech API](#authentication) (Аутентификация в API речи). | Обязательно для заполнения |

### <a name="request-body"></a>Текст запроса

Текст не является обязательным для `GET` запросы к этой конечной точке.

### <a name="sample-request"></a>Пример запроса

Для этого запроса требуется только заголовок авторизации.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Пример ответа

Этот ответ был усечен для иллюстрации структуры ответа.

> [!NOTE]
> Доступность голоса зависит от региона и конечной точки.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>Коды состояния HTTP

Код состояния HTTP для каждого ответа указывает на успешное выполнение или возникновение распространенных ошибок.

| HTTP status code (Код состояния HTTP) | ОПИСАНИЕ | Возможная причина |
|------------------|-------------|-----------------|
| 200 | OК | Запрос выполнен успешно. |
| 400 | Ошибка запроса | Обязательный параметр отсутствует, пустой или его значение равно нулю. Или переданное либо обязательному, либо необязательному параметру значение является недопустимым. Распространенной проблемой является слишком длинный заголовок. |
| 401 | Не авторизовано | Запрос не авторизован. Убедитесь, что ваш ключ подписки или маркер являются допустимыми и находятся в правильных регионах. |
| 429 | Слишком много запросов | Вы превысили квоту или частоту запросов, разрешенных для вашей подписки. |
| 502 | Недопустимый шлюз | Проблема с сетью или сервером. Может также указывать на недопустимые заголовки. |


## <a name="convert-text-to-speech"></a>Преобразование текста в речь

`v1` Конечная точка позволяет преобразовать преобразования текста в речь с помощью [языка разметки синтеза речи (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Регионы и конечные точки

Для преобразования текста в речь с помощью REST API поддерживаются следующие регионы. Выберите конечную точку, которая соответствует региону подписки.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Заголовки запросов

В этой таблице перечислены обязательные и необязательные заголовки для запросов преобразования текста в речь.

| Верхний колонтитул | ОПИСАНИЕ | Обязательный или необязательный |
|--------|-------------|---------------------|
| `Authorization` | Маркеру авторизации предшествует слово `Bearer`. Дополнительные сведения см. в разделе [Authenticate to the Speech API](#authentication) (Аутентификация в API речи). | Обязательно для заполнения |
| `Content-Type` | Указывает тип контента для предоставленного текста. Допустимое значение: `application/ssml+xml`. | Обязательно для заполнения |
| `X-Microsoft-OutputFormat` | Указывает формат аудиосигнала. Полный список допустимых значений см. в разделе [Аудиосигналы](#audio-outputs). | Обязательно для заполнения |
| `User-Agent` | Имя приложения. Предоставленное значение должно быть меньше 255 символов. | Обязательно для заполнения |

### <a name="audio-outputs"></a>Аудиосигналы

Это список поддерживаемых аудиоформатов, которые отправляются в каждом запросе в виде заголовка `X-Microsoft-OutputFormat`. Каждый включает в себя скорость и тип кодирования. Службы речи поддерживает 24 кГц, 16 кГц, и выводит аудио 8 кГц.

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

### <a name="request-body"></a>Текст запроса

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

См. в разделе нашими шаблонами запуска, примеры конкретного языка:

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Коды состояния HTTP

Код состояния HTTP для каждого ответа указывает на успешное выполнение или возникновение распространенных ошибок.

| HTTP status code (Код состояния HTTP) | ОПИСАНИЕ | Возможная причина |
|------------------|-------------|-----------------|
| 200 | OК | Запрос выполнен успешно; текст ответа представляет собой звуковой файл. |
| 400 | Ошибка запроса | Обязательный параметр отсутствует, пустой или его значение равно нулю. Или переданное либо обязательному, либо необязательному параметру значение является недопустимым. Распространенной проблемой является слишком длинный заголовок. |
| 401 | Не авторизовано | Запрос не авторизован. Убедитесь, что ваш ключ подписки или маркер являются допустимыми и находятся в правильных регионах. |
| 413 | Размер запрашиваемой сущности слишком большой | Входные данные SSM превышают длину 1024 символа. |
| 429 | Слишком много запросов | Вы превысили квоту или частоту запросов, разрешенных для вашей подписки. |
| 502 | Недопустимый шлюз | Проблема с сетью или сервером. Может также указывать на недопустимые заголовки. |

Если состояние HTTP — `200 OK`, текст ответа содержит звуковой файл в затребованном формате. Этот файл можно воспроизводить при передаче, сохранении в буфер или сохранении в файл.

## <a name="next-steps"></a>Дальнейшие действия

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)
