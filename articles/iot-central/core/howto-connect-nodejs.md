---
title: Подключение универсального клиентского приложения Node. js к Azure IoT Central | Документация Майкрософт
description: Как разработчик устройств, как подключить универсальное устройство Node. js к приложению Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 09/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1ddb947e9bcbdde655b5c83cffb67e8fc13d9d0e
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048675"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Подключение универсального клиентского приложения к приложению Azure IoT Central (Node.js)

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

В этой статье описано, каким образом разработчик устройства может подключить универсальное устройство Node.js, представляющее собой реальное устройство, к приложению Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- Приложение Azure IoT Central. Дополнительные сведения см. в статье [Создание приложения Azure IoT Central](quick-deploy-iot-central.md).
- Компьютер для разработки с установленным [Node.js](https://nodejs.org/) 4.0.0 или более поздней версии. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. Node.js доступен для разных операционных систем.

## <a name="create-a-device-template"></a>Создание шаблона устройства

В приложении IoT Central Azure вам понадобится шаблон устройства со следующими измерениями, свойствами, параметрами и командами устройства.

Дополнительные сведения о допустимых именах свойств см. в разделе [Формат тегов и свойств](../../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

### <a name="telemetry-measurements"></a>Измерения телеметрии

Добавьте следующие данные телеметрии на странице **измерения** :

| Отображаемое имя | Имя поля  | Units | Min | Max | Число десятичных знаков |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| температура;  | Температура | F     | 60  | 110 | 0              |
| влажность.     | Влажность    | %     | 0   | 100 | 0              |
| давление;     | pressure    | кПа   | 80  | 110 | 0              |

> [!NOTE]
> Данные телеметрии передаются в формате чисел с плавающей запятой.

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают с именами свойств в соответствующем коде устройства, данные телеметрии невозможно будет отобразить в приложении.

### <a name="state-measurements"></a>Измерения состояния

Добавьте следующее состояние на странице **измерения** :

| Отображаемое имя | Имя поля  | Значение 1 | Отображаемое имя | Значение 2 | Отображаемое имя |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Режим вентилятора     | fanmode     | 1       | Выполняется      | 0       | Остановка      |

> [!NOTE]
> Для измерения состояния используется строковый тип данных.

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают с именами свойств в соответствующем коде устройства, состояние не может быть отображено в приложении.

### <a name="event-measurements"></a>Измерения событий

Добавьте следующее событие на странице **измерения** :

| Отображаемое имя | Имя поля  | Уровень серьезности |
| ------------ | ----------- | -------- |
| Перегрев  | overheat    | Ошибка    |

> [!NOTE]
> Для измерения событий используется строковый тип данных.

### <a name="location-measurements"></a>Измерения расположения

На странице **измерения** добавьте следующее измерение расположения:

| Отображаемое имя | Имя поля  |
| ------------ | ----------- |
| Место проведения     | location    |

Тип данных измерения Location состоит из двух чисел с плавающей запятой для долготы и широты и необязательного числа с плавающей запятой для высоты.

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают с именами свойств в соответствующем коде устройства, расположение не может быть отображено в приложении.

### <a name="device-properties"></a>Свойства устройства

Добавьте следующие свойства устройства на странице **Свойства** .

| Отображаемое имя        | Имя поля        | Тип данных |
| ------------------- | ----------------- | --------- |
| Серийный номер       | SerialNumber      | text      |
| Производитель устройства | manufacturer      | text      |

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают с именами свойств в соответствующем коде устройства, свойства не могут быть отображены в приложении.

### <a name="settings"></a>данных

Добавьте следующие **числовые** параметры на странице **Параметры** :

| Отображаемое имя    | Имя поля     | Units | Десятичные знаки | Min | Max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Скорость вращения вентилятора       | fanSpeed       | Об/мин   | 0        | 0   | 3000 | 0       |
| Установить температуру | setTemperature | F     | 0        | 20  | 200  | 80      |

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают с именами свойств в соответствующем коде устройства, устройство не сможет получить значение параметра.

### <a name="commands"></a>Команды

На странице **команды** добавьте следующую команду:

| Отображаемое имя    | Имя поля     | Default Timeout (Время ожидания по умолчанию) | Тип данных |
| --------------- | -------------- | --------------- | --------- |
| Оставшего       | Оставшего      | 30              | number    |

Добавьте следующее поле ввода в команду обратного отсчета:

| Отображаемое имя    | Имя поля     | Тип данных | Значение |
| --------------- | -------------- | --------- | ----- |
| Число из      | каунтфром      | number    | 10    |

Введите имена полей точно так же, как показано в таблицах в шаблоне устройства. Если имена полей не совпадают с именами свойств в соответствующем коде устройства, устройство не сможет обработать команду.

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении IoT Central Azure добавьте реальное устройство в шаблон устройства, созданный в предыдущем разделе.

Запишите сведения о подключении устройства на странице **подключения устройства** : **идентификатор области**, **идентификатор устройства**и **первичный ключ**. Эти значения можно добавить в код устройства далее в этом пошаговом руководство.

![Сведения о подключении устройства](./media/howto-connect-nodejs/device-connection.png)

### <a name="create-a-nodejs-application"></a>Создание приложения Node.js

Ниже показано, как создать клиентское приложение, реализующее реальное устройство, добавленное в приложение. Здесь приложение Node.js представляет реальное устройство.

1. Создайте на компьютере папку с именем `connected-air-conditioner-adv`. Перейдите к этой папке в среде командной строки.

1. Чтобы инициализировать проект Node.js, выполните следующие команды:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Создайте файл **ConnectedAirConditioner.js** в папке `connected-air-conditioner-adv`.

1. Добавьте следующие инструкции `require` в начало файла **connectedAirConditionerAdv.js**:

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
    var locLong = -122.1215;
    var locLat = 47.6740;
    ```

    Обновите заполнители `{your Scope ID}`, `{your Device ID}`и `{your Primary Key}` значениями, которые были сделаны ранее. В этом примере вы инициализируем `targetTemperature` нулем. Вы можете использовать текущее чтение с устройства или значение из двойникаа устройства.

1. Чтобы отправить измерения телеметрии, состояния, события и расположения в приложение IoT Central Azure, добавьте в файл следующую функцию:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
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

1. Чтобы определить параметры, на которые реагирует устройство, добавьте следующее определение:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Чтобы обработать обновленные параметры в приложении Azure IoT Central, добавьте следующий файл:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
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
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Добавьте следующий код, обрабатывающий команду обратного отсчета, отправленную из приложения IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');
    
      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;
    
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          hubClient.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }
    
            doCountdown();
          });
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

        // Create handler for countdown command
        hubClient.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
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

В среде командной строки выполните следующую команду:

```cmd/sh
node connectedAirConditionerAdv.js
```

Будучи оператором в приложении Azure IoT Central для реального устройства вы можете делать следующее:

* Просматривать данные телеметрии на странице **Measurements** (Измерения).

    ![Просмотр телеметрии](media/howto-connect-nodejs/viewtelemetry.png)

* Просмотрите расположение на странице **измерения** :

    ![Просмотр измерений расположения](media/howto-connect-nodejs/viewlocation.png)

* Просматривать значения свойств устройства, отправленные из устройства, на странице **Свойства**. Плитки свойств устройства обновляются при подключении устройства:

    ![Просмотр свойств устройства](media/howto-connect-nodejs/viewproperties.png)

* Задайте скорость вращения вентилятора и целевую температуру на странице **Параметры** .

    ![Установка скорости вращения вентилятора](media/howto-connect-nodejs/setfanspeed.png)

* Вызовите команду обратного вызова на странице **команды** :

    ![Команда обратного вызова](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как подключить универсальный клиент Node. js к приложению IoT Central Azure, предлагаем следующий шаг: Узнайте, как [настроить настраиваемый шаблон устройства](howto-set-up-template.md) для вашего устройства IOT.
