---
title: Конечная точка контейнера «Текст к речи»
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275003"
---
Контейнер предоставляет [APIs на основе REST.](../rest-text-to-speech.md) Существует множество [проектов исходного кода образца](https://github.com/Azure-Samples/Cognitive-Speech-TTS) для платформы, фреймворка и языковых вариаций.

С контейнером *«Стандартный текст к речи»* следует полагаться на местность и голос скачатого тега изображений. Например, если вы `latest` загрузили тег, `en-US` локаль по умолчанию есть и `JessaRUS` голос. Аргумент `{VOICE_NAME}` будет [`en-US-JessaRUS`](../language-support.md#standard-voices). Смотрите пример SSML ниже:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Тем не менее, для *пользовательских Текст к речи* вам нужно получить **голос / модель** из [пользовательского голосового портала](https://aka.ms/custom-voice-portal). Пользовательское название модели является синонимом названия голоса. Перейдите на страницу **Обучения** и скопируйте `{VOICE_NAME}` **голосовую / модель** для использования в качестве аргумента.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Пользовательская голосовая модель - имя голоса":::

Смотрите пример SSML ниже:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Давайте построим запрос HTTP POST, предоставив несколько заголовков и полезную нагрузку данных. Замените `{VOICE_NAME}` заполнитель на собственную стоимость.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Эта команда:

* Строит запрос HTTP POST `speech/synthesize/cognitiveservices/v1` для конечных точек.
* Определяет `Accept` заголовок`audio/*`
* Углянут `Content-Type` заголовок, для получения дополнительной информации, `application/ssml+xml`см. [request body](../rest-text-to-speech.md#request-body)
* Определяет `X-Microsoft-OutputFormat` заголовок `riff-16khz-16bit-mono-pcm`, для более вариантов см аудио [выход.](../rest-text-to-speech.md#audio-outputs)
* Отправляет запрос на [язык разметки синтеза речи (SSML)](../speech-synthesis-markup.md) с учетом конечных `{VOICE_NAME}` точек.