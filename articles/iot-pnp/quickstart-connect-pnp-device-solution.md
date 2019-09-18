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
ms.openlocfilehash: a082e4b7896b317bf2b28971d3693bada95a3445
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806557"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Краткое руководство. Взаимодействие с устройством IoT Plug and Play (предварительная версия), подключенным к решению

Предварительная версия IoT Plug and Play упрощает Интернет вещей, позволяя взаимодействовать с возможностями устройства без знаний базовой реализации устройства. В этом кратком руководстве показано, как подключиться к устройству IoT Plug and Play, подключенному к решению, а также управлять им с помощью Node.js.

## <a name="prerequisites"></a>Предварительные требования

Скачайте и установите Node.js с сайта [nodejs.org](https://nodejs.org).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Подготовка Центра Интернета вещей

Для выполнения инструкций, приведенных в этом кратком руководстве, также необходим Центр Интернета вещей Azure в подписке Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

> [!NOTE]
> На этапе общедоступной предварительной версии функции IoT Plug and Play доступны только в центрах Интернета вещей, созданных в таких регионах, как **Центральная часть США**, **Северная Европа** и **Восточная Япония**.

Добавьте расширение Интернета вещей Microsoft Azure в интерфейсе командной строки Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Выполните приведенную ниже команду, чтобы создать удостоверение устройства в Центре Интернета вещей. Замените **YourIoTHubName** и **YourDevice** фактическими именами. Если у вас нет Центра Интернета вещей, создайте его, выполнив инструкции, указанные в [этой статье](../iot-hub/iot-hub-create-using-cli.md).

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Выполните приведенные ниже команды, чтобы получить _строку подключения к только что зарегистрированному устройству_:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Выполните приведенные ниже команды, чтобы получить _строку подключения к вашему Центру Интернета вещей_:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Подключение устройства

В рамках этого краткого руководства вы используете пример датчика состояния окружающей среды, написанного на Node.js, как устройство IoT Plug and Play. В приведенных ниже инструкциях описана установка и запуск устройства:

1. Клонируйте репозиторий GitHub:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. В терминале перейдите в корневую папку клонированного репозитория, после этого в папку **/azure-iot-samples-node/digital-twins/Quickstarts/Device**, а затем установите все зависимости, выполнив следующую команду:

    ```cmd/sh
    npm install
    ```

1. Настройте _строку подключения к устройству_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Выполните следующую команду, чтобы запустить пример:

    ```cmd/sh
    node sample_device.js
    ```

1. Отобразятся сообщения о том, что устройство отправило данные телеметрии и свойства. Теперь устройство готово к приему команд и обновлений свойств. Не закрывайте этот терминал. Он понадобится вам позже, чтобы проверить работу примеров служб.

## <a name="build-the-solution"></a>Выполните сборку решения.

В рамках этого краткого руководства вы используете пример решения Интернета вещей на Node.js для взаимодействия с примером устройства.

1. Откройте другой терминал. Перейдите в папку клонированного репозитория, а затем в папку **/azure-iot-samples-node/digital-twins/Quickstarts/Service**. Установите все зависимости, выполнив следующую команду:

    ```cmd/sh
    npm install
    ```

1. Настройте _строку подключения к центру_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Чтение свойства

1. При подключении устройства в терминале отображается следующее сообщение:

    ```cmd/sh
    reported state property as online
    ```

1. Откройте файл **get_digital_twin.js**. Замените `deviceID` удостоверением вашего устройства и сохраните файл.

1. Перейдите в терминал, открытый для запуска примера службы, и выполните следующую команду:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. В выходных данных вы увидите, что в компоненте _environmentSensor_ сообщается одно и то же состояние:

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Обновление доступного для записи свойства

1. Откройте файл **update_digital_twin_property.js**.

1. В начале файла находится набор констант, для которых указаны заполнители в верхнем регистре. Замените **deviceID** фактическим удостоверением устройства, обновите константы, указав приведенные ниже значения, и сохраните файл:

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Перейдите в терминал, открытый для запуска примера службы, и запустите пример с помощью следующей команды:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. В окне терминала отобразится информация о цифровом двойнике, связанная с вашим устройством. Найдите компонент _environmentSensor_. Вы увидите новое значение яркости (60).

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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
    Received an update for brightness: 60
    updated the property
    ```
2. Вернитесь в терминал _службы_ и еще раз выполните приведенную ниже команду, чтобы подтвердить, что свойство было обновлено.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. В выходных данных вы увидите, что в компоненте environmentSensor обновлено значение яркости. Примечание. Устройству может потребоваться некоторое время, чтобы завершить обновление. Вы можете повторять этот шаг, пока устройство фактически не обработает обновление свойства.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Вызов команды

1. Откройте файл **invoke_command.js**.

1. В начале файла замените `deviceID` фактическим удостоверением устройства. Обновите константы, указав приведенные ниже значения, а затем сохраните файл:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Перейдите в терминал, открытый для запуска примера службы. Для запуска примера используйте следующую команду:

    ```cmd/sh
    node invoke_command.js
    ```

1. В терминале выходные данные об успешном выполнении выглядят следующим образом:

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Перейдя в терминал _устройства_, вы увидите, что команда была подтверждена:

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы намерены перейти к изучению новых статьей, не удаляйте ресурсы, использованные при работе с этим кратким руководством. В противном случае вы можете удалить их, чтобы избежать дополнительных расходов.

Чтобы удалить центр и зарегистрированное устройство, выполните описанные ниже действия с помощью интерфейса командной строки Azure:

```azurecli-interactive
az group delete --name <Your group name>
```

Чтобы удалить только то устройство, которое вы зарегистрировали в Центре Интернета вещей, выполните следующие действия с помощью интерфейса командной строки Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к решению Интернета вещей. Дополнительные сведения о создании решения, взаимодействующего с устройствами IoT Plug and Play, см. в следующей статье:

> [!div class="nextstepaction"]
> [Практическое руководство. Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md) (Подключение к устройству IoT Plug and Play (предварительная версия) и взаимодействие с ним)
