---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: trbye
ms.openlocfilehash: 9365b4bbf6e6fb37156b6e9f24b923591945ea52
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376676"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../overview.md#try-the-speech-service-for-free)
> * [Создать приложение LUIS и получить ключ конечной точки](../../../../quickstarts/create-luis.md).
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md?pivots=programming-language-python).

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>Поддержка и обновления

Обновления пакета SDK службы "Речь" для Python распространяются с помощью PyPI, о чем указывается в [заметках о выпуске](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Если новая версия доступна, вы можете обновить ее с помощью команды `pip install --upgrade azure-cognitiveservices-speech`.
Проверьте, какая версия установлена в данный момент, просмотрев переменную `azure.cognitiveservices.speech.__version__`.

Если у вас возникла проблема или отсутствует функция, ознакомьтесь со статьей [Возможности получения поддержки и справки](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Создание приложения Python, использующего пакет SDK для службы "Речь"

### <a name="run-the-sample"></a>Запуск примера

Вы можете скопировать [пример кода](#sample-code) из этого краткого руководства в исходный файл `quickstart.py` и запустить его в интегрированной среде разработки или в консоли.

```Bash
python quickstart.py
```

Кроме того, можно скачать это краткое руководство как записную книжку [Jupyter](https://jupyter.org) из [репозитория с примером пакета SDK для службы "Речь"](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) и запустить его как записную книжку.

### <a name="sample-code"></a>Образец кода

> [!NOTE]
> Пакет SDK для распознавания речи по умолчанию распознает использование языкового стандарта en-us. Дополнительные сведения о выборе исходного языка см. в разделе [Specify source language for speech to text](../../../../how-to-specify-source-language.md) (Указание исходного языка для преобразования речи в текст).

```python
import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.audio.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")

# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Установка и использование пакета SDK для службы "Речь" с помощью Visual Studio Code

1. Скачайте и установите 64-разрядную версию [Python](https://www.python.org/downloads/) (версия 3.5 или 3.8) на компьютере.
1. Скачайте и установите [Visual Studio Code](https://code.visualstudio.com/Download).
1. Откройте Visual Studio Code и установите расширение Python. В меню выберите **Файл** > **Параметры** > **Расширения**. Выполните поиск по запросу **Python**.

   ![Установка расширения Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Создайте папку для хранения проекта. Например, с помощью проводника.
1. В Visual Studio Code щелкните значок **Файл**. Затем откройте созданную папку.

   ![Открытие папки](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Создайте исходный файл Python `speechsdk.py`, выбрав значок создания файла

   ![Создание файла](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Скопируйте, вставьте и сохраните [код Python](#sample-code) в созданный файл.
1. Вставьте сведения о подписке службы "Речь".
1. Если интерпретатор Python выбран, он отображается в левой части строки состояния в нижней части окна.
   В противном случае откроется список доступных интерпретаторов Python. Откройте палитру команд <kbd>CTRL+SHIFT+P</kbd> и введите **Python: Select Interpreter** (Python: выбор интерпретатора). Выберите соответствующий интерпретатор.
1. Вы можете установить пакет SDK службы "Речь" для Python в Visual Studio Code. Сделайте это, если он еще не установлен для выбранного интерпретатора Python.
   Чтобы установить пакет SDK для службы "Речь", откройте окно терминала. Откройте палитру команд еще раз <kbd>CTRL+SHIFT+P</kbd> и введите **Терминал: создание нового интегрированного терминала**.
   В открывшемся окне терминала введите команду `python -m pip install azure-cognitiveservices-speech` или команду, соответствующую вашей системе.
1. Чтобы запустить пример кода, щелкните правой кнопкой мыши в редакторе. Выберите пункт **Run Python File in Terminal** (Запустить файл Python в окне терминала).
   Первые 15 секунд речевых данных из звукового файла будут распознаны и записаны в окне консоли.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

При возникновении проблемы в процессе работы с данной инструкцией см. более подробное руководство по [Visual Studio Code для Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]