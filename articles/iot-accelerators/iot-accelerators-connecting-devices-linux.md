---
title: Подготовка устройств Linux к удаленному мониторингу с помощью C в Azure | Документация Майкрософт
description: В статье описывается, как подключить устройство к предварительно настроенному акселератору решения для удаленного мониторинга с помощью приложения на C в среде Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628329"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Подключение устройства к акселератору решения для удаленного мониторинга в Linux

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

В этом руководстве показано, как подключить реальное устройство к акселератору решений для удаленного мониторинга.

Как и для большинства внедряемых приложений, работающих на устройствах с ограниченными ресурсами, клиентский код для приложения на устройстве пишется на языке C. В этом руководстве вы создадите приложение на компьютере под управлением Ubuntu (Linux).

Если вы предпочитаете имитацию устройства, см. раздел [Создание и тестирование нового имитированного устройства](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этих действий, описанных в этом руководстве, необходимо устройство под управлением Ubuntu версии 15.04 или более поздней версии. Прежде чем продолжить, настройте [среду разработки Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

## <a name="view-the-code"></a>Просмотрите код

[Пример кода](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client), используемый в этом руководстве доступный в репозитории GitHub пакета SDK для устройств Azure IoT для C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Загрузка исходного кода и подготовка проекта

Чтобы подготовить проект, клонируйте или загрузите [репозиторий пакетов SDK для устройств Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c) из GitHub.

Этот пример находится в папке **samples/solutions/remote_monitoring_client**.

В текстовом редакторе откройте файл **remote_monitoring.c** в папке **samples/solutions/remote_monitoring_client**.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Создание и запуск приложения

Ниже приведены указания по использованию *CMake* для создания клиентского приложения. Клиентское приложение удаленного мониторинга создается частью процесса сборки для SDK.

1. Измените файл **remote_monitoring.c**, чтобы заменить `<connectionstring>` строкой подключения устройства, которую вы записали в начале этого практического руководства, при добавлении устройства в акселератор решений.

1. Перейдите в корневой каталог хранилища клонированных копий [Репозиторий пакетов SDK для Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) и выполните следующие команды для создания клиентского приложения:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Запустите клиентское приложение и отправьте данные телеметрии в Центр Интернета вещей.

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    В консоли появляются сообщения в следующих случаях:

    - приложение отправляет пример данных телеметрии в акселератор решения;
    - приложение отвечает на методы, вызываемые из панели мониторинга решения.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
