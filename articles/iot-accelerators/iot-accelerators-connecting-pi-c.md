---
title: Подготовка устройства Raspberry Pi к удаленному мониторингу с помощью C в Azure | Документация Майкрософт
description: В статье описывается, как подключить устройство Raspberry Pi к акселератору решения для удаленного мониторинга с помощью приложения на С.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: 838a33fd390b28fec609c42487dca225ddc6eaa8
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628210"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Подключение устройства Raspberry Pi к акселератору решения для удаленного мониторинга с помощью С

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

В этом руководстве показано, как подключить реальное устройство к акселератору решений для удаленного мониторинга. Как и для большинства внедряемых приложений, работающих на устройствах с ограниченными ресурсами, код клиента для приложения на устройстве Raspberry Pi пишется на языке C. В этом руководстве вы создадите приложение на устройстве Raspberry Pi под управлением ОС Raspbian.

Если вы предпочитаете имитацию устройства, см. раздел [Создание и тестирование нового имитированного устройства](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Необходимое оборудование

Настольный компьютер, чтобы иметь удаленный доступ к командной строке Raspberry Pi.

[Начальный набор Microsoft IoT для Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) или эквивалентные компоненты. В этом руководстве используются следующие компоненты из набора:

- Raspberry Pi 3;
- карта MicroSD (с NOOBS);
- кабель MiniUSB;
- кабель Ethernet;

### <a name="required-desktop-software"></a>Необходимое ПО для настольного компьютера

На настольном компьютере необходимо установить клиент SSH, чтобы иметь удаленный доступ к командной строке Raspberry Pi.

- Windows не предоставляет клиент SSH. Мы советуем использовать [PuTTY](https://www.putty.org/).
- Большинство дистрибутивов Linux и Mac OS содержат служебную программу командной строки SSH. Дополнительные сведения см. в статье [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (Подключение по протоколу SSH с помощью Linux или Mac OS).

### <a name="required-raspberry-pi-software"></a>Необходимое ПО для Raspberry Pi

В этой статье предполагается, что вы установили последнюю версию [Raspbian ОС на устройстве Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

В следующих шагах объясняется, как подготовить устройство Raspberry Pi для создания приложения C, которое подключается к акселератору решения.

1. Подключитесь к Raspberry Pi с помощью **ssh**. Дополнительные сведения см. в разделе о [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) на [веб-сайте Raspberry Pi](https://www.raspberrypi.org/).

1. Чтобы обновить устройство Raspberry Pi, используйте следующую команду:

    ```sh
    sudo apt-get update
    ```

1. Чтобы завершить действия, описанные в этом практическом руководстве, выполните описанные в [настройках среды разработки Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux), чтобы добавить необходимые средства разработки и библиотеки на устройство Raspberry Pi.

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
