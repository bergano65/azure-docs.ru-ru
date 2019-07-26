---
title: Начало работы с двойниками устройств Центра Интернета вещей (Node) | Документация Майкрософт
description: Добавление тегов и последующее использование запроса Центра Интернета вещей с помощью двойников устройств Центра Интернета вещей. Используйте пакеты SDK для Центра Интернета вещей Azure для Node.js, чтобы реализовать приложение имитации устройства и приложение службы, которое добавит теги, и выполнить запрос Центра Интернета вещей.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: 8dd5269532e9eb6139d8debb0ee9b503cd2e4354
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404019"
---
# <a name="get-started-with-device-twins-node"></a>Начало работы с двойниками устройств (Node)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

По завершении работы с этим руководством у вас будет два консольных приложения Node.js:

* **AddTagsAndQuery.js** — это внутреннее приложение Node.js, которое добавляет теги и выполняет запросы к двойникам устройств.

* **TwinSimulatedDevice.js** — это приложение Node.js, которое имитирует устройство, подключающееся к Центру Интернета вещей с использованием удостоверения устройства, созданного ранее, и сообщает о его условии подключения.

> [!NOTE]
> Статья [Общие сведения о пакетах SDK для Azure IoT и их использование](iot-hub-devguide-sdks.md) содержит сведения о разных пакетах SDK для Интернета вещей Azure, с помощью которых можно создать приложения для устройств и внутренние приложения.
>

Для работы с этим учебником требуется:

* Node. js версии 10.0. x или более поздней.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Получение строки подключения для центра Интернета вещей

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Создание приложения службы

В этом разделе вы создадите консольное приложение Node.js, которое добавляет метаданные расположения в двойник устройства, связанный с **myDeviceId**. После этого оно запрашивает данные двойников устройств, хранящихся в Центре Интернета вещей, выбирает устройства, находящиеся в США, а затем те, которые сообщили о подключении по сети мобильной связи.

1. Создайте пустую папку с именем **addtagsandqueryapp**. В папке **addtagsandqueryapp** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:

    ```
    npm init
    ```

2. В командной строке в папке **addtagsandqueryapp** выполните следующую команду, чтобы установить пакет **azure-iothub**.
   
    ```
    npm install azure-iothub --save
    ```

3. В текстовом редакторе создайте файл **AddTagsAndQuery.js** в папке **addtagsandqueryapp**.

4. Добавьте следующий код в файл **AddTagsAndQuery. js** и замените значение заполнителя **{строка подключения центра Интернета вещей}** строкой подключения центра Интернета вещей, скопированной ранее в поле [Получение строки подключения к центру Интернета вещей](#get-the-iot-hub-connection-string):

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    Объект **Registry** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства из службы. Предыдущий код сначала инициализирует объект **Registry**, затем извлекает двойник устройства для **myDeviceId** и, наконец, обновляет его теги, используя сведения о требуемом расположении.

    После обновления тегов он вызывает функцию **queryTwins**.

5. Добавьте следующий код в конце файла **AddTagsAndQuery.js** для реализации функции **queryTwins**:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Предыдущий код выполняет два запроса: первый выбирает только двойников устройства, расположенных на фабрике **Redmond43**, а второй уточняет условия первого запроса и выбирает устройства, подключенные по сети мобильной связи.

    При создании объекта **query** предыдущий код указывает максимальное количество возвращаемых документов. Объект **query** содержит логическое свойство **hasMoreResults**, которое можно использовать для вызова методов **nextAsTwin** несколько раз, чтобы получить все результаты. Метод **next** доступен для результатов, которые не являются двойниками устройств, например для результатов статистических запросов.

6. Запустите приложение, выполнив следующую команду:

    ```
        node AddTagsAndQuery.js
    ```

   В результатах запроса на все устройства, расположенные на фабрике **Redmond43**, отобразится одно устройство, а для запроса на ограничение результатов устройствами, использующими сеть мобильной связи, не отобразится ни одного устройства.
   
    ![Просмотр одного устройства в результатах запроса](media/iot-hub-node-node-twin-getstarted/service1.png)

В следующем разделе рассказывается о том, как создать приложение устройства, которое сообщает сведения о подключении и изменяет результат запроса, описанного в предыдущем разделе.

## <a name="create-the-device-app"></a>Создание приложения устройства

В этом разделе вы создадите консольное приложение Node.js, которое подключается к центру как **myDeviceId** и обновляет сообщаемые свойства двойника устройства, добавив в них сведения о подключении по сети мобильной связи.

1. Создайте пустую папку с именем **reportconnectivity**. В папке **reportconnectivity** создайте файл package.json, используя следующую команду в командной строке. Примите значения по умолчанию:
   
    ```
    npm init
    ```

2. В командной строке в папке **reportconnectivity** выполните следующую команду, чтобы установить пакет SDK для устройств **azure-iot-device** и пакет **azure-iot-device-mqtt**.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. В текстовом редакторе создайте файл **ReportConnectivity.js** в папке **reportconnectivity**.

4. Добавьте следующий код в файл **ReportConnectivity.js** и замените заполнитель **{device connection string}** строкой подключения устройства, скопированной при создании удостоверения устройства **myDeviceId**.

    ```
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    Объект **Client** позволяет получить все методы, необходимые для взаимодействия с двойниками устройства с устройства. После инициализации объекта **Client** предыдущий код извлекает двойник устройства для **myDeviceId** и обновляет его сообщаемое свойство, используя сведения о подключении.

5. Запуск приложения устройства

    ```   
        node ReportConnectivity.js
    ```

    Отобразится сообщение `twin state reported`.

6. Теперь, когда устройство сообщило сведения о подключении, оно должно появиться в обоих запросах. Вернитесь к папке **addtagsandqueryapp** и выполните запросы снова:

    ```   
        node AddTagsAndQuery.js
    ```

    На этот раз **myDeviceId** должен появиться в результатах обоих запросов.

    ![Показывать myDeviceId в обоих результатах запроса](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Следующие шаги

В этом руководстве мы настроили новый Центр Интернета вещей на портале Azure и создали удостоверение устройства в реестре удостоверений Центра Интернета вещей. Вы добавили метаданные устройства в качестве тегов из внутреннего приложения и написали код приложения имитации устройства, чтобы сообщить сведения о подключении в двойнике устройства. Вы также узнали, как запрашивать эти сведения, используя похожий на SQL язык запросов Центра Интернета вещей.

Ознакомьтесь со следующими материалами, чтобы узнать как:

* Отправка данных телеметрии с устройств с помощью учебника Приступая к [работе с центром Интернета вещей](quickstart-send-telemetry-node.md)

* настроить устройства с помощью требуемых свойств двойника устройства ([Руководство. Настройка устройств из внутренней службы](tutorial-device-twins.md));

* Управление устройствами в интерактивном режиме (например, включение вентилятора из управляемого пользователем приложения) с помощью учебника [Использование прямых методов](quickstart-control-device-node.md) .