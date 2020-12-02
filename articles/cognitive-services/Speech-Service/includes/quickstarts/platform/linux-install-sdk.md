---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 785008633318ee6670170d66cba96bd5853f9ed9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188471"
---
## <a name="install-speech-sdk"></a>Установка пакета SDK для службы "Речь"

Пакет SDK для службы "Речь" может использоваться для создания как 64-разрядных, так и 32-разрядных приложений. Необходимые библиотеки и файлы заголовков можно скачать в виде TAR-файла по ссылке: https://aka.ms/csspeech/linuxbinary.

Скачайте и установите пакет SDK, как описано ниже.

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

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]