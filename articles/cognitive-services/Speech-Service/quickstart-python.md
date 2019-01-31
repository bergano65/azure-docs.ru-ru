---
title: Краткое руководство. Распознавание речи с помощью Python в службе "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве объясняется, как создать консольное приложение для преобразования речи в текст с помощью пакета SDK службы "Речь" для Python. После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: chlandsi
ms.openlocfilehash: 1c7287b919c46ead4f961aff769da5c8bf68cc6f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226576"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

В этой статье показано, как использовать службу "Речь" с помощью пакет SDK службы "Речь" для Python, а также как распознавать речь с использованием микрофона.

## <a name="prerequisites"></a>Предварительные требования

Предварительные требования для начала работы:

* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).
* [Python 3.5 (64-разрядная версия)](https://www.python.org/downloads/) или более поздняя версия.
* Пакет SDK службы "Речь" для Python доступен для Windows (x64), Mac (macOS X версии 10.12 или более поздней) и Linux (Ubuntu 16.04 или 18.04 для x64).
* Для установки необходимых пакетов выполните следующие команды в Ubuntu 16.04:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* В Windows также необходим [Распространяемый компонент Visual C++ для Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) для вашей платформы.

## <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Пакет SDK службы "Речь" в Cognitive Services для Python можно установить из [PyPI](https://pypi.org/) с помощью следующей команды в командной строке:

```sh
pip install azure-cognitiveservices-speech
```

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `1.2.0`.

## <a name="support-and-updates"></a>Поддержка и обновления

Обновления пакета SDK службы "Речь" для Python будет распространяться с помощью PyPI, о чем будет объявлено на странице [Заметки о выпуске](./releasenotes.md).
Если новая версия доступна, вы можете обновить ее с помощью команды `pip install --upgrade azure-cognitiveservices-speech`.
Вы можете проверить, какая версия установлена в данный момент, проверив переменную `azure.cognitiveservices.speech.__version__`.

Если у вас есть проблема или отсутствует функция, перейдите на нашу страницу [службы поддержки](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>Создание приложения Python с помощью пакета SDK для службы "Речь"

### <a name="run-the-sample"></a>Запуск примера

Вы можете скопировать [код](#quickstart-code) из этого краткого руководства в исходный файл `quickstart.py` и запустить его в интегрированной среде разработки или в консоли.

```sh
python quickstart.py
```

Кроме того, можно скачать это краткое руководство как записную книжку [Jupyter](https://jupyter.org) из [репозитория примеров службы "Речь" в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) и запустить как записную книжку.

### <a name="sample-code"></a>Пример кода

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Установка и использование пакета SDK для службы "Речь" с помощью Visual Studio Code

1. [Скачайте](https://www.python.org/downloads/) и установите 64-разрядную версию Python (версия 3.5 или более поздняя) на компьютере.
1. [Скачайте](https://code.visualstudio.com/Download) и установите Visual Studio Code.
1. Откройте Visual Studio Code и установите расширение Python. Для этого выберите **Файл** > **Параметры** > **Расширения** в меню и найдите Python.
   ![Установка расширения Python](media/sdk/qs-python-vscode-python-extension.png)
1. Создайте папку для хранения проекта, например, с помощью проводника Windows.
1. В Visual Studio Code щелкните значок **Файл**, а затем откройте созданную папку.
   ![Открытие папки](media/sdk/qs-python-vscode-python-open-folder.png)
1. Создайте исходный файл Python `speechsdk.py`, щелкнув значок "Создать файл".
   ![Создание файла](media/sdk/qs-python-vscode-python-newfile.png)
1. Скопируйте, вставьте и сохраните [код Python](#quickstart-code) в созданный файл.
1. Вставьте сведения о подписке службы "Речь".
1. Если интерпретатор Python уже выбран, он будет отображаться в левой части строки состояния в нижней части окна.
   В противном случае вы можете открыть список доступных интерпретаторов Python. Откройте **палитру команд** (`Ctrl+Shift+P`), введите **Python: выбор интерпретатора** и выберите соответствующий интерпретатор.
1. Если пакет SDK службы "Речь" для Python еще не установлен для выбранного интерпретатора Python, его можно установить в Visual Studio Code.
   Чтобы установить пакет SDK для службы "Речь", откройте окно терминала в палитре команд (`Ctrl+Shift+P`) и введите **Терминал: создание нового интегрированного терминала**.
   В открывшемся окне терминала введите команду `python -m pip install azure-cognitiveservices-speech` или команду, соответствующую вашей системе.
1. Чтобы запустить пример кода, щелкните правой кнопкой мыши в редакторе и выберите пункт **Run Python File in Terminal** (Запустить файл Python в окне терминала).
   Произнесите несколько слов после запроса, после чего появится транскрибированный текст.
   ![Запуск примера](media/sdk/qs-python-vscode-python-run.png)

При возникновении проблемы в процессе работы с данной инструкцией см. более подробное руководство по [Visual Studio Code для Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Ознакомиться с примерами на Python на сайте GitHub](https://aka.ms/csspeech/samples)
