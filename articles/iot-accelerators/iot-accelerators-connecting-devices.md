---
title: Подготовка устройств Windows к удаленному мониторингу с помощью C в Azure | Документация Майкрософт
description: В статье описывается, как подключить устройство к предварительно настроенному акселератору решения для удаленного мониторинга с помощью приложения на C в среде Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: 729ba19153eeb9767961d099e7a37c10a38b1286
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634719"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Подключение устройства к акселератору решения для удаленного мониторинга в Windows

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

В этом руководстве показано, как подключить реальное устройство к акселератору решений для удаленного мониторинга.

Как и для большинства внедряемых приложений, работающих на устройствах с ограниченными ресурсами, клиентский код для приложения на устройстве пишется на языке C. В этом руководстве вы создадите приложение на компьютере под управлением Windows.

Если вы предпочитаете имитацию устройства, см. раздел [Создание и тестирование нового имитированного устройства](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы завершить действия, описанные в этом практическом руководстве, выполните описанные в [настройках среды разработки Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment), чтобы добавить необходимые средства разработки и библиотеки на компьютер Windows.

## <a name="view-the-code"></a>Просмотрите код

[Пример кода](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client), используемый в этом руководстве доступный в репозитории GitHub пакета SDK для устройств Azure IoT для C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Загрузка исходного кода и подготовка проекта

Чтобы подготовить проект, [клонируйте репозиторий пакетов SDK для устройств Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) из GitHub.

Этот пример находится в папке **samples/solutions/remote_monitoring_client**.

В текстовом редакторе откройте файл **remote_monitoring.c** в папке **samples/solutions/remote_monitoring_client**.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

1. Измените файл **remote_monitoring.c**, чтобы заменить `<connectionstring>` строкой подключения устройства, которую вы записали в начале этого практического руководства, при добавлении устройства в акселератор решений.

1. Выполните действия, описанные в [сборке пакета SDK для C в Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows), чтобы выполнить сборку пакета SDK и клиентского приложения удаленного мониторинга.

1. В командной строке, используемой для построения решения, выполните следующую команду.

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    В консоли появляются сообщения в следующих случаях:

    - приложение отправляет пример данных телеметрии в акселератор решения;
    - приложение отвечает на методы, вызываемые из панели мониторинга решения.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
