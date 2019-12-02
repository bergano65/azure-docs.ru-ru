---
title: Взаимодействие с устройством IoT Plug and Play (предварительная версия), подключенным к решению Интернета вещей Azure | Документация Майкрософт
description: Подключение к устройству IoT Plug and Play (предварительная версия), подключенному к решению Интернета вещей Azure, а также взаимодействие с ним с помощью Node.js.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3275c01059a61e4eb8591948695656f4e4b722ed
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152122"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Краткое руководство. Взаимодействие с устройством IoT Plug and Play (предварительная версия), подключенным к решению (Node.js)

Предварительная версия IoT Plug and Play упрощает Интернет вещей, позволяя взаимодействовать с возможностями устройства без знаний базовой реализации устройства. В этом кратком руководстве показано, как подключиться к устройству IoT Plug and Play, подключенному к решению, а также управлять им с помощью Node.js.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций, приведенных в этом кратком руководстве, необходимо установить платформу Node.js на компьютере для разработки. Вы можете скачать последнюю рекомендуемую версию для нескольких платформ на сайте [nodejs.org](https://nodejs.org).

Текущую версию Node.js на компьютере, на котором ведется разработка, можно проверить, используя следующую команду:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="connect-your-device"></a>Подключение устройства

В рамках этого краткого руководства вы используете пример датчика состояния окружающей среды, написанного на Node.js, как устройство IoT Plug and Play. В приведенных ниже инструкциях описана установка и запуск устройства:

1. Откройте окно терминала в предпочитаемом каталоге. Выполните следующую команду для клонирования репозитория GitHub [примеров Интернета вещей Azure для Node.js](https://github.com/azure-samples/azure-iot-samples-node) в это расположение:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Это окно терминала теперь будет использоваться в качестве терминала _устройства_. Перейдите к клонированному репозиторию, а затем в папку **/azure-iot-samples-node/digital-twins/Quickstarts/Device**. Установите все зависимости, выполнив следующую команду:

    ```cmd/sh
    npm install
    ```

1. Настройте _строку подключения к устройству_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Выполните следующую команду, чтобы запустить пример:

    ```cmd/sh
    node sample_device.js
    ```

1. Отобразится сообщение о том, что устройство отправило определенные сведения и находится в сети. Это указывает на то, что устройство начало отправлять данные телеметрии в центр и готово к получению команд и обновлений свойств. Не закрывайте этот терминал. Он понадобится вам позже, чтобы проверить работу примеров служб.

## <a name="build-the-solution"></a>Выполните сборку решения.

В рамках этого краткого руководства вы используете пример решения Интернета вещей на Node.js для взаимодействия с примером устройства.

1. Откройте другое окно терминала (это будет терминал _службы_). Перейдите в папку клонированного репозитория, а затем в папку **/azure-iot-samples-node/digital-twins/Quickstarts/Service**. Установите все зависимости, выполнив следующую команду:

    ```cmd/sh
    npm install
    ```

1. Настройте _строку подключения центра Интернета вещей_, чтобы разрешить службе подключаться к ней:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Чтение свойства

1. После подключения _устройства_ к его терминалу отобразится следующее сообщение, содержащее сведения о его сетевом состоянии:

    ```cmd/sh
    reported state property as online
    ```

1. В папке **/azure-iot-samples-node/digital-twins/Quickstarts/Service** откройте файл **get_digital_twin.js**. Замените заполнитель `<DEVICE_ID_GOES_HERE>` удостоверением вашего устройства и сохраните файл.

1. Перейдите в окно терминала _службы_ и запустите пример с помощью следующей команды для чтения сведений об устройстве:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. В выходных данных терминала _службы_ перейдите к компоненту `environmentalSensor`. Вы увидите, что свойство `state` зарегистрировано как _online_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Обновление доступного для записи свойства

1. Откройте файл **update_digital_twin_property.js**.

1. В начале файла находится набор констант, для которых указаны заполнители в верхнем регистре. Замените заполнитель `<DEVICE_ID_GOES_HERE>` фактическим удостоверением устройства, обновите остальные константы, указав приведенные ниже значения, и сохраните файл:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Перейдите в терминал _службы_ и запустите пример с помощью следующей команды для обновления свойства:

    ```cmd/sh
    node update_digital_twin_property.js
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
            "value": "online"
          }
        }
      }
    }
    ```

1. Перейдя в терминал _устройства_, вы увидите, что устройство получило обновление:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Вернитесь в окно терминала _службы_ и еще раз выполните приведенную ниже команду для получения сведений об устройстве, чтобы подтвердить, что свойство было обновлено.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. В выходных данных терминала _службы_ вы увидите, что в компоненте `environmentalSensor` обновлено значение яркости. Примечание. Устройству может потребоваться некоторое время, чтобы завершить обновление. Вы можете повторять этот шаг, пока устройство фактически не обработает обновление свойства.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Вызов команды

1. Откройте файл **invoke_command.js**.

1. В начале файла замените заполнитель `<DEVICE_ID_GOES_HERE>` фактическим удостоверением устройства. Обновите остальные константы, указав приведенные ниже значения, а затем сохраните файл:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Перейдите к терминалу _службы_. Чтобы запустить пример для вызова команды, используйте следующую команду:

    ```cmd/sh
    node invoke_command.js
    ```

1. В выходных данных в терминале _службы_ должно содержаться следующее подтверждение:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Перейдя в терминал _устройства_, вы увидите, что команда была подтверждена:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к решению Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md) (Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним)
