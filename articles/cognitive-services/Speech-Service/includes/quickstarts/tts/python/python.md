---
title: Краткое руководство. Синтез речи с помощью Python в службе "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как синтезировать речь в Linux, используя Python и пакет SDK службы "Речь".
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 239bb1e43b6a89ed1e8a18d544cf646b5f2de9b9
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671204"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md?pivots=programming-language-python).

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

Кроме того, можно скачать это краткое руководство как записную книжку [Jupyter](https://jupyter.org) из [репозитория с примером пакета SDK для службы "Речь"](https://aka.ms/csspeech/samples) и запустить его как записную книжку.

### <a name="sample-code"></a>Образец кода

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

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
   В противном случае откроется список доступных интерпретаторов Python. Откройте палитру команд (CTRL+SHIFT+P) и введите **Python: Select Interpreter** (Python: выбор интерпретатора). Выберите соответствующий интерпретатор.
1. Вы можете установить пакет SDK службы "Речь" для Python в Visual Studio Code. Сделайте это, если он еще не установлен для выбранного интерпретатора Python.
   Чтобы установить пакет SDK для службы "Речь", откройте окно терминала. Откройте палитру команд еще раз (CTRL+SHIFT+P) и введите **Терминал: создание нового интегрированного терминала**.
   В открывшемся окне терминала введите команду `python -m pip install azure-cognitiveservices-speech` или команду, соответствующую вашей системе.
1. Чтобы запустить пример кода, щелкните правой кнопкой мыши в редакторе. Выберите пункт **Run Python File in Terminal** (Запустить файл Python в окне терминала).
   При появлении запроса введите текст. После этого будет воспроизведен синтезированный звук.

   ![Запуск примера](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

При возникновении проблемы в процессе работы с данной инструкцией см. более подробное руководство по [Visual Studio Code для Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также раздел

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
