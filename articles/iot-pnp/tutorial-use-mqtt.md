---
title: Использование MQTT для создания клиента устройства IoT Plug and Play (предварительная версия) | Документация Майкрософт
description: Используя протокол MQTT напрямую, вы можете создать клиент устройства IoT Plug and Play (предварительная версия), не применяя пакеты SDK для устройств Azure IoT.
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535210"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>Использование MQTT для разработки клиента устройства IoT Plug and Play (предварительная версия)

При возможности в процессе создания клиентов устройств IoT Plug and Play вы должны использовать один из пакетов SDK для устройств Azure IoT. Но в некоторых сценариях, например при ограниченном объеме памяти на устройстве, возможно, потребуется использовать библиотеку MQTT для обмена данными с центром Интернета вещей.

В примере в этом руководстве используется библиотека MQTT [Eclipse Mosquitto](http://mosquitto.org/) и Visual Studio. В инструкциях этого руководства предполагается, что на компьютере разработки вы используете Windows.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций из этого учебника в ОС Windows установите в локальной среде Windows такое программное обеспечение:

* [Visual Studio (Community, Professional или Enterprise).](https://visualstudio.microsoft.com/downloads/) При [установке](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) Visual Studio обязательно добавьте рабочую нагрузку **Разработка классических приложений на C++** .
* [Git](https://git-scm.com/download/);
* [CMake](https://cmake.org/download/).
* [Обозреватель Интернета вещей Azure](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить подписанный URL-адрес для подключения устройства к центру. Запишите эту строку. Вы будете использовать ее позже при работе с этим руководством:

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Используйте строку подключения к центру Интернета вещей, чтобы настроить**обозреватель Интернета вещей Azure**:

1. Запустите **обозреватель Интернета вещей Azure**.
1. На странице **Параметры** вставьте строку подключения к центру Интернета вещей в параметры **Конфигурации приложений**.
1. Выберите **Сохранить и подключиться**.
1. Добавленное ранее устройство будет доступно в списке устройств на главной странице.

## <a name="clone-sample-repo"></a>Клонирование примера репозитория

Используйте следующую команду, чтобы клонировать пример репозитория в подходящее расположение на локальном компьютере:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Установка библиотеки MQTT

С помощью средства `vcpkg` скачайте библиотеку Eclipse Mosquitto и выполните ее сборку.

Используйте следующую команду, чтобы клонировать репозиторий **Vcpkg** в подходящее расположение на локальном компьютере:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Используйте следующие команды, чтобы подготовить среду `vcpkg`. При выполнении `vcpkg integrate install` вам потребуется командная строка с повышенными привилегиями:

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Используйте следующую команду, чтобы скачать библиотеку Eclipse Mosquitto и выполнить ее сборку:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Перенос примера в IoT Plug and Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Проверка примера кода без данных IoT Plug and Play

Внесите в код данные о своем центре Интернета вещей и устройстве, прежде чем выполнять его сборку и запуск.

Чтобы просмотреть пример кода в Visual Studio, откройте файл решения *MQTTWin32.sln* в папке *IoTMQTTSample\src\Windows*.

В **Обозревателе решений** щелкните правой кнопкой мыши проект **TelemetryMQTTWin32** и выберите **Назначить запускаемым проектом**.

В проекте **TelemetryMQTTWin32** откройте исходный файл **MQTT_Mosquitto.cpp**. Внесите в определения данных о подключениях сведения об устройстве, которые вы записали ранее. Замените заполнитель строки маркера для:

* идентификатора `IOTHUBNAME` именем созданного вами центра Интернета вещей;
* идентификатора `DEVICEID` именем созданного устройства;
* идентификатора `PWD` значением подписанного URL-адреса, которое вы создали для устройства.

Убедитесь, что код работает правильно, запустив обозреватель Интернета вещей Azure и начав прослушивание телеметрии.

Запустите приложение (CTRL+F5), и через несколько секунд вы увидите выходные данные, аналогичные следующим:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Выходные данные примера приложения MQTT":::

В обозревателе Интернета вещей Azure вы увидите, что устройство не является устройством IoT Plug and Play:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Устройство, не относящееся к IoT Plug and Play, в обозревателе Интернета вещей Azure":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Включение IoT Plug and Play для устройства

Устройство IoT Plug and Play должно соответствовать ряду простых соглашений. Если устройство отправляет идентификатор модели при подключении, оно становится устройством IoT Plug and Play.

В этом примере вы добавите идентификатор модели** к пакету подключения MQTT. Вы передадите идентификатор модели в качестве параметра querystring в `USERNAME` и измените `api-version` на `2020-05-31-preview`:

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Повторно выполните сборку и запустите пример.

Двойник устройства теперь включает идентификатор модели:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Просмотр идентификатора модели в обозревателе Azure IoT":::

Теперь вы можете перейти к компоненту IoT Plug and Play:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Просмотр компонентов в обозревателе Интернета вещей Azure":::

Теперь вы можете изменить код устройства для реализации телеметрии, свойств и команд, определенных в вашей модели. Пример реализации устройства термостата с использованием библиотеки Mosquitto см. на странице [Использование MQTT PnP с Центром Интернета вещей Azure без пакета SDK для Интернета вещей в Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) на сайте GitHub.

> [!NOTE]
> По умолчанию срок действия подписанного URL-адреса составляет 60 минут.

> [!NOTE]
>Клиент использует файл корневого сертификата `IoTHubRootCA_Baltimore.pem` для проверки удостоверения центра Интернета вещей, к которому он подключается.

### <a name="mqtt-topics"></a>Темы MQTT

Приведенные ниже определения предназначены для тем MQTT, которые устройство использует для отправки данных в центр Интернета вещей. Дополнительные сведения см. в статье [Взаимодействие с Центром Интернета вещей с помощью протокола MQTT](../iot-hub/iot-hub-mqtt-support.md):

* `DEVICE_CAPABILITIES_MESSAGE` определяет тему, которую устройство использует для передачи данных о реализуемых интерфейсах.
* `DEVICETWIN_PATCH_MESSAGE` определяет тему, которую устройство использует для передачи данных об обновлениях свойств в центр Интернета вещей.
* `DEVICE_TELEMETRY_MESSAGE` определяет тему, которую устройство использует для отправки телеметрии в центр Интернета вещей.

Дополнительные сведения об MQTT см. на странице с [примерами MQTT для Интернета вещей Azure](https://github.com/Azure-Samples/IoTMQTTSample/) в репозитории GitHub.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как изменить клиент устройства MQTT для соответствия соглашениям IoT Plug and Play. Дополнительные сведения см. в

> [!div class="nextstepaction"]
> [Архитектура](concepts-architecture.md)

Дополнительные сведения о поддержке Центра Интернета вещей для протокола MQTT см. в статье:

> [!div class="nextstepaction"]
> [Взаимодействие с Центром Интернета вещей с помощью протокола MQTT](../iot-hub/iot-hub-mqtt-support.md)
