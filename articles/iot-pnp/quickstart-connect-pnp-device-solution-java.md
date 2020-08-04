---
title: Взаимодействие с подключенным к решению устройством IoT Plug and Play (предварительная версия) с помощью Java | Документация Майкрософт
description: Подключение к устройству IoT Plug and Play (предварительная версия), подключенному к решению Azure IoT, а также взаимодействие с ним с помощью Java.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 752d1d925c55ed79c7eb1673c6602adb9c2371fb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320992"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Краткое руководство. Взаимодействие с подключенным к решению устройством IoT Plug and Play (предварительная версия) с помощью Java

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Предварительная версия IoT Plug and Play упрощает Интернет вещей, позволяя взаимодействовать с возможностями устройства без знаний базовой реализации устройства. В этом кратком руководстве показано, как подключиться к устройству IoT Plug and Play, подключенному к решению, а также управлять им с помощью Java.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций, приведенных в этом кратком руководстве, необходимо установить платформу Java SE 8 на компьютере для разработки. Кроме того, необходимо установить Maven 3.

Подробные сведения см. в статье о [подготовке среды разработки](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) в пакете SDK для устройств Azure IoT для Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к вашему Центру Интернета вещей_ (запишите для использования в будущем):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Запуск примера устройства

В рамках этого краткого руководства вы используете в качестве устройства IoT Plug and Play пример датчика состояния окружающей среды, написанный на Java. В приведенных ниже инструкциях описана установка и запуск устройства:

1. Откройте окно терминала в предпочитаемом каталоге. Выполните следующую команду для клонирования репозитория [примеров Azure IoT для Java](https://github.com/Azure-Samples/azure-iot-samples-java) с GitHub в это расположение:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Это окно терминала теперь будет использоваться в качестве терминала _устройства_. Перейдите в папку клонированного репозитория, а затем в папку **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample**. Установите необходимые библиотеки и скомпилируйте приложение имитированного устройства, выполнив следующую команду:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Настройте _строку подключения к устройству_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Выполните следующую команду, чтобы открыть пример из папки устройства.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Отобразятся сообщения о том, что устройство подключено, выполняет различные шаги по настройке и ожидает обновления службы. После этого отобразятся журналы телеметрии. Это указывает на то, что устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в центр. Продолжите работу примера, после того как выполните следующие действия. Не закрывайте этот терминал. Он понадобится вам позже, чтобы проверить работу примеров служб.

## <a name="run-the-sample-solution"></a>Запуск примера решения

В рамках этого краткого руководства вы используете пример решения Интернета вещей на Java для взаимодействия с примером устройства.

1. Откройте другое окно терминала (это будет терминал _службы_). Перейдите в папку клонированного репозитория, а затем в папку **/azure-iot-samples-java/digital-twin/Samples/service/JdkSample**.

1. Установите необходимые библиотеки и скомпилируйте приложение службы, выполнив следующую команду:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Настройте _строку подключения центра Интернета вещей_и _идентификатор устройства_, чтобы разрешить службе подключаться к ним:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Чтение свойства

1. После подключения _устройства_ к его терминалу отобразится одно из следующих сообщений с информацией о его сетевом состоянии: Свойство `state`, которое указывает, находится ли устройство в сети, имеет значение _true_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Перейдите в окно терминала _службы_ и запустите пример службы с помощью следующей команды для чтения сведений об устройстве:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. В выходных данных терминала _службы_ перейдите к компоненту `environmentalSensor`. Вы увидите, что свойство `state` имеет значение _true_:
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Обновление доступного для записи свойства

1. Перейдите в терминал _службы_ и задайте следующие переменные, чтобы определить, какое свойство следует обновить:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Запустите пример службы с помощью следующей команды, чтобы обновить свойство:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. В выходных данных терминала _службы_ содержатся обновленные сведения об устройстве. Перейдите к компоненту `environmentalSensor`, чтобы просмотреть новое значение яркости — 42.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Перейдя в терминал _устройства_, вы увидите, что устройство получило обновление:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Вернитесь в окно терминала _службы_ и еще раз выполните приведенную ниже команду для получения сведений об устройстве, чтобы подтвердить, что свойство было обновлено.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. В выходных данных терминала _службы_ вы увидите, что в компоненте `environmentalSensor` обновлено значение яркости. Примечание. Устройству может потребоваться некоторое время, чтобы завершить обновление. Вы можете повторять этот шаг, пока устройство фактически не обработает обновление свойства.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Вызов команды

1. Перейдите в терминал _службы_ и задайте следующие переменные, чтобы определить, какую команду следует вызвать:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Чтобы запустить пример службы для вызова команды, используйте следующую команду:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. В выходных данных в терминале _службы_ должно содержаться следующее подтверждение:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Перейдя в терминал _устройства_, вы увидите, что команда была подтверждена:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к решению Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md) (Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним)
