---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 7c57952d0b78271bbcc45bd282385524772f0f9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266650"
---
## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас есть учетная запись Azure и подписка на услуги speech. Если у вас нет учетной записи и подписки, [попробуйте услугу Speech бесплатно.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Прежде чем вы можете сделать что-нибудь, вам нужно установить речь SDK. В зависимости от вашей платформы, следуйте инструкциям в разделе <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Получить речь <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> статьи речи SDK.

## <a name="import-dependencies"></a>Импорт зависимостей

Чтобы выполнить примеры в этой `#include` `using` статье, включите следующие и операторы в верхней части файла кода СЗ.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Чувствительные данные и переменные среды

Пример исходного кода в этой статье зависит от переменных среды для хранения конфиденциальных данных, таких как ключ подписки ресурса Speech и регион. Кодовый файл C's содержит два значения строки, которые присваиваются переменным среды хоста, а именно: `SPEECH__SUBSCRIPTION__KEY` и `SPEECH__SERVICE__REGION`. Оба этих поля находятся в области класса, что делает их доступными в телах метода класса. Для получения дополнительной информации [environment variables and application configuration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)о переменных среды см.

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Изменение языка исходного кода

Одной из распространенных задач речевого перевода является определение языка ввода (или источника). Давайте взглянем на то, как вы бы изменить язык ввода на итальянский. В коде взаимодействуйте с [`SpeechTranslationConfig`][config] `SetSpeechRecognitionLanguage` экземпляром, вызывая метод.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

Свойство [`SpeechRecognitionLanguage`][recognitionlang] ожидает строку формата языкового локализации. Вы можете предоставить любое значение в столбце **Locale** в списке поддерживаемых [языков/языков.](../../../language-support.md)

## <a name="add-translation-language"></a>Добавление языка перевода

Еще одна общая задача речевого перевода заключается в указании целевых языков перевода, по крайней мере один требуется, но кратные поддерживаются. В следующем фрагменте кода, как французский, так и немецкий язык в качестве языка перевода цели.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

С каждым [`AddTargetLanguage`][addlang]вызовом на, новый язык целевого перевода указан. Другими словами, когда речь распознается из исходного языка, каждый целевой перевод доступен как часть полученной операции перевода.

## <a name="initialize-a-translation-recognizer"></a>Инициализация распознавания перевода

После создания [`SpeechTranslationConfig`][config], следующим шагом является инициализация [`TranslationRecognizer`][recognizer]. Когда вы [`TranslationRecognizer`][recognizer]инициализировать, вам нужно `translationConfig`будет передать его ваш . Объект конфигурации предоставляет учетные данные, необходимые службе речи для проверки запроса.

Если вы распознаете речь с помощью микрофона по [`TranslationRecognizer`][recognizer] умолчанию устройства, вот как должно выглядеть:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Если вы хотите указать устройство ввода звука, то [`AudioConfig`][audioconfig] вам нужно `audioConfig` создать и [`TranslationRecognizer`][recognizer]обеспечить параметр при инициализации вашего .

> [!TIP]
> [Узнайте, как получить идентификатор устройства для ввода звука.](../../../how-to-select-audio-input-devices.md)

Во-первых, вы `AudioConfig` будете ссылаться на объект следующим образом:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Если вы хотите предоставить аудио файл вместо использования микрофона, вам `audioConfig`все равно нужно будет предоставить . Однако, когда [`AudioConfig`][audioconfig]вы создаете, вместо вызова, `FromDefaultMicrophoneInput`вы будете звонить `FromWavFileInput` и передавать `filename` параметр.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Преобразование текста

Для перевода речи, речь SDK опирается на микрофон или аудио ввод файла. Распознавание речи происходит перед переводом речи. После того, как все объекты были инициализированы, позвоните функции распознавания и получите результат.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Для получения дополнительной информации о речи к тексту, см [основы распознавания речи](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Синтез переводов

После успешного распознавания речи и перевода результат содержит все переводы в словаре. Ключом [`Translations`][translations] словаря является язык целевого перевода, а значением является переведенный текст. Признанная речь может быть переведена, а затем синтезирована на другом языке (речь к речи).

### <a name="event-based-synthesis"></a>Синтез на основе событий

Объект `TranslationRecognizer` разоблачает `Synthesizing` событие. Событие запускается несколько раз и обеспечивает механизм для извлечения синтезированного звука из результата распознавания перевода. Если вы переводите на несколько языков, [см.](#manual-synthesis) Укажите голос синтеза, [`SetVoiceName`][voicename] назначив обработчик `Synthesizing` а также предоставив обработчик ановийку событий, получите звук. Следующий пример сохраняет переведенный звук как файл *.wav.*

> [!IMPORTANT]
> Синтез на основе событий работает только с одним переводом, **не** добавляя несколько языков целевого перевода. Кроме того, [`SetVoiceName`][voicename] например, язык должен быть таким же, как и на языке целевого перевода; `"de"` может накарте `"de-DE-Hedda"`.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Ручной синтез

Словарь [`Translations`][translations] может быть использован для синтеза аудио из текста перевода. Итерировать каждый перевод и синтезировать перевод. При создании `SpeechSynthesizer` экземпляра `SpeechConfig` объект должен [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] иметь свойство, настроенное на нужный голос. Следующий пример переводится на пять языков, и каждый перевод затем синтезируется в аудио файл на соответствующем нейронном языке.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Для получения дополнительной информации о синтезе речи, см [основы синтеза речи](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
