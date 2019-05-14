---
title: Краткое руководство. Синтезирование речи с помощью C++ (Linux) в службах "Речь"
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как синтезировать речь в Linux, используя C++ и пакет SDK служб "Речь"
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: yinhew
ms.openlocfilehash: b5db21d40f1e270fb6771ba47af28a9e90228ea4
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465592"
---
# <a name="quickstart-synthesize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>Краткое руководство. Синтезирование речи в приложении C++ для Linux с помощью пакета SDK для служб "Речь"

Также доступны краткие руководства по [распознаванию речи](quickstart-cpp-linux.md).

В этой статье вы создадите консольное приложение C++ для Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Вы можете использовать [пакет SDK для служб "Речь"](speech-sdk.md) в Cognitive Services для синтезирования речи из текста в реальном времени и ее воспроизведения через динамики компьютера. Приложение создано с помощью [пакета SDK для службы "Речь" для Linux](https://aka.ms/csspeech/linuxbinary) и компилятора C++ дистрибутива Linux (например, `g++`).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам потребуется ключ подписки службы "Речь". Его можно получить бесплатно. Дополнительные сведения см. в статье [Бесплатная пробная подписка на службу "Речь"](get-started.md).

## <a name="install-speech-sdk"></a>Установка пакета SDK для службы "Речь"

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `1.5.0`.

Пакет SDK для службы "Речь" может использоваться для создания как 64-разрядных, так и 32-разрядных приложений. Необходимые библиотеки и файлы заголовков можно скачать в виде TAR-файла по ссылке: https://aka.ms/csspeech/linuxbinary.

Скачайте и установите пакет SDK, как описано ниже.

1. Убедитесь, что зависимости пакета SDK установлены.

   * Для Ubuntu.

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Для Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

1. Выберите каталог, в который должны быть извлечены файлы пакета SDK для службы "Речь", и задайте переменную среды `SPEECHSDK_ROOT`, чтобы она указывала на этот каталог. Эта переменная упрощает подключение к каталогу в будущих командах. Например, если вы хотите использовать каталог `speechsdk` в домашнем каталоге, используйте следующую команду.

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Если каталог не существует, создайте его.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Скачайте и извлеките архив `.tar.gz` с двоичными файлами пакета SDK для службы "Речь".

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Проверьте содержимое каталога верхнего уровня извлеченного пакета.

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   В списке каталогов должны находиться уведомление сторонних разработчиков и файлы лицензий, а также каталог `include` с файлами заголовков (`.h`) и каталог `lib` с библиотеками.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>Добавление примеров кода

1. Создайте исходный файл C++ с именем `helloworld.cpp` и вставьте в него следующий код.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-linux/helloworld.cpp#code)]

1. В этом новом файле замените строку `YourSubscriptionKey` на ключ подписки службы "Речь".

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

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

## <a name="run-the-app"></a>Запуск приложения

1. Настройте путь к библиотеке загрузчика так, чтобы он указывал на библиотеку пакета SDK для службы "Речь".

   * В системе **x64** (64-разрядная) введите следующую команду.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * В системе **x86** (32-разрядная) введите следующую команду.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Запустите приложение.

   ```sh
   ./helloworld
   ```

1. В окне консоли отобразится запрос на ввод текста. Введите несколько слов или предложение. Введенный текст передается в службы "Речь" и синтезируется в речь, которая воспроизводится через динамик.

   ```text
   Type some text that you want to speak...
   > hello
   Speech synthesized to speaker for text [hello]
   Press enter to exit...
   ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Ознакомьтесь с примерами на C++ на сайте GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>См. также

- [Настройка голосов](how-to-customize-voice-font.md)
- [Запись образцов голосов](record-custom-voice-samples.md)
