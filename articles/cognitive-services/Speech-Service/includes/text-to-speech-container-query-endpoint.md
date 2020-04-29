---
title: Запрос к конечной точке контейнера текста для речи
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81275003"
---
Контейнер предоставляет [интерфейсы API конечной точки на основе интерфейса RESTful](../rest-text-to-speech.md). Существует множество [примеров проектов исходного кода](https://github.com/Azure-Samples/Cognitive-Speech-TTS) для платформ, платформ и языковых вариантов.

Используя стандартный контейнер преобразования *текста в речь* , вы должны полагаться на языковой стандарт и голоса скачанного тега образа. Например, если вы загрузили `latest` тег, язык по умолчанию `en-US` — `JessaRUS` и Voice. Затем `{VOICE_NAME}` аргумент будет иметь [`en-US-JessaRUS`](../language-support.md#standard-voices)значение. См. пример SSML ниже:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Однако для *пользовательского преобразования текста в речь* необходимо получить **голос или модель** с [настраиваемого голосового портала](https://aka.ms/custom-voice-portal). Имя пользовательской модели является синонимом имени голоса. Перейдите на страницу **обучения** и скопируйте **голосовое или модель** для использования в качестве `{VOICE_NAME}` аргумента.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Настраиваемая Voice-модель — имя голоса":::

См. пример SSML ниже:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Давайте создадим запрос HTTP POST, предоставляя несколько заголовков и полезных данных. Замените `{VOICE_NAME}` заполнитель своим собственным значением.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Эта команда:

* Конструирует запрос HTTP POST для `speech/synthesize/cognitiveservices/v1` конечной точки.
* Задает `Accept` заголовок`audio/*`
* Задает заголовок `Content-Type` `application/ssml+xml`для более подробной информации см. в разделе [текст запроса](../rest-text-to-speech.md#request-body).
* Задает `X-Microsoft-OutputFormat` заголовок `riff-16khz-16bit-mono-pcm`для дополнительных параметров см. [звуковые выходные данные](../rest-text-to-speech.md#audio-outputs).
* Отправляет запрос на [языке разметки речи (SSML)](../speech-synthesis-markup.md) , `{VOICE_NAME}` заданный в конечной точке.