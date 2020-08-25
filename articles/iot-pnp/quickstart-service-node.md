---
title: Взаимодействие с устройством IoT Plug and Play (предварительная версия), подключенным к решению Интернета вещей Azure (Node.js) | Документация Майкрософт
description: Подключение к устройству IoT Plug and Play (предварительная версия), подключенному к решению Интернета вещей Azure, а также взаимодействие с ним с помощью Node.js.
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: fd65dcc9ce0be07daa5848a0ac583cf795150e47
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184760"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Краткое руководство. Взаимодействие с устройством IoT Plug and Play (предварительная версия), подключенным к решению (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

Предварительная версия IoT Plug and Play упрощает Интернет вещей, позволяя взаимодействовать с возможностями устройства без знаний базовой реализации устройства. В этом кратком руководстве показано, как подключиться к устройству IoT Plug and Play, подключенному к решению, а также управлять им с помощью Node.js.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Для выполнения инструкций, приведенных в этом кратком руководстве, необходимо установить платформу Node.js на компьютере для разработки. Вы можете скачать последнюю рекомендуемую версию для нескольких платформ на сайте [nodejs.org](https://nodejs.org).

Текущую версию Node.js на компьютере, на котором ведется разработка, можно проверить, используя следующую команду:

```cmd/sh
node --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Выполните следующую команду, чтобы получить _строку подключения к Центру Интернета вещей_ для вашего концентратора. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим кратким руководством.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Выполните указанную ниже команду, чтобы получить _строку подключения устройства_, добавленного в центр. Запишите эту строку подключения. Вы будете использовать ее позже при работе с этим кратким руководством.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Клонирование репозитория пакета SDK с помощью примера кода

Пакет SDK службы предоставляется в режиме предварительной версии, поэтому эти примеры нужно клонировать из [ветви предварительной версии пакета SDK для Node](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh). Откройте окно терминала в любой папке. С помощью следующей команды клонируйте ветвь **pnp-preview-refresh** репозитория [пакета SDK Microsoft Azure IoT для Node.js](https://github.com/Azure/azure-iot-sdk-node) на сайте GitHub:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
```

## <a name="run-the-sample-device"></a>Запуск примера устройства

С помощью этого краткого руководства вы сможете использовать пример термостата, написанный на Node.js, как устройство IoT Plug and Play. Чтобы запустить пример устройства, сделайте следующее:

1. Откройте окно терминала и перейдите в локальную папку, содержащую пакет SDK Microsoft Azure IoT для Node.js, клонированный из репозитория GitHub.

1. Это окно терминала используется в качестве терминала **устройства**. Перейдите в папку клонированного репозитория, а затем в папку */azure-iot-sdk-node/device/samples/pnp*. Установите все зависимости, выполнив следующую команду:

    ```cmd/sh
    npm install
    ```

1. Настройте _строку подключения к устройству_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Запустите пример термостата с помощью следующей команды:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Отобразится сообщение о том, что устройство отправило определенные сведения и находится в сети. Эти сообщения означают, что устройство начало отправлять в концентратор данные телеметрии и теперь готово получать команды и обновления свойств. Не закрывайте этот терминал, он понадобится вам, чтобы проверить работу примера службы.

## <a name="run-the-sample-solution"></a>Запуск примера решения

С помощью этого краткого руководства вы примените пример решения Интернета вещей на Node.js для взаимодействия с настроенным примером устройства.

1. Откройте другое окно терминала, которое будет терминалом **службы**. Пакет SDK службы предоставляется в режиме предварительной версии, поэтому эти примеры нужно клонировать из [ветви предварительной версии пакета SDK для Node](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh).

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
    ```

1. Перейдите в папку клонированного репозитория, а затем в папку */azure-iot-sdk-node/digitaltwins/samples/service/javascript*. Установите все зависимости, выполнив следующую команду:

    ```cmd/sh
    npm install
    ```

1. Настройте переменные среды для идентификатора устройства и _строки подключения к Центру Интернета вещей_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Чтение свойства

1. Когда вы выполняли пример устройства термостата в терминале **устройства**, отображались следующие сведения о состоянии подключения.

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Перейдите в окно терминала **службы** и запустите пример с помощью следующей команды для чтения сведений об устройстве:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. В выходных данных терминала **службы** найдите ответ цифрового двойника. Вы увидите сведения об идентификаторе модели устройства и его свойствах.

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. Следующий фрагмент кода из файла *get_digital_twin.js* получает идентификатор модели двойника устройства.

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

В нашем примере он выводит значение `Model Id: dtmi:com:example:Thermostat;1`.

### <a name="update-a-writable-property"></a>Обновление доступного для записи свойства

1. Откройте файл *update_digital_twin.js* в любом редакторе кода.

1. Изучите пример кода. Вы увидите, как создать обновление в формате JSON для изменения свойств цифрового двойника устройства. В этом примере код изменяет значение температуры термостата на 42.

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. В терминале **службы** с помощью следующей команды запустите следующий пример для обновления свойства.

    ```cmd/sh
    node update_digital_twin.js
    ```

1. В терминале **устройства** вы увидите, что устройство успешно получило обновление.

    ```cmd/sh
    The following properties will be updated for root interface:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. В терминале **службы** выполните следующую команду, чтобы убедиться, что свойство обновлено.

    ```cmd/sh
    node get_digital_twin.js
    ```

1. В терминале **службы** вы увидите ответ от цифрового двойника, где в компоненте `thermostat1` отображается обновленное значение температуры. Устройству может потребоваться некоторое время, чтобы завершить обновление. Повторяйте этот шаг, пока устройство не завершит обновление свойства.

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>Вызов команды

1. Откройте файл *invoke_command.js* и изучите его код.

1. Перейдите к терминалу **службы**. Чтобы запустить пример для вызова команды, используйте следующую команду:

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. В выходных данных в терминале **службы** отобразится следующее подтверждение.

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. В терминале **устройства** вы увидите, что команда успешно подтверждена.

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как подключить устройство IoT Plug and Play к решению Интернета вещей. Дополнительные сведения о моделях устройства IoT Plug and Play см. в статье

> [!div class="nextstepaction"]
> [Руководство для разработчиков IoT Plug and Play (предварительная версия)](concepts-developer-guide.md)
