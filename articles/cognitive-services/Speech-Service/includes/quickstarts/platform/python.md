---
title: Краткое руководство. Настройка платформы для использования пакета SDK службы "Речь" для Python — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для использования Python с пакетом SDK для службы "Речь",
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: d4a81c6dd2b44efd432345627e78bb69fff1688d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504334"
---
а также как установить [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для Python.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

- Пакет SDK службы "Речь" для Python доступен для таких операционных систем:
  - Windows: x64 и x86.
  - MAC: macOS X версии 10.12 или более поздней.
  - Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9 на x64.

## <a name="prerequisites"></a>Предварительные требования

- Для поддерживаемых платформ Linux потребуется установить определенные библиотеки (`libssl` для поддержки протокола SSL и `libasound2` для поддержки звука). Ниже приведены команды, необходимые для установки правильных версий этих библиотек. Используйте команды для своего дистрибутива.

  - Чтобы установить необходимые пакеты в Ubuntu, выполните следующие команды:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - Чтобы установить необходимые пакеты в Debian 9, выполните следующие команды:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- В Windows для вашей платформы необходим [распространяемый компонент Microsoft Visual C++ для Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads). Обратите внимание, что при первой установке может потребоваться перезагрузить Windows, чтобы продолжить работу с данным руководством.
- И наконец, потребуется [Python 3.5 или более поздней версии](https://www.python.org/downloads/). Чтобы проверить установку, откройте командную строку, введите команду `python --version` и проверьте результат. Если установка выполнена правильно, вы получите ответ "Python 3.5.1" или аналогичный.

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Установка пакета SDK для службы "Речь" с помощью Visual Studio Code

1. Скачайте и установите последнюю поддерживаемую версию [Python](https://www.python.org/downloads/) для своей платформы (3.5 или более поздней версии).
   - Пользователи Windows должны выбрать вариант добавления Python в переменную среды PATH при установке.
1. Скачайте и установите [Visual Studio Code](https://code.visualstudio.com/Download).
1. Откройте Visual Studio Code и установите расширение Python. В меню выберите **Файл** > **Параметры** > **Расширения**. Найдите **Python** и щелкните **Установить**.

   ![Установка расширения Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Кроме того, в Visual Studio Code можно установить пакет SDK службы "Речь" для Python из интегрированной командной строки:
   1. Откройте терминал (в раскрывающихся меню выберите **Терминал** > **Новый терминал**).
   1. В открывшемся окне терминала введите команду `python -m pip install azure-cognitiveservices-speech`.

Вот и все. Вы можете приступать к написанию кода для пакета SDK для службы "Речь" на Python и перейти к [Дальнейшим действиям](#next-steps) ниже. Если вы не знакомы с Visual Studio Code, ознакомьтесь с более детальной [документацией по Visual Studio Code](https://code.visualstudio.com/docs). Дополнительные сведения о Visual Studio Code и Python см. в [учебнике по Visual Studio Code для Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="install-the-speech-sdk-using-the-command-line"></a>Установка пакета SDK для службы "Речь" с помощью командной строки

Если вы не используете Visual Studio Code, можно воспользоваться следующей командой. Она устанавливает пакет Python из [PyPI](https://pypi.org/) для пакета SDK для службы "Речь". Пользователям Visual Studio Code следует перейти к следующему подразделу.

```sh
pip install azure-cognitiveservices-speech
```

Если вы используете macOS, вам может потребоваться выполнить следующую команду, чтобы команда `pip` сработала:

```sh
python3 -m pip install --upgrade pip
```

Успешно установив `azure-cognitiveservices-speech` с помощью `pip`, можно использовать пакет SDK для службы "Речь", импортировав пространство имен в проекты Python. Например:

```py
import azure.cognitiveservices.speech as speechsdk
```

Это показано более подробно в примерах кода, которые можно открыть по ссылкам, указанным в разделе [Дополнительная информация](#next-steps) ниже.

## <a name="support-and-updates"></a>Поддержка и обновления

Обновления пакета SDK службы "Речь" для Python распространяются с помощью PyPI, о чем указывается в [заметках о выпуске](~/articles/cognitive-services/speech-service/releasenotes.md).
Если новая версия доступна, вы можете обновить ее с помощью команды `pip install --upgrade azure-cognitiveservices-speech`.
Проверьте, какая версия установлена в данный момент, просмотрев переменную `azure.cognitiveservices.speech.__version__`.

Если у вас возникла проблема или отсутствует функция, ознакомьтесь со статьей [Возможности получения поддержки и справки](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [windows](../quickstart-list.md)]