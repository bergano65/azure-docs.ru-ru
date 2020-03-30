---
title: Начните с идентификатора модуля Azure IoT Hub & модуля twin (Node.js)
description: Сведения о создании удостоверения модуля и обновлении двойника модуля с помощью пакетов SDK для Центра Интернета вещей для Node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: bf80925a2dc5c6d06ba14fe1c0d72a2fc1f88d51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953872"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Начало работы с IoT концентратора модуль идентичности и модуля twin (Node.js)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Удостоверение и двойник модуля](iot-hub-devguide-module-twins.md) аналогичны удостоверению и двойнику устройства Центра Интернета вещей Azure, однако они предоставляют более высокую степень детализации. В то время как удостоверение и двойник устройства Центра Интернета вещей Azure позволяют серверному приложению настраивать устройство и отображать состояние устройства, удостоверение и двойник модуля предоставляют эти возможности для отдельных компонентов устройства. Благодаря этому можно изолировать конфигурацию и условия для каждого компонента на совместимых устройствах с несколькими компонентами, например устройствах на основе операционной системы или устройствах со встроенным ПО.

По завершении работы с этим руководством у вас будет два приложения Node.js:

* **CreateIdentities** — создает удостоверение устройства и модуля, а также соответствующий ключ безопасности для подключения к клиентам устройства и модуля.

* **UpdateModuleTwinReportedProperties** — отправляет обновленные сообщаемые свойства двойника модуля в Центр Интернета вещей.

> [!NOTE]
> Сведения о пакетах SDK Azure IoT, которые можно использовать для создания приложений, которые будут работать на устройствах и в серверной части решения, см. в [этой статье](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Предварительные требования

* Версия node.js 10.0.x или позже. В статье [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Подготовка среды разработки) описывается, как установить Node.js для работы с этим учебником в ОС Windows или Linux.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получите строку соединения концентратора IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Создание удостоверения устройства и удостоверения модуля в Центре Интернета вещей

В этом разделе объясняется, как создать приложение Node.js, создающее удостоверение устройства и удостоверение модуля в реестре удостоверений Центра Интернета вещей. Устройства и модули не могут подключаться к Центру Интернета вещей, если в реестре удостоверений для них нет соответствующей записи. Дополнительные сведения см. в разделе, посвященном реестру удостоверений, в [руководстве разработчика для Центра Интернета вещей Azure](iot-hub-devguide-identity-registry.md). Запущенное консольное приложение создает уникальные идентификаторы и ключи одновременно для устройства и для модуля. Устройство и модуль применяют эти значения для идентификации при отправке сообщений с устройства в облако в Центр Интернета вещей. В идентификаторах учитывается регистр символов.

1. Создайте каталог для размещения файлов с кодом.

2. Выполните в этом каталоге команду **npm init -y**, чтобы создать пустой файл package.json со значениями по умолчанию. Это файл проекта, в котором будет содержаться код.

3. Выполнить **npm установить -S\@azure-iothub модулей-предварительный** для установки службы SDK внутри **node_modules** субдиректоров.

    > [!NOTE]
    > Слово module в имени подкаталога node_modules обозначает "библиотеку узла". Он не имеет никакого отношения к модулям Центра Интернета вещей.

4. Создайте в этом каталоге приведенный ниже JS-файл. Присвойте ему имя **add.js**. Скопируйте имя Центра Интернета вещей и строку подключения к нему, затем вставьте их в соответствующие места.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Это приложение создает удостоверение устройства с идентификатором **myFirstDevice** и удостоверение модуля с идентификатором **myFirstModule** на устройстве **myFirstDevice**. (Если этот идентификатор модуля уже существует в реестре идентификации, код просто извлекает существующую информацию о модуле.) Затем приложение отображает основной ключ для этой идентификации. Этот ключ будет использоваться в приложении виртуального модуля для подключения к Центру Интернета вещей.

Выполните его для узла add.js. Так вы получите строку подключения для удостоверения устройства и еще одну для удостоверения модуля.

> [!NOTE]
> В реестре удостоверений в Центре Интернета вещей хранятся только удостоверения устройств и модулей, необходимые для защиты доступа к Центру Интернета вещей. Реестр удостоверений хранит идентификаторы устройств и ключи, используемые в качестве учетных данных безопасности. Реестр удостоверений также хранит флаги включения и отключения для каждого устройства. Эти флаги можно использовать для отключения доступа для этого устройства. Если в приложении необходимо хранить другие метаданные для конкретного устройства, следует использовать хранилище конкретного приложения. У вас нет возможности включать и отключать удостоверения модулей. Дополнительные сведения см. в [руководстве разработчика для Центра Интернета вещей](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Обновление двойника модуля с помощью пакета SDK для устройства на Node.js

В этом разделе объясняется, как на имитированном устройстве создать приложение Node.js, которое обновляет сообщаемые свойства двойника модуля.

1. **Получите строку подключения модуля** - вопийте на [порталAz.](https://portal.azure.com/) Перейдите к Центру Интернета вещей и щелкните "Устройства IoT". Найдите устройство myFirstDevice, откройте его и вы увидите, что модуль myFirstModule успешно создан. Скопируйте строку подключения модуля. Она понадобится на следующем шаге.

   ![Сведения о модуле на портале Azure](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Подобно тому, как вы сделали в шаге выше, создать каталог для вашего кода устройства и использовать NPM для инициализации и установить устройство SDK **(npm установить -S azure-iot-устройство-amqp\@модулей-предварительный просмотр**).

   > [!NOTE]
   > Команда npm install может показаться довольно медленной. Не спешите, ведь ей нужно извлечь большой объем кода из репозитория пакетов.

   > [!NOTE]
   > Если вы увидите ошибку "npm ERR! registry error parsing json" (Ошибка реестра при синтаксическом анализе JSON), можете проигнорировать ее. Если вы увидите ошибку "npm ERR! registry error parsing json" (Ошибка реестра при синтаксическом анализе JSON), можете проигнорировать ее.

3. Создайте файл с именем twin.js. Скопируйте и вставьте в него строку удостоверения устройства.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Теперь запустите этот файл командой **node twin.js**.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Затем вы увидите:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы продолжить знакомство с Центром Интернета вещей и изучить другие сценарии Интернета вещей, см. следующие ресурсы:

* [How to get started with device management (Node)](iot-hub-node-node-device-management-get-started.md) (Начало работы с управлением устройствами (Node))

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)