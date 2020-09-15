---
title: Подключение примера кода устройства C IoT Plug and Play (предварительная версия) к Центру Интернета вещей | Документация Майкрософт
description: Создание и запуск примера кода устройства C IoT Plug and Play (предварительная версия), который использует несколько компонентов и подключается к Центру Интернета вещей. С помощью обозревателя Интернета вещей Azure просматривайте сведения, отправленные устройством в центр.
author: ericmitt
ms.author: ericmitt
ms.date: 07/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 29017ec11429b26018093980ca71c317b12085b5
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505759"
---
# <a name="tutorial-connect-an-iot-plug-and-play-multiple-component-device-applications-running-on-linux-or-windows-to-iot-hub-c"></a>Руководство по подключению приложений устройства IoT Plug and Play с несколькими компонентами в Linux или Windows к Центру Интернета вещей (C)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

В этом учебнике показано, как создать пример приложения устройства IoT Plug and Play с компонентами и корневым интерфейсом, подключить его к Центру Интернета вещей и с помощью обозревателя Центра Интернета вещей Azure просмотреть сведения, отправляемые в центр. Пример приложения написан на языке C и включен в пакет SDK для устройств Azure IoT для C. Разработчик решения может использовать обозреватель Интернета вещей Azure, чтобы ознакомиться с возможностями устройства IoT Plug and Play, не просматривая код устройства.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Операции, описанные в этом учебнике, можно выполнить в Linux или Windows. Команды оболочки в этом учебнике соответствуют требованиям соглашения Linux для разделителей пути "`/`". Если вы используете Windows, не забудьте изменить эти разделители на "`\`".

Необходимые условия зависят от операционной системы:

### <a name="linux"></a>Linux

В этом учебнике предполагается, что вы используете Ubuntu Linux. Действия, описанные в этом руководстве, были протестированы в Ubuntu 18.04.

Для выполнения инструкций, указанных в этом учебнике, в ОС Linux необходимо установить указанное ниже программное обеспечение в локальной среде.

Установите **GCC**, **Git**, **cmake** и все необходимые зависимости с помощью команды `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Убедитесь, что версия `cmake` выше **2.8.12**, а версия **GCC** выше **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Для выполнения инструкций из этого учебника в ОС Windows установите в локальной среде Windows такое программное обеспечение:

* [Visual Studio (Community, Professional или Enterprise)](https://visualstudio.microsoft.com/downloads/). При [установке](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) Visual Studio обязательно добавьте рабочую нагрузку **Разработка классических приложений на C++** .
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Обозреватель Интернета вещей Azure

Для взаимодействия с примером устройства во второй части этого учебника используется **обозреватель Интернета вещей Azure**. [Скачайте и установите последний выпуск обозревателя Интернета вещей Azure](./howto-use-iot-explorer.md) для вашей операционной системы.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к Центру Интернета вещей_ для вашего концентратора. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим учебником.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Вы также можете использовать обозреватель Интернета вещей, чтобы найти строку подключения для центра Интернета вещей.

Выполните указанную ниже команду, чтобы получить _строку подключения устройства_, добавленного в центр. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим учебником.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Загрузка кода

С помощью этого учебника вы подготовите среду разработки, которую можно использовать для клонирования и сборки пакета SDK для устройств Центра Интернета вещей Azure для C.

Откройте командную строку в выбранной папке. Выполните следующую команду для клонирования репозитория GitHub [пакетов SDK и библиотек Интернета вещей Azure для С](https://github.com/Azure/azure-iot-sdk-c) в это расположение:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Выполнение этой операции может занять несколько минут.

## <a name="build-and-run-the-code"></a>Сборка и выполнение кода

Вы можете создать и запустить код с помощью Visual Studio или `cmake` в командной строке.

### <a name="use-visual-studio"></a>Использование Visual Studio

1. Откройте корневую папку клонированного репозитория. Через пару секунд поддержка **CMake** в Visual Studio создаст все, что необходимо для выполнения и отладки проекта.
1. Когда среда Visual Studio в **Обозревателе решений** готова, перейдите к примеру *iothub_client/samples/pnp/pnp_temperature_controller/* .
1. Щелкните правой кнопкой мыши файл *pnp_temperature_controller.c* и выберите **Add Debug Configuration** (Добавить конфигурацию отладки). Выберите **По умолчанию**.
1. Visual Studio откроет файл *launch.vs.json*. Измените этот файл, как показано в следующем фрагменте кода, чтобы задать необходимые переменные среды:

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "connectionString",
            "IOTHUB_DEVICE_CONNECTION_STRING": "<Your device connection string>"
          }
        }
      ]
    }
    ```

1. Щелкните правой кнопкой мыши файл *pnp_temperature_controller.c* и выберите **Задать как элемент запуска**.
1. Для трассировки выполнения кода в Visual Studio добавьте точку останова в функцию `main` в файле *pnp_temperature_controller.c*.
1. Теперь можно запустить и отладить пример в меню **Отладка**.

Теперь устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в концентратор. Продолжите работу примера, после того как выполните следующие действия.

### <a name="use-cmake-at-the-command-line"></a>Использование `cmake` в командной строке

Сборка примера

1. Создайте подкаталог _CMake_ в корневой папке пакета SDK для клонированного устройства и перейдите к этой папке:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Выполните следующие команды, чтобы создать и собрать файлы проекта для пакетов SDK и примеров:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

Запуск примера:

1. Создайте две переменные среды, чтобы настроить приложение для подключения к центру Интернета вещей с помощью строки подключения:

    * **IOTHUB_DEVICE_SECURITY_TYPE** со значением `"connectionString"`.
    * **IOTHUB_DEVICE_CONNECTION_STRING**, чтобы сохранить строку подключения устройства, которую вы записали ранее.

1. Из папки _cmake_ перейдите к папке, содержащей исполняемый файл, и запустите его:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

Теперь устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в концентратор. Продолжите работу примера, после того как выполните следующие действия.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Проверка кода с помощью обозревателя Azure IoT

После запуска примера клиента устройства используйте обозреватель Интернета вещей Azure, чтобы убедиться, что он работает.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Просмотр кода

В этом примере реализуется устройство контроллера температуры IoT Plug and Play. В этом примере реализуется модель с [несколькими компонентами](concepts-components.md). [Файл модели языка определения Digital Twins (DTDL) для устройства определения температуры](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) определяет телеметрию, свойства и команды, реализуемые устройством.

### <a name="iot-plug-and-play-helper-functions"></a>Функции вспомогательного приложения IoT Plug and Play

В этом примере код использует некоторые функции вспомогательного приложения из папки */common*:

*pnp_device_client_ll* содержит метод connect для IoT Plug and Play с `model-id`, включенным в качестве параметра: `PnP_CreateDeviceClientLLHandle`.

*pnp_protocol* содержит функции вспомогательного приложения IoT Plug and Play:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

Эти функции вспомогательного приложения достаточно универсальны для использования в собственном проекте. В этом примере они используются в трех файлах, соответствующих каждому компоненту в модели:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

Например, в файле *pnp_deviceinfo_component* функция `SendReportedPropertyForDeviceInformation` использует две функции вспомогательного приложения:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

Каждый компонент в примере соответствует этому шаблону.

### <a name="code-flow"></a>поток кода.

Функция `main` инициализирует соединение и отправляет идентификатор модели:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

Код использует `PnP_CreateDeviceClientLLHandle` для подключения к центру Интернета вещей, установки `modelId` в качестве параметра и настройки метода устройства и обработчиков обратного вызова двойника устройства для прямых методов и обновлений двойника устройства:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` также содержит сведения о соединении. **IOTHUB_DEVICE_SECURITY_TYPE** переменной среды определяет, использует ли образец строку подключения или службу подготовки устройств для подключения к центру Интернета вещей.

Если устройство отправляет идентификатор модели, оно становится устройством IoT Plug and Play.

При наличии обработчиков обратного вызова устройство реагирует на обновления двойника и прямые вызовы метода:

* Для обратного вызова двойника устройства `PnP_TempControlComponent_DeviceTwinCallback` вызывает функцию `PnP_ProcessTwinData` для обработки данных. `PnP_ProcessTwinData` использует *шаблон посетителя* для анализа JSON и последующего посещения каждого свойства, вызывая `PnP_TempControlComponent_ApplicationPropertyCallback` для каждого элемента.

* Для обратного вызова команд функция `PnP_TempControlComponent_DeviceMethodCallback` использует вспомогательную функцию для анализа команды и имен компонента:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    Затем функция `PnP_TempControlComponent_DeviceMethodCallback` вызывает команду для компонента:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

Функция `main` инициализирует свойства только для чтения, отправляемые в центр Интернета вещей:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

Функция `main` входит в цикл для обновления данных событий и телеметрии каждого компонента:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

Функция `PnP_ThermostatComponent_SendTelemetry` показывает, как использовать структуру `PNP_THERMOSTAT_COMPONENT`. В примере эта структура используется для хранения сведений о двух терморегуляторах в контроллере температуры. Код использует функцию `PnP_CreateTelemetryMessageHandle` для подготовки и отправки сообщения:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

Затем функция `main` уничтожает различные компоненты и прерывает подключение к концентратору.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого учебника вы узнали, как подключить устройство IoT Plug and Play с компонентами к центру Интернета вещей. Дополнительные сведения о моделях устройства IoT Plug and Play см. в статье

> [!div class="nextstepaction"]
> [Руководство для разработчиков IoT Plug and Play (предварительная версия)](concepts-developer-guide.md)
