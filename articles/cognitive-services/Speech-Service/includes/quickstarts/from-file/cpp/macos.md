---
title: Краткое руководство. Распознавание речи из звукового файла в службе "Речь" с помощью C++ (macOS)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: wolfma
ms.openlocfilehash: 4a043c246cc859706e062cdc11d30cbd657bc6b2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037811"
---
## <a name="prerequisites"></a>предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?tabs=macos)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Создайте исходный файл C++ с именем `helloworld.cpp` и вставьте в него следующий код.

   ```cpp
    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and service region (e.g., "westus").
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech recognizer using a WAV file. The default language is "en-us".
    // Replace with your own audio file name.
    auto audioInput = AudioConfig::FromWavFileInput("whatstheweatherlike.wav");
    auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
    cout << "Recognizing first result...\n";

    // Starts speech recognition, and returns after a single utterance is recognized. The end of a
    // single utterance is determined by listening for silence at the end or until a maximum of 15
    // seconds of audio is processed.  The task returns the recognition text as result.
    // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
    // shot recognition like command or query.
    // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
    auto result = recognizer->RecognizeOnceAsync().get();

    // Checks result.
    switch (result->Reason)
    {
        case ResultReason::RecognizedSpeech:
            cout << "RECOGNIZED: Text=" << result->Text << std::endl;
            break;
        case ResultReason::NoMatch:
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
            break;
        case ResultReason::Canceled:
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

            if (cancellation->Reason == CancellationReason::Error)
            {
                cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
            break;
    }
   ```

1. В этом новом файле замените строку `YourSubscriptionKey` на ваш ключ подписки службы "Речь".

1. Замените строку `YourServiceRegion` значением [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

1. Замените строку `whatstheweatherlike.wav` собственным именем файла.

> [!NOTE]
> Пакет SDK для распознавания речи по умолчанию распознает использование языкового стандарта en-us. Дополнительные сведения о выборе исходного языка см. в разделе [Specify source language for speech to text](../../../../how-to-specify-source-language.md) (Указание исходного языка для преобразования речи в текст).

## <a name="build-the-app"></a>Сборка приложения

> [!NOTE]
> Обязательно введите приведенные ниже команды в виде _одной командной строки_. Для этого проще всего скопировать команду с помощью кнопки **Копировать** рядом с каждой из команд, а затем вставить ее в строку оболочки.

* Запустите сборку приложения с помощью следующей команды.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Запустите приложение

1. Настройте путь к библиотеке загрузчика так, чтобы он указывал на библиотеку пакета SDK для службы "Речь".

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Запустите приложение.

   ```sh
   ./helloworld
   ```

1. Ваш звуковой файл передается в службу "Речь", и первый речевой фрагмент в файле преобразовывается в текст, который появляется в том же окне.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]
