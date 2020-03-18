---
title: Краткое руководство. Синтезирование речи с помощью C++ (Linux) — служба "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как синтезировать речь в Linux, используя C++ и пакет SDK служб "Речь"
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 40befc98c2280418a443ff80e4d246b6e2957e56
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925612"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Настройте среду разработки и создайте пустой проект](../../../../quickstarts/setup-platform.md?tabs=linux).

## <a name="add-sample-code"></a>Добавление примеров кода

1. Создайте исходный файл C++ с именем `helloworld.cpp` и вставьте в него следующий код.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/text-to-speech/helloworld.cpp#code)]

1. В этом новом файле замените строку `YourSubscriptionKey` на ваш ключ подписки службы "Речь".

1. Замените строку `YourServiceRegion` значением [региона](~/articles/cognitive-services/Speech-Service/regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="build-the-app"></a>Сборка приложения

> [!NOTE]
> Обязательно введите приведенные ниже команды в виде _одной командной строки_. Для этого проще всего скопировать команду с помощью кнопки **Копировать** рядом с каждой из команд, а затем вставить ее в строку оболочки.

* В системе **x64** (64-разрядная) выполните сборку приложения с помощью следующей команды.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* В системе **x86** (32-разрядная) выполните сборку приложения с помощью следующей команды.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* В системе **ARM64** (64-разрядная) выполните сборку приложения с помощью следующей команды.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>Запустите приложение

1. Настройте путь к библиотеке загрузчика так, чтобы он указывал на библиотеку пакета SDK для службы "Речь".

   * В системе **x64** (64-разрядная) введите следующую команду.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * В системе **x86** (32-разрядная) введите следующую команду.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * В системе **ARM64** (64-разрядная) введите следующую команду.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Запустите приложение.

   ```sh
   ./helloworld
   ```

1. В окне консоли отобразится запрос на ввод текста. Введите несколько слов или предложение. Введенный текст передается в службу "Речь" и синтезируется в речь, которая воспроизводится через динамик.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>См. также раздел

- [Создание пользовательского голоса](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Запись пользовательских образцов голоса](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
