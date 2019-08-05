---
title: Краткое руководство. Распознавание речи с помощью Python — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве объясняется, как создать консольное приложение для преобразования речи в текст, использующее пакет SDK службы "Речь" для Python. После завершения вы сможете преобразовывать речь в текст в режиме реального времени с использованием микрофона компьютера.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 6cbe583ca59cb98ba233e58dc665c7e18c2a1f7f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559299"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Краткое руководство. Распознавание речи с помощью пакета SDK службы "Речь" для Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

В этой статье показано, как использовать службу "Речь" с помощью пакета SDK службы "Речь" для Python, а также как распознавать речь с использованием микрофона.

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки Azure для службы "Речь". [Его можно получить бесплатно](get-started.md).
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

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Эта команда устанавливает пакет Python из [PyPI](https://pypi.org/) для пакета SDK службы "Речь":

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Поддержка и обновления

Обновления пакета SDK службы "Речь" для Python распространяются с помощью PyPI, о чем объявляется в [заметках о выпуске](./releasenotes.md).
Если новая версия доступна, вы можете обновить ее с помощью команды `pip install --upgrade azure-cognitiveservices-speech`.
Проверьте, какая версия установлена в данный момент, просмотрев переменную `azure.cognitiveservices.speech.__version__`.

Если у вас возникла проблема или отсутствует функция, ознакомьтесь со статьей [Возможности получения поддержки и справки](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Создание приложения Python, использующего пакет SDK для службы "Речь"

### <a name="run-the-sample"></a>Запуск примера

Вы можете скопировать [пример кода](#sample-code) из этого краткого руководства в исходный файл `quickstart.py` и запустить его в интегрированной среде разработки или в консоли.

```sh
python quickstart.py
```

Кроме того, можно скачать это краткое руководство как записную книжку [Jupyter](https://jupyter.org) из [репозитория с примером пакета SDK для службы "Речь"](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) и запустить его как записную книжку.

### <a name="sample-code"></a>Пример кода

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Установка и использование пакета SDK для службы "Речь" с помощью Visual Studio Code

1. Скачайте и установите 64-разрядную версию [Python](https://www.python.org/downloads/) (версия 3.5 или более поздняя) на компьютере.
1. Скачайте и установите [Visual Studio Code](https://code.visualstudio.com/Download).
1. Откройте Visual Studio Code и установите расширение Python. В меню выберите **Файл** > **Параметры** > **Расширения**. Выполните поиск по запросу **Python**.

   ![Установка расширения Python](media/sdk/qs-python-vscode-python-extension.png)

1. Создайте папку для хранения проекта. Например, с помощью проводника.
1. В Visual Studio Code щелкните значок **Файл**. Затем откройте созданную папку.

   ![Открытие папки](media/sdk/qs-python-vscode-python-open-folder.png)

1. Создайте исходный файл Python `speechsdk.py`, щелкнув значок создания файла.

   ![Создание файла](media/sdk/qs-python-vscode-python-newfile.png)

1. Скопируйте, вставьте и сохраните [код Python](#sample-code) в созданный файл.
1. Вставьте сведения о подписке службы "Речь".
1. Если интерпретатор Python выбран, он отображается в левой части строки состояния в нижней части окна.
   В противном случае откроется список доступных интерпретаторов Python. Откройте палитру команд (CTRL+SHIFT+P) и введите **Python: Select Interpreter** (Python: выбор интерпретатора). Выберите соответствующий интерпретатор.
1. Вы можете установить пакет SDK службы "Речь" для Python в Visual Studio Code. Сделайте это, если он еще не установлен для выбранного интерпретатора Python.
   Чтобы установить пакет SDK для службы "Речь", откройте окно терминала. Откройте палитру команд еще раз (CTRL+SHIFT+P) и введите **Терминал: создание нового интегрированного терминала**.
   В открывшемся окне терминала введите команду `python -m pip install azure-cognitiveservices-speech` или команду, соответствующую вашей системе.
1. Чтобы запустить пример кода, щелкните правой кнопкой мыши в редакторе. Выберите пункт **Run Python File in Terminal** (Запустить файл Python в окне терминала).
   Когда появится запрос, произнесите несколько слов. Вскоре после этого отобразится расшифрованный текст.

   ![Запуск примера](media/sdk/qs-python-vscode-python-run.png)

При возникновении проблемы в процессе работы с данной инструкцией см. более подробное руководство по [Visual Studio Code для Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Ознакомиться с примерами на Python на сайте GitHub](https://aka.ms/csspeech/samples)
