---
title: Подключение универсального клиентского приложения Node.js к Azure IoT Central | Документация Майкрософт
description: Как разработчик устройств как подключиться к Azure IoT Central приложения универсальные устройства Node.js.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5497e4956fbdc74eced302867c33a66d07d6a184
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617943"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Подключение универсального клиентского приложения к приложению Azure IoT Central (Node.js)

В этой статье описано, каким образом разработчик устройства может подключить универсальное устройство Node.js, представляющее собой реальное устройство, к приложению Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

1. Приложение Azure IoT Central. Дополнительные сведения см. в [кратком руководстве по созданию приложения](quick-deploy-iot-central.md).
1. Компьютер для разработки с установленным [Node.js](https://nodejs.org/) 4.0.0 или более поздней версии. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. Node.js доступен для разных операционных систем.

## <a name="create-a-device-template"></a>Создание шаблона устройства

В приложении Azure IoT Central вам потребуется шаблон устройства с помощью измерений, свойства устройства, параметры и команды:

### <a name="telemetry-measurements"></a>Измерения телеметрии

Добавьте следующие данные телеметрии на **измерения** страницы:

| Отображаемое имя | Имя поля  | Units | Min | Max | Число десятичных знаков |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| температура;  | Температура | F     | 60  | 110 | 0              |
| влажность.     | Влажность    | %     | 0   | 100 | 0              |
| Давление     | pressure    | кПа   | 80  | 110 | 0              |

> [!NOTE]
> Данные телеметрии передаются в формате чисел с плавающей запятой.

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают имена свойств в соответствующий код устройства, невозможно отобразить данные телеметрии в приложении.

### <a name="state-measurements"></a>Измерения состояния

Добавьте следующее состояние на **измерения** страницы:

| Отображаемое имя | Имя поля  | Значение 1 | Отображаемое имя | Значение 2 | Отображаемое имя |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Режим вентилятора     | fanmode     | 1       | Выполнение      | 0       | Остановлено      |

> [!NOTE]
> Для измерения состояния используется строковый тип данных.

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают имена свойств в соответствующий код устройства, состояние невозможно отобразить в приложении.

### <a name="event-measurements"></a>Измерения событий

Добавьте следующее событие на **измерения** страницы:

| Отображаемое имя | Имя поля  | Уровень серьезности |
| ------------ | ----------- | -------- |
| Перегрев  | overheat    | Ошибка    |

> [!NOTE]
> Для измерения событий используется строковый тип данных.

### <a name="device-properties"></a>Свойства устройства

Добавьте следующие свойства устройства на **свойства** страницы:

| Отображаемое имя        | Имя поля        | Тип данных |
| ------------------- | ----------------- | --------- |
| Серийный номер       | SerialNumber      | текст      |
| Производитель устройства | manufacturer      | текст      |

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают имена свойств в соответствующий код устройства, свойства невозможно отобразить в приложении.

### <a name="settings"></a>Параметры

Добавьте следующий **номер** параметры на **параметры** страницы:

| Отображаемое имя    | Имя поля     | Units | Десятичные знаки | Min | Max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Скорость вращения вентилятора       | fanSpeed       | Об/мин   | 0        | 0   | 3000 | 0       |
| Установить температуру | setTemperature | F     | 0        | 20  | 200  | 80      |

Введите имена полей из таблицы в шаблон устройства. Если имена полей не совпадают имена свойств в соответствующий код устройства, устройство не может принимать значение параметра.

### <a name="commands"></a>Команды

Добавьте следующую команду на **команды** страницы:

| Отображаемое имя    | Имя поля     | Default Timeout (Время ожидания по умолчанию) | Тип данных |
| --------------- | -------------- | --------------- | --------- |
| Обратный отсчет       | Обратный отсчет      | 30              | number    |

Добавьте следующее поле ввода обратного отсчета команды:

| Отображаемое имя    | Имя поля     | Тип данных | Значение |
| --------------- | -------------- | --------- | ----- |
| Отсчет от      | countFrom      | number    | 10    |

Введите имена полей именно так, как показано в таблицах в шаблон устройства. Если имена полей не совпадают имена свойств в соответствующий код устройства, устройство не может обработать команду.

## <a name="add-a-real-device"></a>Добавление реального устройства

В приложении Azure IoT Central добавьте реального устройства шаблон устройства, созданный в предыдущем разделе.

Затем выполните инструкции из учебника «Добавить устройство», чтобы [создать строку подключения для реального устройства](tutorial-add-device.md#generate-connection-string). Использовать эту строку подключения в следующем разделе:

### <a name="create-a-nodejs-application"></a>Создание приложения Node.js

Ниже показано, как создать клиентское приложение, реализующее реальное устройство, добавленное в приложение. Здесь приложение Node.js представляет реальное устройство. 

1. Создайте на компьютере папку с именем `connected-air-conditioner-adv`. Перейдите к этой папке в среде командной строки.

1. Чтобы инициализировать проект Node.js, выполните следующие команды:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Создайте файл **ConnectedAirConditioner.js** в папке `connected-air-conditioner-adv`.

1. Добавьте следующие инструкции `require` в начало файла **connectedAirConditionerAdv.js**:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Добавьте следующие объявления переменных в файл:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Обновить заполнитель `{your device connection string}` с [строки подключения устройства](tutorial-add-device.md#generate-connection-string). В этом примере инициализируется `targetTemperature` до нуля, можно использовать значение из "двойника" устройства или текущего чтения с устройства.

1. Чтобы отправить данные телеметрии, состояние и измерения событий приложение Azure IoT Central, добавьте следующую функцию в файл:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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

1. Добавьте следующий код для обработки обратного отсчета команды, отправленные из приложения IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
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
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
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

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Запуск приложения Node.js

В среде командной строки выполните следующую команду:

```cmd/sh
node connectedAirConditionerAdv.js
```

Будучи оператором в приложении Azure IoT Central для реального устройства вы можете делать следующее:

* Просматривать данные телеметрии на странице **Measurements** (Измерения).

    ![Просмотр телеметрии](media/howto-connect-nodejs/viewtelemetry.png)

* Просматривать значения свойств устройства, отправленные из устройства, на странице **Свойства**. Обновление плитки устройства-свойства при подключении устройства:

    ![Просмотр свойств устройства](media/howto-connect-nodejs/viewproperties.png)

* Задайте вентилятор скорость и целевой температуры с **параметры** страницы:

    ![Установка скорости вращения вентилятора](media/howto-connect-nodejs/setfanspeed.png)

* Вызывает команду обратного отсчета от **команды** страницы:

    ![Вызовите команду обратного отсчета](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как соединиться с универсального клиента Node.js приложение Azure IoT Central, мы предлагаем — Узнайте, как [настроить шаблон пользовательского устройства](howto-set-up-template.md) для устройства Интернета вещей.
