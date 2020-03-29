---
title: Как указать исходный язык для речи в текст
titleSuffix: Azure Cognitive Services
description: Речь SDK позволяет указать исходный язык при преобразовании речи в текст. В этой статье описывается, как использовать методы FromConfig и SourceLanguageConfig, чтобы служба speech знала исходный язык и предоставляла пользовательскую цель модели.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235974"
---
# <a name="specify-source-language-for-speech-to-text"></a>Указать исходный язык для речи в текст

В этой статье вы узнаете, как указать исходный язык для аудиоввода, переданного в Speech SDK для распознавания речи. Кроме того, для определения пользовательской речевой модели для улучшения распознавания предоставляется пример кода.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Как указать исходный язык в C #

В этом примере язык исходного кода прямо `SpeechRecognizer` указан в качестве параметра с использованием конструкции.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

В этом примере язык исходного кода предоставляется с помощью `SourceLanguageConfig`. Затем, `sourceLanguageConfig` передается в качестве `SpeechRecognizer` параметра для построения.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

В этом примере с помощью `SourceLanguageConfig`этого языка исходного кода и пользовательской конечный точки предоставляются. Затем, `sourceLanguageConfig` передается в качестве `SpeechRecognizer` параметра для построения.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`и `EndpointId` набор методов устаревает из `SpeechConfig` класса в C. Использование этих методов не рекомендуется, и не должны использоваться при построении `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Как указать исходный язык в СЗ

В этом примере язык исходного кода прямо `FromConfig` указан в качестве параметра с использованием метода.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

В этом примере язык исходного кода предоставляется с помощью `SourceLanguageConfig`. Затем, `sourceLanguageConfig` передается в качестве `FromConfig` параметра `recognizer`при создании .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

В этом примере с помощью `SourceLanguageConfig`этого языка исходного кода и пользовательской конечный точки предоставляются. Передавается `sourceLanguageConfig` в качестве `FromConfig` параметра `recognizer`при создании .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`и `SetEndpointId` являются устаревшими `SpeechConfig` методами из класса в СЗ и Java. Использование этих методов не рекомендуется, и не должны использоваться при построении `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Как указать исходный язык в Java

В этом примере язык исходного кода предоставляется явно при создании нового `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

В этом примере язык исходного кода предоставляется с помощью `SourceLanguageConfig`. Затем, `sourceLanguageConfig` передается в качестве параметра `SpeechRecognizer`при создании нового .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

В этом примере с помощью `SourceLanguageConfig`этого языка исходного кода и пользовательской конечный точки предоставляются. Затем, `sourceLanguageConfig` передается в качестве параметра `SpeechRecognizer`при создании нового .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`и `setEndpointId` являются устаревшими `SpeechConfig` методами из класса в СЗ и Java. Использование этих методов не рекомендуется, и не должны использоваться при построении `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Как указать исходный язык в Python

В этом примере язык исходного кода прямо `SpeechRecognizer` указан в качестве параметра с использованием конструкции.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

В этом примере язык исходного кода предоставляется с помощью `SourceLanguageConfig`. Затем, `SourceLanguageConfig` передается в качестве `SpeechRecognizer` параметра для построения.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

В этом примере с помощью `SourceLanguageConfig`этого языка исходного кода и пользовательской конечный точки предоставляются. Затем, `SourceLanguageConfig` передается в качестве `SpeechRecognizer` параметра для построения.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`и `endpoint_id` свойства унижаются `SpeechConfig` из класса в Python. Использование этих свойств не рекомендуется, и не должны использоваться при построении `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Как указать исходный язык в Javascript

Первым шагом является `SpeechConfig`создание:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Далее, укажите исходный `speechRecognitionLanguage`язык вашего аудио с :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Если вы используете пользовательскую модель для распознавания, `endpointId`можно указать конечную точку с помощью:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Как указать исходный язык в Objective-C

Первым шагом является `speechConfig`создание:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Далее, укажите исходный `speechRecognitionLanguage`язык вашего аудио с :

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Если вы используете пользовательскую модель для распознавания, `endpointId`можно указать конечную точку с помощью:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>См. также

* Список поддерживаемых языков и языков для речи в тексте можно узнать в [поддержку Language.](language-support.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Справочная документация SDK речи](speech-sdk.md)
