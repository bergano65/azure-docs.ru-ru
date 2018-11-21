---
title: Использование определяемых пользователем функций в Azure Digital Twins | Документация Майкрософт
description: Руководство по созданию определяемых пользователем функций, сопоставителей и назначений ролей с помощью Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 6a757dca48dc3ff41adfe6f8802fad40e7a4ca81
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636838"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Использование определяемых пользователем функций в Azure Digital Twins

[Определяемые пользователем функции](./concepts-user-defined-functions.md) предоставляют пользователю возможность запускать настраиваемую логику для входящих сообщений телеметрии и метаданных пространственного графа. Это позволит пользователю отправлять события в заранее определенные конечные точки. В этом руководстве на примере событий температуры описано обнаружение показаний, превышающих определенную температуру, и информирование о них.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Справочник по клиентской библиотеке

Функции, доступные в качестве вспомогательных методов в среде выполнения определяемых пользователем функций, перечислены в разделе [Справочник клиента](#Client-Reference).

## <a name="create-a-matcher"></a>Создание сопоставителя

Сопоставители как объекты графа хранят сведения о том, какие определяемые пользователем функции будут выполняться для некоторого сообщения телеметрии.

- Сравнения допустимых состояний сопоставителя:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Целевые показатели допустимых состояний сопоставителя:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

В приведенном ниже примере сопоставитель возвращает значение true для любого события телеметрии датчика с типом значения данных `"Temperature"`. Вы можете создать для определяемой пользователем функции несколько сопоставителей.

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Значение | Заменить на |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Регион сервера, в котором размещен ваш экземпляр |

## <a name="create-a-user-defined-function-udf"></a>Создание определяемой пользователем функции

После создания сопоставителей загрузите фрагмент функции со следующим вызовом **POST**:

> [!IMPORTANT]
> - В разделе заголовков укажите `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Основной текст состоит из нескольких частей:
>   - Первая часть содержит метаданные для определяемой пользователем функции.
>   - Вторая часть определяет логику вычислений на JavaScript.
> - В разделе **USER_DEFINED_BOUNDARY** замените значения **SpaceId** и **Matchers**.

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| Значение параметра | Заменить на |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Имя границы составного содержимого |

### <a name="body"></a>Текст

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Значение | Заменить на |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Идентификатор пространства  |
| YOUR_MATCHER_IDENTIFIER | Идентификатор необходимого сопоставителя |

### <a name="example-functions"></a>Примеры функций

Установите чтение данных телеметрии датчика непосредственно для датчика с типом данных **Temperature**, который является `sensor.DataType`.

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Параметр **telemetry** предоставляет атрибуты **SensorId** и **Message** для описания отправленного датчиком сообщения. Параметр **ExecutionContext** предоставляет следующие атрибуты.

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

В следующем примере мы сохраняет сообщение в журнал, если значение температуры из телеметрии датчика превысит предварительно заданный порог. Если для экземпляра Azure Digital Twins включены параметры диагностики, передаются также журналы из определяемых пользователем функций.

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Следующий код активирует уведомление, когда уровень температуры превышает предопределенную константу.

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Более сложную определяемую пользователем функцию можно изучить [в этом примере на сайте Github](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Создание назначения роли

Нам нужно создать назначение роли для выполнения определяемой пользователем функции. В противном случае у нее не будет прав для взаимодействия с API управления и выполнения действий с объектами графа. Действия, которые выполняет определяемая пользователем функция, не освобождаются от управления доступом на основе ролей в API управления Azure Digital Twins. Их можно ограничить по областям, указав определенные роли или пути управления доступом. Дополнительные сведения см. в документации [по управлению доступом на основе ролей](./security-role-based-access-control.md).

1. Запросите роли и получите идентификатор роли, которую вы хотите назначить для определяемой пользователем функции. Передайте это значение в параметре **RoleId**:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **ObjectId** будет созданным ранее идентификатором UDF.
1. Найдите значение **пути**, запрашивая пространства с помощью `fullpath`.
1. Скопируйте возвращенное значение `spacePaths`. Оно будет использоваться в следующем коде.

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Значение параметра | Заменить на |
    | --- | --- |
    | *YOUR_SPACE_NAME* | Имя необходимого пространства |

1. Вставьте полученное значение `spacePaths` в поле **Путь**, чтобы назначить роль определяемой пользователем функции.

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Значение | Заменить на |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Идентификатор необходимой роли |
    | YOUR_USER_DEFINED_FUNCTION_ID | Идентификатор необходимой UDF |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Идентификатор типа для определяемой пользователем функции |
    | YOUR_ACCESS_CONTROL_PATH | Путь для управления доступом |

## <a name="send-telemetry-to-be-processed"></a>Отправка данных телеметрии для обработки

Данные телеметрии, созданные описанным в графе датчиком, будут активировать соответствующую определяемую пользователем функцию. Обработчик данных получает данные телеметрии. Затем создает план выполнения для вызова определяемой пользователем функции.

1. Извлеките сопоставители для датчика, с которого было выполнено чтение.
1. В зависимости от того, что именно сопоставители успешно оценили, извлеките связанные определяемые пользователем функции.
1. Выполните каждую определяемую пользователем функцию.

## <a name="client-reference"></a>Справочник клиента

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Получив идентификатор пространства, функция извлекает это пространство из графа.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Идентификатор пространства |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Получив идентификатор датчика, функция извлекает этот датчик из графа.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Идентификатор датчика |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Получив идентификатор устройства, функция извлекает это устройство из графа.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Идентификатор устройства |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Получив идентификатор и тип данных датчика, функция извлекает текущее значение для этого датчика.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Идентификатор датчика |
| *dataType*  | `string` | Тип данных датчика |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Получив идентификатор пространства и имя значения, функция извлекает текущее значение для этого свойства пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Идентификатор пространства |
| *valueName* | `string` | Имя свойства пространства |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Получив идентификатор и тип данных датчика, функция извлекает исторические значения для этого датчика.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Идентификатор датчика |
| *dataType* | `string` | Тип данных датчика |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Получив идентификатор пространства и имя значения, функция извлекает исторические значения этого свойства пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |
| *valueName* | `string` | Имя свойства пространства |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Получив идентификатор пространства, функция извлекает дочерние пространства для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Получив идентификатор пространства, функция извлекает дочерние датчики для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Получив идентификатор пространства, функция извлекает дочерние устройства для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Получив идентификатор устройства, функция извлекает дочерние датчики для этого родительского устройства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Идентификатор устройства |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Получив идентификатор пространства, функция извлекает родительское пространство.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Идентификатор пространства |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Получив идентификатор датчика, функция извлекает родительское пространство.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Идентификатор датчика |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Получив идентификатор устройства, функция извлекает родительское пространство.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Идентификатор устройства |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Получив идентификатор датчика, функция извлекает родительское устройство.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Идентификатор датчика |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Получив идентификатор пространства, функция извлекает свойство и его значение из пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |
| *propertyName* | `string` | Имя свойства пространства |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Получив идентификатор датчика, функция извлекает свойство и его значение из датчика.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Идентификатор датчика |
| *propertyName* | `string` | Имя свойства датчика |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Получив идентификатор устройства, функция извлекает свойство и его значение из устройства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Идентификатор устройства |
| *propertyName* | `string` | Имя свойства устройства |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Эта функция устанавливает значение с заданным типом данных в объекте датчика.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Идентификатор датчика |
| *dataType*  | `string` | Тип данных датчика |
| *значение*  | `string` | Значение |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Эта функция устанавливает значение с заданным типом данных в объекте пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |
| *dataType* | `string` | Тип данных |
| *значение* | `string` | Значение |

### <a name="logmessage"></a>log(Message)

Эта функция записывает в журнал сообщение из определяемой пользователем функции.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Сообщение для записи |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Эта функция отправляет настраиваемое уведомление для рассылки.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Идентификатор объекта графа. Это может быть идентификатор пространства, датчика, устройства и т. п.|
| *topologyObjectType*  | `string` | Это может быть идентификатор пространства, устройства и т. п.|
| *payload*  | `string` | Полезные данные JSON, отправляемые вместе с уведомлением. |

## <a name="return-types"></a>Возвращаемые типы

Ниже приведены модели, которые описывают объекты из приведенной выше справочной информации.

### <a name="space"></a>Пробел

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>Методы пространства

#### <a name="parent--space"></a>Parent() ⇒ `space`

Эта функция возвращает родительское пространство текущего пространства.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Эта функция возвращает дочерние датчики текущего пространства.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Эта функция возвращает дочерние устройства текущего пространства.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Эта функция возвращает расширенное свойство и его значение для текущего пространства.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Имя расширенного свойства |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Эта функция возвращает значение для текущего пространства.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Имя значения |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Эта функция возвращает исторические значения для текущего пространства.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Имя значения |

#### <a name="notifypayload"></a>Notify(payload)

Эта функция отправляет уведомление с указанными полезными данными.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | полезные данные JSON, которые будут включены в уведомление |

### <a name="device"></a>Устройство

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Методы устройства

#### <a name="parent--space"></a>Parent() ⇒ `space`

Эта функция возвращает родительское пространство текущего устройства.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Эта функция возвращает дочерние датчики текущего устройства.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Эта функция возвращает расширенное свойство и его значение для текущего устройства.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Имя расширенного свойства |

#### <a name="notifypayload"></a>Notify(payload)

Эта функция отправляет уведомление с указанными полезными данными.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | полезные данные JSON, которые будут включены в уведомление |

### <a name="sensor"></a>Датчик

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>Методы датчика

#### <a name="space--space"></a>Space() ⇒ `space`

Эта функция возвращает родительское пространство текущего датчика.

#### <a name="device--device"></a>Device() ⇒ `device`

Эта функция возвращает родительское устройство текущего датчика.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Эта функция возвращает расширенное свойство и его значение для текущего датчика.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Имя расширенного свойства |

#### <a name="value--value"></a>Value() ⇒ `value`

Эта функция возвращает значение для текущего датчика.

#### <a name="history--value"></a>History() ⇒ `value[]`

Эта функция возвращает исторические значения для текущего датчика.

#### <a name="notifypayload"></a>Notify(payload)

Эта функция отправляет уведомление с указанными полезными данными.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | полезные данные JSON, которые будут включены в уведомление |

### <a name="value"></a>Значение

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Расширенное свойство

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [создать конечные точки Azure Digital Twins](how-to-egress-endpoints.md), чтобы отправлять в них события.

- Дополнительные сведения о конечных точках Azure Digital Twins см. в статье [Маршрутизация событий и сообщений](concepts-events-routing.md).
