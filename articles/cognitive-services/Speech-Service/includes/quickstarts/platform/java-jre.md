---
title: Краткое руководство. Настройка платформы (Windows, Linux, macOS) для использования пакета SDK службы "Речь" для Java — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для использования Java (Windows, Linux, macOS) с пакетом SDK службы "Речь".
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 142d4504ab12e7df5cc1e009038554a5b90dff0c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135500"
---
а также как установить [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для 64-разрядной версии среды JRE Java 8. Если вам нужно только имя пакета, чтобы приступить к работе самостоятельно, учитывайте, что пакет SDK для Java недоступен в центральном репозитории Maven. Независимо от того, что вы используете: Gradle или файл зависимостей `pom.xml`, нужно добавить пользовательский репозиторий с указанием на `https://csspeechstorage.blob.core.windows.net/maven/` (имя пакета см. ниже).

> [!NOTE]
> Ознакомьтесь с пакетом SDK службы "Речь" и устройством Roobo в разделе [Сведения о пакете SDK для речевых устройств](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

- Пакет SDK службы "Речь" для Java доступен для таких операционных систем:
  - Windows: только 64-разрядная версия.
  - MAC: macOS X версии 10.13 или более поздней.
  - Linux: см. список [поддерживаемых дистрибутивов Linux и целевых архитектур](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Предварительные требования

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) или [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Интегрированная среда разработки Eclipse Java](https://www.eclipse.org/downloads/) (требуется предварительная установка Java).
- Для поддерживаемых платформ Linux потребуется установить определенные библиотеки (`libssl` для поддержки протокола SSL и `libasound2` для поддержки звука). Ниже приведены команды, необходимые для установки правильных версий этих библиотек. Используйте команды для своего дистрибутива.

  - Чтобы установить необходимые пакеты в Ubuntu или Debian, выполните следующие команды:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Если пакет libssl1.0.0 недоступен, установите libssl1.0.x (где x больше 0) или libssl1.1.

  - Чтобы установить необходимые пакеты в RHEL/CentOS, выполните следующие команды:

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - Если вы используете RHEL/CentOS 7, выполните инструкции по [настройке пакета SDK службы "Речь" в RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Если вы используете RHEL/CentOS 8, выполните инструкции по [настройке OpenSSL для Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- В Windows для вашей платформы необходим [распространяемый компонент Microsoft Visual C++ для Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads). Обратите внимание, что при первой установке может потребоваться перезагрузить Windows, чтобы продолжить работу с данным руководством.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Создание проекта Eclipse и установка пакета SDK для службы "Речь"

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [windows](../quickstart-list.md)]
