---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 0f5570f1c80fdecb4db6c8baf22ad05f1e4a32a6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266661"
---
## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на услуги speech. Если у вас нет учетной записи и подписки, [попробуйте услугу Speech бесплатно.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Прежде чем вы можете сделать что-нибудь, вам нужно установить речь SDK. В зависимости от вашей платформы, следуйте инструкциям в разделе <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Получить речь <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> статьи речи SDK.

## <a name="import-dependencies"></a>Импорт зависимостей

Чтобы выполнить примеры в этой `using` статье, включите следующие инструкции в верхней части *файла Program.cs.*

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Чувствительные данные и переменные среды

Пример исходного кода в этой статье зависит от переменных среды для хранения конфиденциальных данных, таких как ключ подписки ресурса Speech и регион. Класс `Program` содержит `static readonly string` два значения, которые присваиваются переменным `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`среды хоста, а именно: и . Оба этих поля находятся в области класса, что делает их доступными в телах метода класса. Для получения дополнительной информации [environment variables and application configuration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)о переменных среды см.

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>Создание конфигурации речевого перевода

Чтобы вызвать службу speech с помощью Speech [`SpeechTranslationConfig`][config]SDK, необходимо создать . Этот класс включает информацию о вашей подписке, например, ваш ключ и связанный с ним регион, конечную точку, узла или токен авторизации.

> [!TIP]
> Независимо от того, выполняете ли вы распознавание речи, синтез речи, перевод или распознавание намерений, вы всегда будете создавать конфигурацию.

Есть несколько способов, которые [`SpeechTranslationConfig`][config]можно инициализировать:

* С подпиской: пройти в ключе и связанном с ним регионе.
* С конечней точкой: пройти в конечную точку службы речи. Токен ключа или авторизации не является обязательным.
* С хостом: пройдите по адресу хозяина. Токен ключа или авторизации не является обязательным.
* С токеном авторизации: пройти в токенавторизации и связанном с ним регионе.

Давайте взглянем на [`SpeechTranslationConfig`][config] то, как создается создается с помощью ключа и региона. Просмотрите страницу [поддержки региона,](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) чтобы найти идентификатор региона.

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Изменение языка исходного кода

Одной из распространенных задач речевого перевода является определение языка ввода (или источника). Давайте взглянем на то, как вы бы изменить язык ввода на итальянский. В коде взаимодействуйте с экземпляром, [`SpeechTranslationConfig`][config] назначая свойство. `SpeechRecognitionLanguage`

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

Свойство [`SpeechRecognitionLanguage`][recognitionlang] ожидает строку формата языкового локализации. Вы можете предоставить любое значение в столбце **Locale** в списке поддерживаемых [языков/языков.](../../../language-support.md)

## <a name="add-translation-language"></a>Добавление языка перевода

Еще одна общая задача речевого перевода заключается в указании целевых языков перевода, по крайней мере один требуется, но кратные поддерживаются. В следующем фрагменте кода, как французский, так и немецкий язык в качестве языка перевода цели.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

С каждым [`AddTargetLanguage`][addlang]вызовом на, новый язык целевого перевода указан. Другими словами, когда речь распознается из исходного языка, каждый целевой перевод доступен как часть полученной операции перевода.

## <a name="initialize-a-translation-recognizer"></a>Инициализация распознавания перевода

После создания [`SpeechTranslationConfig`][config], следующим шагом является инициализация [`TranslationRecognizer`][recognizer]. Когда вы [`TranslationRecognizer`][recognizer]инициализировать, вам нужно `translationConfig`будет передать его ваш . Объект конфигурации предоставляет учетные данные, необходимые службе речи для проверки запроса.

Если вы распознаете речь с помощью микрофона по [`TranslationRecognizer`][recognizer] умолчанию устройства, вот как должно выглядеть:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Если вы хотите указать устройство ввода звука, то [`AudioConfig`][audioconfig] вам нужно `audioConfig` создать и [`TranslationRecognizer`][recognizer]обеспечить параметр при инициализации вашего .

> [!TIP]
> [Узнайте, как получить идентификатор устройства для ввода звука.](../../../how-to-select-audio-input-devices.md)

Во-первых, вы `AudioConfig` будете ссылаться на объект следующим образом:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Если вы хотите предоставить аудио файл вместо использования микрофона, вам `audioConfig`все равно нужно будет предоставить . Однако, когда [`AudioConfig`][audioconfig]вы создаете, вместо вызова, `FromDefaultMicrophoneInput`вы будете звонить `FromWavFileInput` и передавать `filename` параметр.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Преобразование текста

Для перевода речи, речь SDK опирается на микрофон или аудио ввод файла. Распознавание речи происходит перед переводом речи. После того, как все объекты были инициализированы, позвоните функции распознавания и получите результат.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Для получения дополнительной информации о речи к тексту, см [основы распознавания речи](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Синтез переводов

После успешного распознавания речи и перевода результат содержит все переводы в словаре. Ключом [`Translations`][translations] словаря является язык целевого перевода, а значением является переведенный текст. Признанная речь может быть переведена, а затем синтезирована на другом языке (речь к речи).

### <a name="event-based-synthesis"></a>Синтез на основе событий

Объект `TranslationRecognizer` разоблачает `Synthesizing` событие. Событие запускается несколько раз и обеспечивает механизм для извлечения синтезированного звука из результата распознавания перевода. Если вы переводите на несколько языков, [см.](#manual-synthesis) Укажите голос синтеза, [`VoiceName`][voicename] назначив обработчик `Synthesizing` а также предоставив обработчик ановийку событий, получите звук. Следующий пример сохраняет переведенный звук как файл *.wav.*

> [!IMPORTANT]
> Синтез на основе событий работает только с одним переводом, **не** добавляя несколько языков целевого перевода. Кроме того, [`VoiceName`][voicename] например, язык должен быть таким же, как и на языке целевого перевода; `"de"` может накарте `"de-DE-Hedda"`.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Ручной синтез

Словарь [`Translations`][translations] может быть использован для синтеза аудио из текста перевода. Итерировать каждый перевод и синтезировать перевод. При создании `SpeechSynthesizer` экземпляра `SpeechConfig` объект должен [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] иметь свойство, настроенное на нужный голос. Следующий пример переводится на пять языков, и каждый перевод затем синтезируется в аудио файл на соответствующем нейронном языке.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Для получения дополнительной информации о синтезе речи, см [основы синтеза речи](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
