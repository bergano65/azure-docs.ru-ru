---
title: Краткое руководство. Синтезирование речи с записью в звуковой файл в службе "Речь" с помощью Python
titleSuffix: Azure Cognitive Services
description: ПОДЛЕЖИТ УТОЧНЕНИЮ
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: d6f6519ea5df630a914243046e74c315b4bd7db9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505398"
---
## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки Azure для служб речи. [Получить бесплатно](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3.5 или более поздней версии](https://www.python.org/downloads/).
* Пакет SDK службы "Речь" для Python доступен для таких операционных систем:
    * Windows x64 или x86.
    * MAC: macOS X версии 10.12 или более поздней.
    * Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9 на x64.
* Чтобы установить необходимые пакеты в Linux, выполните следующие команды.

  * Для Ubuntu.

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * Для Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* В Windows для вашей платформы необходим [распространяемый компонент Microsoft Visual C++ для Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Эта команда устанавливает пакет Python из [PyPI](https://pypi.org/) для пакета SDK службы "Речь":

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Поддержка и обновления

Обновления пакета SDK службы "Речь" для Python распространяются с помощью PyPI, о чем указывается в [заметках о выпуске](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Если новая версия доступна, вы можете обновить ее с помощью команды `pip install --upgrade azure-cognitiveservices-speech`.
Проверьте, какая версия установлена в данный момент, просмотрев переменную `azure.cognitiveservices.speech.__version__`.

Если у вас возникла проблема или отсутствует функция, ознакомьтесь со статьей [Возможности получения поддержки и справки](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Создание приложения Python, использующего пакет SDK для службы "Речь"

### <a name="run-the-sample"></a>Запуск примера

Вы можете скопировать [пример кода](#sample-code) из этого краткого руководства в исходный файл `quickstart.py` и запустить его в интегрированной среде разработки или в консоли.

```sh
python quickstart.py
```

Кроме того, можно скачать это краткое руководство как записную книжку [Jupyter](https://jupyter.org) из [репозитория с примером пакета SDK для службы "Речь"](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) и запустить его как записную книжку.

### <a name="sample-code"></a>Пример кода

````Python

import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and service region (e.g., "westus").
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Установка и использование пакета SDK для службы "Речь" с помощью Visual Studio Code

1. Скачайте и установите 64-разрядную версию [Python](https://www.python.org/downloads/) (версия 3.5 или более поздняя) на компьютере.
1. Скачайте и установите [Visual Studio Code](https://code.visualstudio.com/Download).
1. Откройте Visual Studio Code и установите расширение Python. В меню выберите **Файл** > **Параметры** > **Расширения**. Выполните поиск по запросу **Python**.

   ![Установка расширения Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Создайте папку для хранения проекта. Например, с помощью проводника.
1. В Visual Studio Code щелкните значок **Файл**. Затем откройте созданную папку.

   ![Открытие папки](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Создайте исходный файл Python `speechsdk.py`, выбрав значок создания файла

   ![Создание файла](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Скопируйте, вставьте и сохраните [код Python](#sample-code) в созданный файл.
1. Вставьте сведения о подписках на службы речи
1. Если интерпретатор Python выбран, он отображается в левой части строки состояния в нижней части окна.
   В противном случае откроется список доступных интерпретаторов Python. Откройте палитру команд (CTRL+SHIFT+P) и введите **Python: Select Interpreter** (Python: выбор интерпретатора). Выберите соответствующий интерпретатор.
1. Вы можете установить пакет SDK службы "Речь" для Python в Visual Studio Code. Сделайте это, если он еще не установлен для выбранного интерпретатора Python.
   Чтобы установить пакет SDK для службы "Речь", откройте окно терминала. Откройте палитру команд еще раз (CTRL+SHIFT+P) и введите **Терминал: создание нового интегрированного терминала**.
   В открывшемся окне терминала введите команду `python -m pip install azure-cognitiveservices-speech` или команду, соответствующую вашей системе.
1. Чтобы запустить пример кода, щелкните правой кнопкой мыши в редакторе. Выберите пункт **Run Python File in Terminal** (Запустить файл Python в окне терминала).
   Текст преобразуется в речь и сохраняется в указанных звуковых данных.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

При возникновении проблемы в процессе работы с данной инструкцией см. более подробное руководство по [Visual Studio Code для Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
