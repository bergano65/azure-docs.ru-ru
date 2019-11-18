---
title: Указание языка исходного текста для перевода речи в текст
titleSuffix: Azure Cognitive Services
description: Пакет SDK для распознавания речи позволяет указать исходный язык при преобразовании речи в текст. В этой статье описывается, как использовать методы Фромконфиг и Саурцелангуажеконфиг, чтобы служба распознавания речи знала исходный язык и предоставил настраиваемый целевой объект модели.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 94b8fb026b61b52e8096cf54e1db30a6c260c04b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109951"
---
# <a name="specify-source-language-for-speech-to-text"></a>Укажите исходный язык для перевода речи в текст

В этой статье вы узнаете, как указать исходный язык для входных данных, передаваемых в речевой пакет SDK для распознавания речи. Кроме того, приведен пример кода для указания пользовательской модели речи для улучшения распознавания.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Как указать исходный язык вC#

Первым шагом является создание `SpeechConfig`:

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Затем укажите исходный язык звука с помощью `SpeechRecognitionLanguage`:

```csharp
speechConfig.SpeechRecognitionLanguage = "de-DE";
```

Если вы используете пользовательскую модель для распознавания, можно указать конечную точку с `EndpointId`:

```csharp
speechConfig.EndpointId = "The Endpoint ID for your custom model.";
```

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Как указать исходный язык вC++

В этом примере исходный язык явно указывается в качестве параметра с помощью метода `FromConfig`.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

В этом примере исходный язык предоставляется с помощью `SourceLanguageConfig`. Затем `sourceLanguageConfig` передается в качестве параметра для `FromConfig` при создании `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

В этом примере исходный язык и Пользовательская конечная точка предоставляются с помощью `SourceLanguageConfig`. `sourceLanguageConfig` передается в качестве параметра для `FromConfig` при создании `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` и `SetEndpointId` являются устаревшими методами из класса `SpeechConfig` в C++ и Java. Использовать эти методы не рекомендуется, и их не следует использовать при создании `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Как указать исходный язык в Java

В этом примере исходный язык предоставляется явно при создании нового `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

В этом примере исходный язык предоставляется с помощью `SourceLanguageConfig`. Затем `sourceLanguageConfig` передается в качестве параметра при создании нового `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

В этом примере исходный язык и Пользовательская конечная точка предоставляются с помощью `SourceLanguageConfig`. Затем `sourceLanguageConfig` передается в качестве параметра при создании нового `SpeechRecognizer`.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` и `setEndpointId` являются устаревшими методами из класса `SpeechConfig` в C++ и Java. Использовать эти методы не рекомендуется, и их не следует использовать при создании `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Как указать исходный язык в Python

Первым шагом является создание `speech_config`:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

Затем укажите исходный язык звука с помощью `speech_recognition_language`:

```Python
speech_config.speech_recognition_language="de-DE"
```

Если вы используете пользовательскую модель для распознавания, можно указать конечную точку с `endpoint_id`:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Как указать исходный язык в JavaScript

Первым шагом является создание `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Затем укажите исходный язык звука с помощью `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Если вы используете пользовательскую модель для распознавания, можно указать конечную точку с `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Как указать исходный язык в цели-C

Первым шагом является создание `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Затем укажите исходный язык звука с помощью `speechRecognitionLanguage`:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Если вы используете пользовательскую модель для распознавания, можно указать конечную точку с `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>См. также

* Список поддерживаемых языков и языковых стандартов для распознавания речи в тексте см. в разделе [Поддержка языков](language-support.md).

## <a name="next-steps"></a>Дополнительная информация

* [Справочная документация по пакету SDK для распознавания речи](speech-sdk.md)
