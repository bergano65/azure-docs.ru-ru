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
ms.openlocfilehash: 8071ddbc5f6073598daf0a08d359ccd19ccd1e4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110804"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Отправка сообщений об лабонента с помощью Концентратора IoT (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Центр Интернета вещей Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств и серверной частью решения. [Телеметрия отправки с устройства на быстрый запуск концентратора IoT](quickstart-send-telemetry-node.md) показывает, как создать концентратор IoT, предоставить в нем идентификацию устройства и кодировать смоделированное приложение устройства, которое отправляет сообщения от устройства к облаку.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Этот учебник основан на [отправке телеметрии с устройства на концентратор IoT.](quickstart-send-telemetry-node.md) В нем показано следующее:

* Отправка сообщений, передаваемых из облака на устройство, из серверной части решения на одно устройство через Центр Интернета вещей.
* Получение на устройстве сообщений, передаваемых из облака на устройство.
* От задней части решения — запрос подтверждения доставки *(обратной связи)* для сообщений, отправленных на устройство из Концентратора IoT.

Дополнительные сведения о сообщениях, отправляемых из облака на устройство, см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide-messaging.md).

По завершении работы с этим руководством запустите два консольных приложения Node.js:

* **SimulatedDevice**, модифицированная версия приложения, созданного в [телеметрии Отправки с устройства на концентратор IoT,](quickstart-send-telemetry-node.md)который подключается к концентратору IoT и получает сообщения от облака к устройству.

* **SendCloudToDeviceMessage**, который отправляет сообщение об облаке к устройству в приложение для имитации устройства через IoT Концентратор, а затем получает подтверждение его доставки.

> [!NOTE]
> IoT Концентратор имеет поддержку SDK для многих платформ и языков устройств (включая C, Java, Python и Javascript) через SDK-устройства Azure IoT. Пошаговые указания по связыванию устройства с кодом из этого руководства, а также по подключению к Центру Интернета вещей Azure см. в [центре разработчиков для Интернета вещей Azure](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Предварительные требования

* Версия node.js 10.0.x или позже. В статье [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Подготовка среды разработки) описывается, как установить Node.js для работы с этим учебником в ОС Windows или Linux.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial) всего за несколько минут.

* Убедитесь, что в брандмауэре открыт порт 8883. Образец устройства в этой статье использует протокол МЗТТ, который общается по порту 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Получение сообщений в приложении для имитации устройства

В этом разделе вы модифицируете приложение с моделируемого устройства, созданное в [телеметрии Отправки с устройства на концентратор IoT,](quickstart-send-telemetry-node.md) для получения сообщений от облака к устройству из концентратора IoT.

1. Используя текстовый редактор, откройте файл **SimulatedDevice.js.** Этот файл находится в папке **iot-hub'quickstarts-simulated-device** от корневой папки кода образца Node.js, загруженного в [телеметрии Отправки с устройства на быстрый запуск концентратора IoT.](quickstart-send-telemetry-node.md)

2. Зарегистрируйте обработчик с клиентом устройства для получения сообщений, отправленных из Концентратора IoT. Добавьте вызов `client.on` сразу после строки, которая создает клиент устройства, как в следующем фрагменте:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    В этом примере устройство ссылается на **полную** функцию уведомления IoT Hub о том, что оно обработало сообщение. Вызов для **завершения** не требуется, если вы используете транспорт МЗТТ и может быть опущен. Он необходим для HTTPS и АМЗП.
  
   > [!NOTE]
   > Если в качестве транспорта вместо MQTT или AMQP используется HTTPS, то экземпляр **DeviceClient** редко проверяет наличие сообщений от Центра Интернета вещей (реже чем каждые 25 минут). Дополнительные сведения о различиях между поддержкой MQTT, AMQP и HTTPS, а также регулировании Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

В этой статье создается бэкэнд-сервис для отправки сообщений от облака к устройству через созданный концентратор IoT, созданный в [телеметрии Отправки с устройства на концентратор IoT.](quickstart-send-telemetry-node.md) Для отправки сообщений об облаке и устройстве службе требуется разрешение **на подключение службы.** По умолчанию каждый концентратор IoT создается с помощью службы общего доступа, названной **службой,** которая предоставляет это разрешение.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Отправка сообщения из облака на устройство

В этом разделе создается консольное приложение Node.js, которое отправляет сообщения, передаваемые из облака на устройство, в приложение имитации устройства. Вам нужен идентификатор устройства, добавленного в [телеметрию Отправки с устройства на быстрый запуск концентратора IoT.](quickstart-send-telemetry-node.md) Вам также нужна строка подключения концентратора IoT, которая была скопирована ранее в [строке подключения концентратора IoT.](#get-the-iot-hub-connection-string)

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

5. Добавьте в файл **SendCloudToDeviceMessage.js** следующий код. Замените значения заполнителя «строка подключения концентратора» и «устройство id» строкой заполнителя строкой концентратора IoT и идентификатором устройства, который вы отметили ранее:

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

1. В запросе команды в папке **с моделируемым устройством** запустите следующую команду для отправки телеметрии в концентратор IoT и прослушивания сообщений от облака к устройству:

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
   > Для простоты, этот учебник не реализует какой-либо политики повторной попытки. В рабочем коде следует реализовать политики повторных попыток (например, с экспоненциальной задержкой), как указано в статье [Обработка временных сбоев](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы научились отправлять и получать сообщения с облака на устройство.

Примеры комплексных решений, в которых используется Центр Интернета вещей, см. в [документации по акселератору решения Azure IoT для удаленного мониторинга](https://azure.microsoft.com/documentation/suites/iot-suite/).

Дополнительные сведения о разработке решений с помощью Центра Интернета вещей см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide.md).
