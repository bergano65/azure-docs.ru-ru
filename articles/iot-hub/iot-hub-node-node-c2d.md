---
title: Сообщения облака на устройство с Центра Интернета вещей Azure | Документации Майкрософт
description: Как отправлять сообщения из облака на устройство из Центра Интернета вещей Azure, используя пакеты SDK Интернета вещей Azure для Node.js. Для получения сообщений, отправляемых из облака на устройство, следует изменить приложение имитации устройства, а для отправки таких сообщений следует изменить внутреннее приложение.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: b1aa8f2ce7d271187657d57993032069639ca9c7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404097"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Отправка сообщений из облака на устройства с помощью Центра Интернета вещей (Node)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Введение

Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. В кратком руководстве [Отправка данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-node.md) показано, как создать центр Интернета вещей, подготавливать в нем удостоверение устройства и создать код для приложения имитации устройства, которое отправляет сообщения, отправляемые с устройства в облако.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Это руководство строится на [отправке данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-node.md). В нем показано следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.
* Получение на устройстве сообщений, передаваемых из облака на устройство.
* В серверной части решения запросите подтверждение доставки (*обратная связь*) для сообщений, отправляемых на устройство из центра Интернета вещей.

Дополнительные сведения о сообщениях, отправляемых из облака на устройство, см. в разделе с [руководством разработчика для центра Интернета вещей](iot-hub-devguide-messaging.md).

По завершении работы с этим руководством запустите два консольных приложения Node.js:

* **SimulatedDevice**— измененная версия приложения, созданная при [отправке данных телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-node.md), который подключается к центру Интернета вещей и получает сообщения из облака на устройство.

* **SendCloudToDeviceMessage**, который отправляет сообщение из облака на устройство в приложение имитации устройства через центр Интернета вещей, а затем получает подтверждение доставки.

> [!NOTE]
> В Центре Интернета вещей реализована поддержка для пакетов SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые указания по связыванию устройства с кодом из этого руководства, а также по подключению к Центру Интернета вещей Azure см. в [центре разработчиков для Интернета вещей Azure](https://azure.microsoft.com/develop/iot).
>

Для работы с этим учебником требуется:

* Node. js версии 10.0. x или более поздней.
* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial) всего за несколько минут.

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства

В этом разделе вы измените приложение имитации устройства, созданное при отправке данных [телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-node.md) для получения сообщений из облака на устройство из центра Интернета вещей.

1. В текстовом редакторе откройте файл SimulatedDevice.js.

2. Измените функцию **connectCallback** для обработки сообщений, отправленных из Центра Интернета вещей. В этом примере устройство всегда вызывает функцию **complete** для уведомления Центра Интернета вещей о том, что сообщение обработано. Новая версия функции **connectCallback** выглядит как следующий фрагмент кода.

    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
  
   > [!NOTE]
   > Если в качестве транспорта вместо MQTT или AMQP используется HTTPS, то экземпляр **DeviceClient** редко проверяет наличие сообщений от Центра Интернета вещей (реже чем каждые 25 минут). Дополнительные сведения о различиях между MQTT, AMQP и поддержкой HTTPS и регулированием центра Интернета вещей см. в разделе [руководств разработчика для центра Интернета вещей](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

В этой статье вы создадите серверную службу для отправки сообщений из облака на устройство через центр Интернета вещей, созданный при отправке данных [телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-node.md). Для отправки сообщений из облака на устройство службе требуется разрешение на **Подключение к службе** . По умолчанию каждый центр Интернета вещей создается с помощью политики общего доступа с именем **Service** , предоставляющей это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство

В этом разделе создается консольное приложение Node.js, которое отправляет сообщения, передаваемые из облака на устройство, в приложение имитации устройства. Вам потребуется идентификатор устройства, добавленного в краткое руководство по отправке данных [телеметрии с устройства в центр Интернета вещей](quickstart-send-telemetry-node.md) . Кроме того, вам потребуется строка подключения центра Интернета вещей, скопированная ранее в [поле получение строки подключения для центра Интернета вещей](#get-the-iot-hub-connection-string).

1. Создайте пустую папку **sendcloudtodevicemessage**. В папке **sendcloudtodevicemessage** создайте файл package.json, введя в командной строке следующую команду. Примите значения по умолчанию:

    ```shell
    npm init
    ```

2. В командной строке в папке **sendcloudtodevicemessage** выполните следующую команду, чтобы установить пакет **azure-iothub**.

    ```shell
    npm install azure-iothub --save
    ```

3. В текстовом редакторе создайте файл **SendCloudToDeviceMessage.js** в папке **sendcloudtodevicemessage**.

4. Добавьте следующие операторы `require` в начало файла **SendCloudToDeviceMessage.js** .

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Добавьте в файл **SendCloudToDeviceMessage.js** следующий код. Замените значения заполнителей {IOT в центре Интернета вещей} и {Device ID} на строку подключения центра Интернета вещей и идентификатор устройства, записанные ранее:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Добавьте следующую функцию для вывода результатов операции в консоль.

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Добавьте следующую функцию для вывода сообщений о доставке в консоль.

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Добавьте приведенный ниже код для отправки сообщения на устройство и обработки сообщения о доставке, получаемого после подтверждения устройством получения сообщения из облака на устройство.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Сохраните и закройте файл **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Запуск приложений

Теперь все готово к запуску приложений.

1. В командной строке в папке **simulateddevice** выполните следующую команду, чтобы начать отправку данных телеметрии в Центр Интернета вещей и прослушивание сообщений из облака на устройства:

    ```shell
    node SimulatedDevice.js
    ```

    ![Запуск приложения виртуального устройства](./media/iot-hub-node-node-c2d/receivec2d.png)

2. В командной строке в папке **sendcloudtodevicemessage** выполните следующую команду, чтобы отправить сообщение из облака на устройство и ожидать подтверждения доставки:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Запуск приложения для отправки команды из облака на устройство](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Для простоты в этом руководстве не реализована политика повтора. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Следующие шаги

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по акселератору решения Azure IoT для удаленного мониторинга](https://azure.microsoft.com/documentation/suites/iot-suite/).

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide.md).
