---
title: Запрос к конечной точке контейнера текста для речи
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491097"
---
Контейнер предоставляет [интерфейсы API конечной точки на основе интерфейса RESTful](../rest-text-to-speech.md). Существует множество [примеров проектов исходного кода](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) для платформ, платформ и языковых вариантов.

Используя стандартный контейнер преобразования *текста в речь* , вы должны полагаться на языковой стандарт и голоса скачанного тега образа. Например, если вы скачали тег `latest`, язык по умолчанию `en-US` и `JessaRUS` голоса. Затем [`en-US-JessaRUS`](../language-support.md#standard-voices)аргумент `{VOICE_NAME}`. См. пример SSML ниже:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Однако для *пользовательского преобразования текста в речь* необходимо получить **голос или модель** с [настраиваемого голосового портала](https://aka.ms/custom-voice-portal). Имя пользовательской модели является синонимом имени голоса. Перейдите на страницу **обучения** и скопируйте **голосовое или модель** для использования в качестве аргумента `{VOICE_NAME}`.
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

Давайте создадим запрос HTTP POST, предоставляя несколько заголовков и полезных данных. Замените заполнитель `{VOICE_NAME}` собственным значением.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Эта команда:

* Конструирует запрос HTTP POST для конечной точки `speech/synthesize/cognitiveservices/v1`.
* Указывает заголовок `Accept` `audio/*`
* Задает `Content-Type` заголовок `application/ssml+xml`. Дополнительные сведения см. в разделе [текст запроса](../rest-text-to-speech.md#request-body).
* Задает `X-Microsoft-OutputFormat` заголовок `riff-16khz-16bit-mono-pcm`. Дополнительные параметры см. в разделе [выходные данные звука](../rest-text-to-speech.md#audio-outputs).
* Отправляет запрос на [языке разметки речи (SSML)](../speech-synthesis-markup.md) , заданный `{VOICE_NAME}` конечной точке.