---
title: Использование автоматического определения языка для распознавания речи в тексте
titleSuffix: Azure Cognitive Services
description: Пакет SDK для распознавания речи поддерживает автоматическое определение языка для речи в тексте. При использовании этой функции предоставленный звук сравнивается с предоставленным списком языков и определяется наиболее вероятным соответствием. Возвращаемое значение затем можно использовать для выбора языковой модели, используемой для перевода речи в текст.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-seven
ms.openlocfilehash: bc438c3e606fefc10e9ffbb64c79f7167d9af062
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122055"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Автоматическое определение языка для речи в текст

Автоматическое определение языка используется для определения наиболее вероятного совпадения звука, переданного в речевой пакет SDK, по сравнению со списком предоставленных языков. Значение, возвращаемое автоматическим определением языка, используется для выбора языковой модели для распознавания речи в тексте, что обеспечивает более точную транскрипцию. Чтобы узнать, какие языки доступны, см. раздел [Поддержка языков](language-support.md).

В этой статье вы узнаете, как использовать `AutoDetectSourceLanguageConfig` для создания объекта `SpeechRecognizer` и получения обнаруженного языка.

> [!IMPORTANT]
> Эта функция доступна только для речевого пакета SDK для C# C++ и Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Автоматическое определение языка с помощью пакета SDK для распознавания речи

Автоматическое определение языка в настоящее время имеет ограничение на два языка на стороне служб для каждого обнаружения. Учитывайте это ограничение при создании объекта `AudoDetectSourceLanguageConfig`. В приведенных ниже примерах вы создадите `AutoDetectSourceLanguageConfig`, а затем используйте его для создания `SpeechRecognizer`.

> [!TIP]
> Можно также указать пользовательскую модель, которая будет использоваться при выполнении преобразования речи в текст. Дополнительные сведения см. [в разделе Использование настраиваемой модели для автоматического определения языка](#use-a-custom-model-for-automatic-language-detection).

В следующих фрагментах кода показано, как использовать автоматическое определение языка в приложениях.

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "de-DE" });
using (var recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Использование настраиваемой модели для автоматического определения языка

Помимо определения языка с помощью моделей речевых служб, можно указать пользовательскую модель для расширенного распознавания. Если пользовательская модель не указана, служба будет использовать языковую модель по умолчанию.

В приведенных ниже фрагментах кода показано, как указать пользовательскую модель в вызове службы распознавания речи. Если обнаруженный язык `en-US`, используется модель по умолчанию. Если обнаруженный язык `fr-FR`, используется конечная точка для настраиваемой модели:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

## <a name="next-steps"></a>Дальнейшие действия

- [Справочная документация по пакету SDK для распознавания речи](speech-sdk.md)
