---
title: Взаимодействие с устройством IoT Plug and Play (предварительная версия), подключенным к решению Интернета вещей Azure | Документация Майкрософт
description: Подключение к устройству IoT Plug and Play (предварительная версия), подключенному к решению Интернета вещей Azure, а также взаимодействие с ним с помощью C# (.NET).
author: baanders
ms.author: baanders
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9e8bc6c4ad7ed852ddaae2e193b91887fcd92e47
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550780"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Краткое руководство. Взаимодействие с устройством IoT Plug and Play (предварительная версия), подключенным к решению (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Предварительная версия IoT Plug and Play упрощает Интернет вещей, позволяя взаимодействовать с возможностями устройства без знаний базовой реализации устройства. В этом кратком руководстве показано, как подключиться к устройству IoT Plug and Play, подключенному к решению, а также управлять им с помощью C# (.NET).

## <a name="prerequisites"></a>предварительные требования

Для выполнения инструкций, приведенных в этом кратком руководстве, необходимо установить .NET Core (2.x.x или 3.x.x) на компьютере для разработки. Вы можете скачать предпочтительную версию пакета SDK для .NET Core для нескольких платформ по [этой ссылке](https://dotnet.microsoft.com/download/dotnet-core/).

Версию .NET, которая находится на компьютере разработки, можно проверить, выполнив следующую команду в окне локального терминала: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к вашему Центру Интернета вещей_ (запишите для использования в будущем):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Запуск примера устройства

В рамках этого краткого руководства вы используете пример датчика состояния окружающей среды, написанного на C#, как устройство IoT Plug and Play. В приведенных ниже инструкциях описана установка и запуск устройства:

1. Откройте окно терминала в предпочитаемом каталоге. Выполните следующую команду для клонирования репозитория [примеров Azure IoT для C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) с GitHub в это расположение:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Это окно терминала теперь будет использоваться в качестве терминала _устройства_. Перейдите в папку клонированного репозитория, а затем в папку **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample**.

1. Настройте _строку подключения к устройству_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Выполните сборку требуемых пакетов и запустите пример с помощью следующей команды:

    ```cmd\sh
        dotnet run
    ```

1. Вы увидите сообщения о том, что устройство успешно зарегистрировано и ожидает обновлений из облака. Это указывает на то, что устройство готово к получению команд и обновлений свойств и начало отправлять данные телеметрии в центр. Не закрывайте этот терминал. Он понадобится вам позже, чтобы проверить работу примеров служб.

## <a name="run-the-sample-solution"></a>Запуск примера решения

В рамках этого краткого руководства вы используете пример решения Интернета вещей на C# для взаимодействия с примером устройства.

1. Откройте другое окно терминала (это будет терминал _службы_). Перейдите в папку клонированного репозитория, а затем в папку **/azure-iot-samples-csharp/digitaltwin/Samples/service**.

1. Настройте _строку подключения центра Интернета вещей_и _идентификатор устройства_, чтобы разрешить службе подключаться к ним:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Чтение свойства

1. После подключения _устройства_ к его терминалу отобразится следующее сообщение, содержащее сведения о его сетевом состоянии:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Перейдите в окно терминала _службы_ и запустите пример с помощью следующих команд, чтобы прочитать сведения об устройстве:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. В выходных данных терминала _службы_ перейдите к компоненту `environmentalSensor`. Вы видите, что свойство `state`, которое указывает, находится ли устройство в сети, определено как _true_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
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

1. Запустите пример с помощью следующей команды, чтобы обновить свойство:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. В выходных данных терминала _службы_ содержатся обновленные сведения об устройстве. Перейдите к компоненту `environmentalSensor`, чтобы просмотреть новое значение яркости — 42.

    ```json
        "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

1. Перейдя в терминал _устройства_, вы увидите, что устройство получило обновление:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Вернитесь в окно терминала _службы_ и еще раз выполните приведенную ниже команду для получения сведений об устройстве, чтобы подтвердить, что свойство было обновлено.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. В выходных данных терминала _службы_ вы увидите, что в компоненте `environmentalSensor` обновлено значение яркости. Примечание. Устройству может потребоваться некоторое время, чтобы завершить обновление. Вы можете повторять этот шаг, пока устройство фактически не обработает обновление свойства.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Вызов команды

1. Перейдите в терминал _службы_ и задайте следующие переменные, чтобы определить, какую команду следует вызвать:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Чтобы запустить пример для вызова команды, используйте следующие команды:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. В выходных данных в терминале _службы_ должно содержаться следующее подтверждение:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Перейдя в терминал _устройства_, вы увидите, что команда была подтверждена:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к решению Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md) (Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним)
