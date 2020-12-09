---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 4b19ae1b584a2d300e4144e79ef76245c71035cf
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126138"
---
## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Приложение Azure IoT Central, созданное на основе шаблона **Пользовательское приложение**. Дополнительные сведения см. в [кратком руководстве по созданию приложения](../articles/iot-central/core/quick-deploy-iot-central.md). Приложение должно быть создано не ранее 14 июля 2020 года.
* Компьютер для разработки с установленным [Node.js](https://nodejs.org/) 6 или более поздней версии. Вы можете запустить `node --version` в командной строке, чтобы проверить версию. В инструкциях этого руководства предполагается, что вы выполняете команду **node** в командной строке Windows. Но вы можете использовать Node.js во множестве других операционных системах.
* Локальная копия репозитория [пакета SDK Интернета вещей Microsoft Azure для Node.js](https://github.com/Azure/azure-iot-sdk-node) с сайта GitHub, содержащая образец кода. Используйте эту ссылку, чтобы скачать копию репозитория: [Скачать ZIP-файл](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Затем распакуйте файл в подходящее расположение на локальном компьютере.

## <a name="review-the-code"></a>Просмотр кода

В копии скачанного ранее пакета SDK Интернета вещей Microsoft Azure для Node.js откройте файл *azure-iot-sdk-node/device/samples/pnp/simple_thermostat.js* с помощью текстового редактора.

Когда вы запускаете пример для подключения к IoT Central, для регистрации устройства и создания строки подключения используется служба подготовки устройств (DPS). В примере извлекаются необходимые сведения о подключении DPS из среды командной строки.

Метод `main`:

* создает объект `client` и задает идентификатор модели `dtmi:com:example:Thermostat;1` перед установкой соединения;
* создает обработчик команд;
* запускает цикл для оправки данных телеметрии температуры каждые 10 секунд;
* отправляет свойство `maxTempSinceLastReboot` в IoT Central. IoT Central игнорирует свойство `serialNumber`, так как оно не является частью модели устройства;
* создает обработчик свойств с возможностью записи.

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

Функция `provisionDevice` показывает, как устройство использует DPS для регистрации и подключения к IoT Central. Полезные данные содержат идентификатор модели:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

Функция `sendTelemetry` показывает, как устройство отправляет данные телеметрии температуры в IoT Central. Метод `getCurrentTemperatureObject` возвращает объект, который выглядит как `{ temperature: 45.6 }`:

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

Метод `main` использует следующие два метода для отправки свойства `maxTempSinceLastReboot` в IoT Central. Метод `main` вызывает `createReportPropPatch` с объектом, который выглядит как `{maxTempSinceLastReboot: 80.9}`:

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

Метод `main` использует следующие два метода для обработки обновлений доступного для записи свойства _целевой температуры_ из IoT Central. Обратите внимание, что `propertyUpdateHandle` создает ответ с версией и кодом состояния:

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

Метод `main` использует следующие два метода для обработки вызовов команды `getMaxMinReport`. Метод `getMaxMinReportObject` создает отчет в виде объекта JSON:

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>Получение сведений о подключении

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Выполнение кода

Чтобы запустить пример приложения, откройте среду командной строки и перейдите в папку *Azure-IOT-SDK-node/Device/Samples/PnP*, содержащую пример файла *simple_thermostat.js*.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Установите необходимые пакеты.

```cmd/sh
npm install
```

Запустите пример:

```cmd/sh
node simple_thermostat.js
```

В указанных ниже выходных данных показаны регистрация устройства и его подключение к IoT Central. Затем в примере отправляется свойство `maxTempSinceLastReboot` перед отправкой данных телеметрии:

```output
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Вы можете увидеть, как устройство реагирует на команды и обновления свойств:

```output
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```
