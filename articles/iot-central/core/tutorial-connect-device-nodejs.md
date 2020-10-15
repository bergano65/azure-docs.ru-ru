---
title: Руководство. Подключение универсального клиентского приложения Node.js к Azure IoT Central | Документация Майкрософт
description: Из этого руководства вы узнаете, как разработчик устройства может подключить устройство, на котором выполняется клиентское приложение Node.js, к приложению Microsoft Azure IoT Central. Чтобы создать шаблон устройства, вам нужно импортировать модель его возможностей и добавить к ней представления для взаимодействия с устройством.
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
- devx-track-js
ms.openlocfilehash: 87284b88076cbd205a5d8ae388fe0b37c35cf6f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328553"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Руководство по Создание клиентского приложения и его подключение к приложению Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Эта статья предназначена для создателей решений и разработчиков устройств.*

Из этого руководства вы узнаете, как разработчик устройства может подключить клиентское приложение Node.js к приложению Microsoft Azure IoT Central. Приложение Node.js имитирует поведение датчика окружающей среды. С помощью примера _модели возможностей устройства_ вы создадите _шаблон устройства_ в IoT Central. Затем вы добавите представления в шаблон устройства, чтобы разрешить оператору взаимодействовать с устройством.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * импорт модели возможностей устройства для создания шаблона устройства;
> * добавление значений по умолчанию и пользовательских представлений в шаблон устройства;
> * публикация шаблона устройства и добавление реального устройства в приложение IoT Central;
> * создание кода устройства Node.js и его выполнение для подключения к приложению IoT Central;
> * просмотр имитированных данных телеметрии, которые отправляет устройство;
> * управление свойствами устройства через представления;
> * вызов синхронных и асинхронных команд для управления устройством.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Приложение Azure IoT Central, созданное на основе шаблона **Пользовательское приложение**. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md). Приложение должно быть создано не раньше 14.07.2020.
* Компьютер для разработки с установленным [Node.js](https://nodejs.org/) 10.0.0 или более поздней версии. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. В инструкциях этого руководства предполагается, что вы выполняете команду **node** в командной строке Windows. Но вы можете использовать Node.js во множестве других операционных системах.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Создание приложения Node.js

Ниже показано, как создать клиентское приложение Node.js, которое подключается к реальному устройству, добавленному в приложение. Это приложение Node.js моделирует поведение реального устройства.

1. Перейдите к созданной ранее папке `environmental-sensor` в среде командной строки.

1. Чтобы инициализировать проект Node.js и установить все требуемые зависимости, выполните указанные ниже команды и подтвердите все значения по умолчанию при выполнении `npm init`.

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Создайте файл с именем **environmentalSensor.js** в папке `environmental-sensor`.

1. Добавьте следующие инструкции `require` в начало файла **environmentalSensor.js**:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Добавьте следующие объявления переменных в файл:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Замените заполнители `{your Scope ID}`, `{your Device ID}` и `{your Primary Key}` записанными ранее значениями. В нашем примере параметру `targetTemperature` присваивается начальное значение 0. Но вы можете использовать текущие данные с устройства или значение из двойника устройства.

1. Чтобы отправить имитированные данные телеметрии в приложение Azure IoT Central, добавьте в файл следующую функцию:

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Имена элементов телеметрии (`temp` и `humid`) должны совпадать с именами в шаблоне устройства.

1. Чтобы отправить свойства двойника устройства в приложение Azure IoT Central, добавьте в файл следующую функцию:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central использует двойники устройств для синхронизации значений свойств между устройством и приложением IoT Central. Для значений свойств устройства используются передаваемые свойства двойника устройства. Доступные для записи свойства используют как передаваемые, так и требуемые свойства двойника устройства.

1. Чтобы определить и использовать доступные для записи параметры, на которые реагирует устройство, добавьте следующий фрагмент кода. Сообщение, которое устройство отправляет в ответ на [изменение записываемого свойства](concepts-telemetry-properties-commands.md#writeable-property-types), должно содержать поля `av` и `ac`. Поле `ad` является необязательным.

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed', 200);
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed', 200);
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting]}`);
            writeableProperties[setting](desiredChange[setting], (newValue, status, code) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  ad: status,
                  ac: code,
                  av: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Когда оператор задает доступное для записи свойство в приложении IoT Central, приложение использует требуемое свойство двойника устройства для отправки значения на устройство. Затем устройство отправляет ответ, используя передаваемое свойство двойника устройства. Когда IoT Central получает значение передаваемого свойства, состояние в представлении свойства меняется на **синхронизировано**.

    Имена свойств (`name` и `brightness`) должны совпадать с именами в шаблоне устройства.

1. Добавьте следующий код для обработки команд, отправляемых из приложения IoT Central:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    Имена команд (`blink`, `turnon`, `turnoff` и `rundiagnostics`) должны совпадать с именами в шаблоне устройства.

    Сейчас в IoT Central не используется схема ответа, определенная в модели возможностей устройства. При использовании синхронной команды полезные данные ответа могут включать любой допустимый текст JSON. При использовании асинхронной команды устройство должно немедленно вернуть ответ 202 с последующим обновлением передаваемого свойства по завершении работы. Обновление передаваемого свойства имеет следующий формат:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Оператор может просмотреть полезные данные ответа в журнале команд.

1. Добавьте следующий код, чтобы завершить подключение к Azure IoT Central, и присоедините функции в клиентском коде:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true',
              processorArchitecture: 'ARM',
              swVersion: '1.0.0'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Запуск приложения Node.js

Чтобы запустить клиентское приложение устройства, в среде командной строки введите следующую команду:

```cmd/sh
node environmentalSensor.js
```

Вы увидите, как устройство подключается к приложению Azure IoT Central и начинает отправлять данные телеметрии:

![Выполнение клиентского приложения](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Вы можете увидеть, как устройство реагирует на команды и обновления свойств:

![Действия клиентского приложения](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="view-raw-data"></a>Просмотр необработанных данных

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите продолжать работу с набором руководств по IoT Central, чтобы узнать больше о создании решении IoT Central, ознакомьтесь со следующим руководством:

> [!div class="nextstepaction"]
> [Создайте шаблон устройства шлюза](./tutorial-define-gateway-device-type.md)

Теперь, когда вы как разработчик устройства узнали о принципах создания устройств с помощью Node.js, ознакомьтесь со следующими руководствами:

* Прочитайте статью [Что такое шаблоны устройств?](./concepts-device-templates.md), чтобы узнать больше о роли шаблонов устройств при реализации кода устройства.
* Узнайте о [регистрации устройств с помощью IoT Central и безопасном подключении устройств к Azure IoT Central](./concepts-get-connected.md).
