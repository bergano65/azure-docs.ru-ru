---
title: Краткое руководство. Распознавание речи с помощью C++ (macOS) в службах "Речь"
titleSuffix: Azure Cognitive Services
description: Узнайте, как распознавать речь в macOS, используя C++ и пакет SDK службы "Речь"
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: wolfma
ms.openlocfilehash: be4c5d6187e6367ba04ce7debcc0701937e87ae9
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012351"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении C++ для macOS с помощью пакета SDK для службы "Речь"

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

В этой статье вы создадите консольное приложение C++ для macOS версии 10.13 и выше. Вы будете использовать [пакет SDK для службы "Речь"](speech-sdk.md) в Cognitive Services, чтобы преобразовать речь с микрофона вашего компьютера Mac в текст в реальном времени. Приложение создается с использованием [пакета SDK службы "Речь" для macOS](https://aka.ms/csspeech/macosbinary) и компилятора C++ по умолчанию компьютера Mac (например, `g++`).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="install-speech-sdk"></a>Установка пакета SDK для службы "Речь"

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `1.4.0`.

Пакет SDK службы "Речь" для macOS можно скачать в архивированном пакете платформы по адресу https://aka.ms/csspeech/macosbinary.

Скачайте и установите пакет SDK, как описано ниже.

1. Выберите каталог, в который должны быть извлечены файлы пакета SDK для службы "Речь", и задайте переменную среды `SPEECHSDK_ROOT`, чтобы она указывала на этот каталог. Эта переменная упрощает подключение к каталогу в будущих командах. Например, если вы хотите использовать каталог `speechsdk` в домашнем каталоге, используйте следующую команду.

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Если каталог не существует, создайте его.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Скачайте и извлеките архив `.zip` с платформой пакета SDK для службы "Речь".

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Проверьте содержимое каталога верхнего уровня извлеченного пакета.

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Список файлов в каталоге должен содержать уведомление третьих лиц и файлы лицензии, а также каталог `MicrosoftCognitiveServicesSpeech.framework`.

## <a name="add-sample-code"></a>Добавление примеров кода

1. Создайте исходный файл C++ с именем `helloworld.cpp` и вставьте в него следующий код.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. В этом новом файле замените строку `YourSubscriptionKey` на ключ подписки службы "Речь".

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="build-the-app"></a>Сборка приложения

> [!NOTE]
> Обязательно введите приведенные ниже команды в виде _одной командной строки_. Для этого проще всего скопировать команду с помощью кнопки **Копировать** рядом с каждой из команд, а затем вставить ее в строку оболочки.

* Запустите сборку приложения с помощью следующей команды.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++11 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
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

> [!div class="nextstepaction"]
> [Ознакомьтесь с примерами для C++ на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)

