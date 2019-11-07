---
title: Краткое руководство. Распознавание речи с микрофона в службе "Речь" с помощью C++ (macOS)
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в macOS, используя C++ и пакет SDK службы "Речь"
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 9b19cd1ff8340a22afb6713289bc3d7be42857ea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505646"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=macos).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=macos).

## <a name="add-sample-code"></a>Добавление примеров кода

1. Создайте исходный файл C++ с именем `helloworld.cpp` и вставьте в него следующий код.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. В этом новом файле замените строку `YourSubscriptionKey` на ключ подписки службы "Речь".

1. Замените строку `YourServiceRegion` значением [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="build-the-app"></a>Сборка приложения

> [!NOTE]
> Обязательно введите приведенные ниже команды в виде _одной командной строки_. Для этого проще всего скопировать команду с помощью кнопки **Копировать** рядом с каждой из команд, а затем вставить ее в строку оболочки.

* Запустите сборку приложения с помощью следующей команды.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Запуск приложения

1. Настройте путь к библиотеке загрузчика так, чтобы он указывал на библиотеку пакета SDK для службы "Речь".

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Запустите приложение.

   ```sh
   ./helloworld
   ```

1. В окне консоли появится подсказка, запрашивающая сказать что-то. Произнесите фразу или предложение на английском языке. Ваша речь передается в службу "Речь" и преобразовывается в текст, который появляется в том же окне.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]