---
title: Руководство по добавлению реального устройства в приложение Azure IoT Central
description: Руководство по Сведения о том, как добавить реальное устройство в приложение Azure IoT Central и настроить его. Также вы узнаете, как создавать код в реальном устройстве.
author: sandeeppujar
ms.author: sandeepu
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5234c371cc4a7238148915f4dcca1faac564f608
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108975"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Руководство по Добавление реального устройства в приложение Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

В этом руководстве показано, как добавить *реальное устройство* в приложение Microsoft Azure IoT Central и настроить его. В этом руководстве вы создадите реальное устройство с помощью Node.js и выполните его код на настольном компьютере. Для работы с этим руководством не требуется отдельное устройство IoT, такое как Raspberry Pi или устройство MXChip IoT DevKit.

Это руководство состоит из двух частей:

* Во-первых, вы, как оператор, добавите реальное устройство в приложение Azure IoT Central, а затем настроите его. В конце этой части вы извлечете строку подключения для использования во второй части.
* Затем, как разработчик устройства, вы ознакомитесь со сведениями о коде в вашем реальном устройстве. Вы добавите строку подключения из первой части в пример кода.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * добавление нового реального устройства;
> * настройка реального устройства;
> * получение строки подключения для реального устройства из приложения;
> * Сопоставление клиентского кода с приложением.
> * Настройка клиентского кода для реального устройства

## <a name="prerequisites"></a>Предварительные требования

Прежде чем вы начнете, конструктор должен выполнить инструкции по крайней мере в первом руководстве по созданию приложения Azure IoT Central: [Определение типа нового устройства в приложении в Azure IoT Central](tutorial-define-device-type.md) (обязательно).

Установите на компьютере для разработки [Node.js](https://nodejs.org/) версии 8.0.0 или более поздней. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. Node.js доступен для разных операционных систем.

## <a name="add-a-real-device"></a>Добавление реального устройства

Чтобы добавить реальное устройство в приложение, используете шаблон устройства **подключенного кондиционера**, который вы создали в руководстве по [определению типа нового устройства](tutorial-define-device-type.md).

1. Чтобы добавить новое устройство, оператору нужно выбрать элемент **Устройства** на панели слева.

   ![Шаблон подключенного кондиционера на странице Device Explorer](media/tutorial-add-device/explorer.png)

   В **Device Explorer** отображается шаблон устройства **Подключенный кондиционер** и имитированное устройство. IoT Central автоматически создает имитированное устройство во время создания шаблона устройства.

2. Обратите внимание, что в **Обозревателе устройств** выбран шаблон устройства **Connected Air Conditioner** (Подключенный кондиционер). Чтобы приступить к подключению реального устройства кондиционирования воздуха, использующего этот шаблон, выберите **+** , а затем **Real**:

   ![Первые шаги добавления нового реального подключенного кондиционера](media/tutorial-add-device/newreal.png)

3. Введите собственный **Идентификатор устройства** (должен быть строчным) или используйте предлагаемое значение. Также можно ввести **Имя устройства** для нового устройства и нажать кнопку **Create** (Создать).

   ![Переименование устройства](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Настройка реального устройства

Реальное устройство создается из шаблона устройства **Подключенный кондиционер**. Вы можете использовать раздел **Settings** (Параметры), чтобы настроить устройство и задать значения свойств для записи сведений о нем.

1. Обратите внимание, что на странице **Параметры** в качестве состояния параметра **Set Temperature** (Установить температуру) указано **no update** (не обновлен). Он остается в таком состоянии до тех пор, пока реальное устройство не подключится к приложению и не подтвердит, что использует этот параметр.

    ![Синхронизация параметров](media/tutorial-add-device/settingssyncing.png)

2. На странице **Properties** (Свойства) нового реального устройства можно изменить свойства для расположения службы и даты последнего обслуживания. Поля серийного номера и версии встроенного ПО пустые. Они будет заполнены, когда устройство подключится к приложению. Эти значения, доступные только для чтения, отправляются из устройства, и их нельзя изменить.

    ![Свойства реального устройства](media/tutorial-add-device/setproperties1.png)

3. Вы можете просматривать следующие страницы параметров своего реального устройства: **Measurements** (Измерения), **Rules** (Правила) и **Dashboard** (Панель мониторинга).

## <a name="prepare-the-client-code"></a>Подготовка клиентского кода

Пример кода в этой статье написан на [Node.js](https://nodejs.org/). Этот код устройства используется для выполнения следующих задач:

* подключение к приложению Azure IoT Central;
* отправка данных телеметрии температуры из подключенного кондиционера;
* отправка свойств устройства в приложение Azure IoT Central;
* отправка ответа оператору, который использует параметр **Set Temperature** (Установить температуру).
* обработка команды Echo от приложения Azure IoT Central.

В статьях, указанных в разделе [Следующие шаги](#next-steps), содержатся более полные примеры и сведения о других языках. Дополнительные сведения см. в статье [Device connectivity in Azure IoT Central](concepts-connectivity.md) (Подключение устройств в Azure IoT Central).

В следующих шагах показано, как подготовить пример [Node.js](https://nodejs.org/):

### <a name="get-the-device-connection-information"></a>Получение сведений о подключении к устройству

1. Срока подключения для экземпляра устройства в приложении создается на основе сведений об устройстве, предоставляемых IoT Central.

   На экране устройства для подключенного кондиционера выберите **Connect** (Подключить).

   ![Страница устройства, отображающая ссылку на сведения о подключении](media/tutorial-add-device/connectionlink.png)

1. Запишите значения **Идентификатор области**, **Идентификатор устройства** и **Первичный ключ** на странице **Подключение к устройству**. Они понадобятся позже.

   ![Сведения о подключении](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Подготовка проекта Node.js

1. Создайте папку с именем `connectedairconditioner` на компьютере для разработки.

1. Перейдите к созданной папке `connectedairconditioner` в среде командной строки.

1. Чтобы инициализировать проект Node.js, выполните следующую команду, приняв все значения по умолчанию:

    ```cmd/sh
    npm init
      ```

1. Чтобы установить требуемые пакеты, выполните следующую команду:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. С помощью текстового редактора создайте файл **ConnectedAirConditioner.js** в папке `connectedairconditioner`.

1. Добавьте следующие инструкции `require` в начало файла **ConnectedAirConditioner.js**:

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Добавьте следующие объявления переменных в файл. Замените заполнители `{your Scope ID}`, `{your Device ID}` и `{your Primary Key}` записанными ранее сведениями о подключении к устройству.

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. Сохраните внесенные изменения, но не закрывайте файл.

## <a name="review-client-code"></a>Просмотр кода клиента

В предыдущем разделе вы создали основу проекта Node.js для приложения устройства, которое подключается к приложению Azure IoT Central. Далее нужно добавить код для выполнения таких задач:

* подключение к приложению Azure IoT Central;
* отправка данных телеметрии в приложение Azure IoT Central;
* отправка свойств устройства в приложение Azure IoT Central;
* получение параметров из приложения Azure IoT Central.
* обработка команды Echo от приложения Azure IoT Central.

1. Чтобы отправить данные телеметрии температуры в приложение Azure IoT Central, добавьте в файл **ConnectedAirConditioner.js** следующий код:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Имя поля в отправляемом файле JSON должно совпадать с именем поля, которое вы указали для данных телеметрии температуры в шаблоне устройства. В этом примере поле называется **temperature**.

1. Чтобы отправить свойства устройства, например **firmwareVersion** и **serialNumber**, добавьте следующее определение:

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) =>
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. Чтобы определить параметры, поддерживаемые вашим устройством, например **setTemperature**, добавьте следующее определение:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

1. Чтобы обрабатывать параметры, отправленные из Azure IoT Central, добавьте следующую функцию, которая определяет и выполняет соответствующий код устройства:

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

    Эта функция выполняет следующее:

    * наблюдает за отправкой требуемого свойства из Azure IoT Central;
    * находит соответствующую функцию, которую нужно вызвать для обработки изменения параметра;
    * возвращает подтверждение в приложение Azure IoT Central.

1. Чтобы ответить на команду, например **echo**, от приложения Azure IoT Central, добавьте следующее определение:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
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
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };
    ```

1. Зарегистрируйте и подключите устройство к приложению IoT Central.

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. Сохраните внесенные изменения.

## <a name="run-the-client-code"></a>Запуск клиентского кода

Теперь можно запустить клиентский код и посмотреть, как он взаимодействует с приложением IoT Central.

1. Чтобы запустить пример, в среде командной строки введите следующую команду:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > При запуске этой команды убедитесь, что вы находитесь в папке `connectedairconditioner`.

1. Приложение выводит выходные данные в консоли:

   ![Выходные данные клиентского приложения](media/tutorial-add-device/output.png)

1. Примерно через 30 секунд вы увидите данные телеметрии на странице **Measurements** (Измерения) устройства:

   ![Реальные данные телеметрии](media/tutorial-add-device/realtelemetry.png)

1. Вы можете увидеть на странице **Параметры**, что теперь параметр синхронизирован. При первом подключении устройство получило значение параметра и подтвердило изменение:

   ![Синхронизированный параметр](media/tutorial-add-device/settingsynced.png)

1. На странице **Параметры** для температуры устройства задайте значение **95** и выберите **Update device** (Обновить устройство). Ваш пример приложения получает и обрабатывает это изменение:

   ![Прием и обработка параметра](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Существует два вида сообщения об обновлении параметра. Одно отображается при отправке состояния `pending`, а другое — при отправке состояния `completed`.

1. На странице **Measurements** (Измерения) можно увидеть, что устройство отправляет более высокие значения температуры:

    ![Значения данных телеметрии температуры теперь выше](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="nextstepaction"]
> * добавление нового реального устройства;
> * Настройка нового устройства.
> * получение строки подключения для реального устройства из приложения;
> * Сопоставление клиентского кода с приложением.
> * Настройка клиентского кода для реального устройства

Теперь, когда вы подключили реальное устройство к приложению Azure IoT Central, вы можете выполнить некоторые из следующих действий.

Будучи оператором, вы можете научиться, как:

* [Управление устройствами](howto-manage-devices.md)
* [использовать наборы устройств](howto-use-device-sets.md);
* [создавать настраиваемую аналитику](howto-use-device-sets.md).

Как разработчик устройств вы можете научиться, как:

* [подключить устройство MXChip IoT DevKit к приложению Azure IoT Central](howto-connect-devkit.md);
* [подключить устройство Raspberry Pi к приложению Azure IoT Central (Python)](howto-connect-raspberry-pi-python.md);
* [подключить устройство Raspberry Pi к приложению Azure IoT Central (C#)](howto-connect-raspberry-pi-csharp.md);
* [подключить устройство Windows 10 IoT Core к приложению Azure IoT Central](howto-connect-windowsiotcore.md);
* [Подключение универсального клиентского приложения к приложению Azure IoT Central (Node.js)](howto-connect-nodejs.md)
