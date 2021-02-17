---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: cf765145cafa2eb06d77ea2e153e45c296281b71
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552599"
---
а также как установить [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Требования к системе

Linux: см. список [поддерживаемых дистрибутивов Linux и целевых архитектур](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* В Windows для вашей платформы необходим [распространяемый компонент Microsoft Visual C++ для Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0). При первой установке может потребоваться перезагрузка.
* GCC;
* [Двоичный выпуск Go (1.13 или более поздней версии)](https://golang.org/dl/)

* Для поддерживаемых платформ Linux потребуется установить определенные библиотеки (`libssl` для поддержки протокола SSL и `libasound2` для поддержки звука). Ниже приведены команды, необходимые для установки правильных версий этих библиотек. Используйте команды для своего дистрибутива.

   * В Ubuntu или Debian сделайте следующее:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Если пакет libssl1.0.0 недоступен, установите libssl1.0.x (где x больше 0) или libssl1.1.

   * Для RHEL/CentOS.

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - Если вы используете RHEL/CentOS 7, выполните инструкции по [настройке пакета SDK службы "Речь" в RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Если вы используете RHEL/CentOS 8, выполните инструкции по [настройке OpenSSL для Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Настройка среды Go

Чтобы настроить среду Go для поиска пакета SDK службы "Речь", выполните следующие действия. В обоих шагах замените `<architecture>` архитектурой вашего ЦП: `x86`, `x64`, `arm32` или `arm64`.

1. Так как привязки зависят от `cgo`, вам нужно задать переменные среды, чтобы помочь Go найти пакет SDK.

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Для запуска приложений, включая пакет SDK, нужно сообщить операционной системе, где найти библиотеки:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [windows](../quickstart-list-go.md)]
