---
title: Руководство. Подключение универсального клиентского приложения Node.js к Azure IoT Central | Документация Майкрософт
description: Из этого руководства вы узнаете, как разработчик устройства может подключить устройство, на котором выполняется клиентское приложение Node.js, к приложению Microsoft Azure IoT Central. Чтобы создать шаблон устройства, вам нужно импортировать модель его возможностей и добавить к ней представления для взаимодействия с устройством.
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624340"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Руководство по Создание клиентского приложения Node.js и его подключение к приложению Azure IoT Central (Node.js)

Из этого руководства вы узнаете, как разработчик устройства может подключить клиентское приложение Node.js к приложению Microsoft Azure IoT Central. Приложение Node.js моделирует поведение реального устройства. С помощью примера _модели возможностей устройства_, представляющего датчик окружающей среды, создайте _шаблон устройства_ в IoT Central. К этому шаблону вы добавите представления, которые позволяют визуализировать данные телеметрии устройства, управлять его свойствами и выполнять на нем команды.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * импорт модели возможностей устройства для создания шаблона устройства;
> * добавление значений по умолчанию и пользовательских представлений в шаблон устройства;
> * публикация шаблона устройства и добавление реального устройства в приложение IoT Central;
> * создание кода устройства Node.js и его выполнение для подключения к приложению IoT Central;
> * Просмотреть смоделированную телеметрию, которую отправляет устройство.
> * управление свойствами устройства через представления;
> * вызов команд для управления устройством.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Приложение Azure IoT Central, созданное на основе шаблона **пользовательского приложения**. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
* Компьютер для разработки с установленным [Node.js](https://nodejs.org/) 10.0.0 или более поздней версии. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. Node.js доступен для разных операционных систем. В инструкциях этого руководства предполагается, что вы выполняете команду **node** в командной строке Windows. Вы можете использовать Node.js в разных операционных системах.

## <a name="create-a-device-template"></a>Создание шаблона устройства

Создайте на локальном компьютере папку с именем `environmental-sensor`.

Скачайте JSON-файл [модели возможностей для датчика окружающей среды](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) и сохраните его в папке `environmental-sensor`.

С помощью текстового редактора замените в скачанном файле `EnvironmentalSensorInline.capabilitymodel.json` два вхождения строки `{YOUR_COMPANY_NAME_HERE}` названием своей компании.

Создайте в приложении Azure IoT Central шаблон устройства с именем *Датчик окружающей среды*, импортировав файл модели возможностей устройства `EnvironmentalSensorInline.capabilitymodel.json`.

![Шаблон устройства с импортированной моделью возможностей устройства](./media/tutorial-connect-device/device-template.png)

Модель возможностей устройства включает два интерфейса: стандартный **Device Information** (Сведения об устройстве) и **Environmental Sensor** (Датчик среды). Интерфейс **датчика окружающей среды** определяет описанные ниже возможности.

| Тип | Отображаемое имя | Описание |
| ---- | ------------ | ----------- |
| Свойство | Состояние устройства     | Состояние устройства. Доступны два состояния устройства: автономное и подключенное. |
| Свойство | Имя клиента    | Имя клиента, который сейчас работает с этим устройством. |
| Свойство | Уровень яркости | Уровень яркости светового индикатора на устройстве. Можно установить значения 1 (высокий), 2 (средний) или 3 (низкий). |
| Телеметрия | температура; | Текущая температура устройства. |
| Телеметрия | влажность.    | Текущая влажность устройства. |
| Get-Help | blink          | Включает мигание светодиодного индикатора на заданный период. |
| Get-Help | turnon         | Включает светодиодный индикатор на устройстве. |
| Get-Help | turnoff        | Выключает светодиодный индикатор на устройстве. |
| Get-Help | rundiagnostics | Запускает цикл диагностики. |

Чтобы настроить отображение свойства **Device State** в приложении IoT Central, выберите команду **Customize** (Настроить) в шаблоне устройства. Разверните элемент **Device State**, введите значение _Online_ (Подключенное) для параметра **True name** (Имя для значения True) и _Offline_ (Автономное) для параметра **False name** (Имя для значение False). Теперь сохраните эти изменения.

![Настройка шаблона устройства](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Создание представлений

Представления позволяют взаимодействовать с устройствами, подключенными к приложению IoT Central. Например, можно создать представления для отображения данных телеметрии и свойств, а также для изменения доступных для записи и облачных свойств. Представления — это часть шаблона устройства.

Чтобы добавить в шаблон устройства **Environmental sensor** (Датчик окружающей среды) несколько стандартных представлений, перейдите к этому шаблону устройства, выберите раздел **Views** (Представления) и щелкните плитку **Generate Default views** (Создать представления по умолчанию). Убедитесь, что параметры **Overview** и **About** имеют значения **On** (Включено), а затем щелкните **Generate default dashboard views** (Создать представления по умолчанию для панели мониторинга). Теперь в шаблоне определены два представления по умолчанию.

Интерфейс **Environmental Sensor** (Датчик окружающей среды) содержит два доступных для записи свойства: **Customer Name** (Имя клиента) и **Brightness Level** (Уровень яркости). Чтобы создать представление для изменения этих свойств, сделайте следующее:

1. Выберите **Views** (Представления) и щелкните плитку **Editing device and cloud data** (Изменение данных об устройстве и облаке).

1. Введите имя формы _Properties_ (Свойства).

1. Выберите свойства **Brightness Level** (Уровень яркости) и **Customer Name** (Имя клиента). Затем щелкните **Add section** (Добавить раздел).

1. Сохраните изменения.

![Добавление представления для редактирования свойств](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Публикация шаблона

Прежде чем добавлять в приложение IoT Central устройство, основанное на шаблоне **Environmental sensor** (Датчик окружающей среды), необходимо опубликовать этот шаблон.

Выберите действие **Publish** (Опубликовать) в шаблоне устройства. На панели, где отображаются изменения для публикации, щелкните **Publish** (Опубликовать).

Чтобы убедиться в готовности шаблона к использованию, перейдите к странице **Devices** (Устройства) в приложении IoT Central. В этом разделе **Devices** (Устройства) отображается список устройств, опубликованных в приложении.

![Опубликованные шаблоны на странице устройств](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении Azure IoT Central добавьте реальное устройство в шаблон приложения, который вы создали в предыдущем разделе.

1. На странице **Devices** (Устройства) выберите шаблон устройства **Environmental sensor** (Датчик окружающей среды).

1. Выберите **+ Создать**.

1. Убедитесь, что для параметра **Simulated** (Имитация) установлено значение **Off** (Отключено). Щелкните **Создать**.

Щелкните имя устройства, а затем выберите **Connect** (Подключить). Запишите значения параметров на странице **Device Connection** (Устройство —подключение): **ID scope** (Область идентификатора), **Device ID** (Идентификатор устройства) и **Primary key** (Первичный ключ). Эти значения понадобятся при создании кода устройства.

![Сведения о подключении устройства.](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Создание приложения Node.js

Ниже показано, как создать клиентское приложение Node.js, которое реализует реальное устройство, добавленное в приложение. Это приложение Node.js моделирует поведение реального устройства.

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

1. Для отправки данных телеметрии в приложение Azure IoT Central необходимо добавить в файл следующую функцию:

    ```javascript
    // Send device measurements.
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

1. Для отправки свойств устройства в приложение Azure IoT Central необходимо добавить в файл следующую функцию:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Чтобы определить и использовать доступные для записи параметры, на которые реагирует устройство, добавьте следующий фрагмент кода:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
      }, 5000);
      }
    };

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Добавьте следующий код для обработки команд, отправляемых из приложения IoT Central:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
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

    // Handle LED turn off command
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

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
    }
    ```

1. Добавьте следующий код, чтобы завершить подключение к Azure IoT Central, и присоедините функции в клиентском коде:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
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

![Выполнение клиентского приложения](media/tutorial-connect-device/run-application.png)

Оператор приложения Azure IoT Central может выполнять следующие действия:

* Данные телеметрии, переданные устройством, можно просмотреть на странице **Overview** (Обзор).

    ![Просмотр телеметрии](media/tutorial-connect-device/view-telemetry.png)

* Измените значения доступных для записи свойств на странице **Properties** (Свойства).

    ![Обновление свойств](media/tutorial-connect-device/update-properties.png)

    ![Обновление свойств, представление устройства](media/tutorial-connect-device/update-properties-device.png)

* Выполните команды на странице **Команды**.

    ![Вызов команды blink](media/tutorial-connect-device/call-command.png)

    ![Вызов команды blink, представление устройства](media/tutorial-connect-device/call-command-device.png)

* На странице **About** (Сведения) можно просмотреть свойства устройства.

    ![Просмотреть свойства](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о модели возможностей устройства и о том, как создавать шаблоны устройств, перейдите к следующему практическому руководству:

> [!div class="nextstepaction"]
> [Определение типа нового устройства Интернета вещей в приложении Azure IoT Central](./howto-set-up-template.md)
