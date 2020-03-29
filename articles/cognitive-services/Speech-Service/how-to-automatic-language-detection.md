---
title: Как использовать автоматическое обнаружение языка для передачи речи в текст
titleSuffix: Azure Cognitive Services
description: Speech SDK поддерживает автоматическое обнаружение языка для передачи речи в текст. При использовании этой функции при условии, что предоставленный звук сравнивается с предоставленным списком языков, и наиболее вероятное соответствие определяется. Возвратное значение может быть использовано для выбора языковой модели, используемой для отречи в тексте.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5592fc3e50db892c6abb09fc2516b8e1c03f0f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239607"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Автоматическое обнаружение языка для передачи текста

Автоматическое обнаружение языка используется для определения наиболее вероятного совпадения аудио, переданного в Speech SDK по сравнению со списком предоставленных языков. Значение, возвращенное автоматическим обнаружением языка, затем используется для выбора языковой модели для передачи текста в текст, предоставляя вам более точную транскрипцию. Чтобы узнать, какие [Language support](language-support.md)языки доступны, см.

В этой статье вы узнаете, `AutoDetectSourceLanguageConfig` как `SpeechRecognizer` использовать для построения объекта и получения обнаруженного языка.

> [!IMPORTANT]
> Эта функция доступна только для речи SDK для C, C, Java и Python.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Автоматическое обнаружение языка с помощью речи SDK

Автоматическое обнаружение языка в настоящее время имеет ограничение на службы в два языка на обнаружение. Имейте это ограничение в `AudoDetectSourceLanguageConfig` виду при строительстве объекта. В приведенных ниже образцах `AutoDetectSourceLanguageConfig`вы создадите, а `SpeechRecognizer`затем используйте его для построения.

> [!TIP]
> Можно также указать пользовательскую модель для использования при выполнении речи в текст. Для получения дополнительной информации [см.](#use-a-custom-model-for-automatic-language-detection)

Следующие фрагменты иллюстрируют, как использовать автоматическое обнаружение языка в приложениях:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Используйте пользовательскую модель для автоматического обнаружения языка

В дополнение к обнаружению языка с помощью моделей службы speech можно указать пользовательскую модель для расширенного распознавания. Если пользовательская модель не предоставлена, служба будет использовать модель языка по умолчанию.

Фрагменты ниже иллюстрируют, как указать пользовательскую модель в вызове к службе речи. Если обнаруженный язык `en-US`используется, то используется модель по умолчанию. Если обнаруженный `fr-FR`язык, то используется конечная точка для пользовательской модели:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

- [Справочная документация SDK речи](speech-sdk.md)
