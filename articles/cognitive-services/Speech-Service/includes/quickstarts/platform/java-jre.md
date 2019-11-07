---
title: Краткое руководство. Настройка платформы (Windows, Linux, macOS) для использования пакета SDK службы "Речь" для Java — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для использования Java (Windows, Linux, macOS) с пакетом SDK для службы "Речь",
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 2814327bdc434dbdae5644bd40b09d0506b21df9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504342"
---
а также как установить [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для 64-разрядной версии среды JRE Java 8.

> [!NOTE]
> Ознакомьтесь с пакетом SDK службы "Речь" и устройством Roobo в разделе [Сведения о пакете SDK для речевых устройств](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

- Пакет SDK службы "Речь" для Java доступен для таких операционных систем:
  - Windows: только 64-разрядная версия.
  - MAC: macOS X версии 10.13 или более поздней.
  - Linux: только 64-разрядная версия для Ubuntu 16.04, Ubuntu 18.04 или Debian 9.

## <a name="prerequisites"></a>Предварительные требования

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) или [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Интегрированная среда разработки Eclipse Java](https://www.eclipse.org/downloads/) (требуется предварительная установка Java).
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

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Создание проекта Eclipse и установка пакета SDK для службы "Речь"

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [windows](../quickstart-list.md)]
