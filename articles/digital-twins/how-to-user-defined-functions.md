---
title: Использование определяемых пользователем функций в Azure Digital Twins | Документация Майкрософт
description: Руководство по созданию определяемых пользователем функций, сопоставителей и назначений ролей с помощью Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 8094965da5fb0a5fad0313fd96e2878f86d78aa7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215503"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Использование определяемых пользователем функций в Azure Digital Twins

[Определяемые пользователем функции](./concepts-user-defined-functions.md) предоставляют пользователю возможность запускать настраиваемую логику для входящих сообщений телеметрии и метаданных пространственного графа, позволяя отправлять события в заранее определенные конечные точки. В этом руководстве на примере событий температуры мы рассмотрим обнаружение и оповещение о любой операции чтения с превышением определенной температуры.

В приведенных ниже примерах `https://yourManagementApiUrl` относится к универсальному коду ресурса API Digital Twins:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourInstanceName* | Имя вашего экземпляра Azure Digital Twins |
| *yourLocation* | Регион сервера, в котором размещен ваш экземпляр |

## <a name="client-library-reference"></a>Справочник по клиентской библиотеке

Функции, доступные в качестве вспомогательных методов в среде выполнения определяемых пользователем функций, перечисляются в разделе [Справочник клиента](#Client-Reference).

## <a name="create-a-matcher"></a>Создание сопоставителя

Сопоставители — это объекты графа, определяющие, какие определяемые пользователем функции будут выполняться для данного сообщения телеметрии.

Сравнения допустимых состояний сопоставителя:

- `Equals`
- `NotEquals`
- `Contains`

Целевые показатели допустимых состояний сопоставителя:

- `Sensor`
- `SensorDevice`
- `SensorSpace`

В приведенном ниже примере в рамках оценки сопоставитель вычислит значение true по любому событию телеметрии датчика с `"Temperature"`, указанным в качестве значения типа данных. Вы можете создать несколько сопоставителей в определяемой пользователем функции.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/matchers
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
  "SpaceId": "yourSpaceIdentifier"
}
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourManagementApiUrl* | Полный URL-путь к нужному API управления  |
| *yourSpaceIdentifier* | Регион сервера, в котором размещен ваш экземпляр |

## <a name="create-a-user-defined-function-udf"></a>Создание определяемой пользователем функции

После создания сопоставителей загрузите фрагмент функции со следующим вызовом POST:

> [!IMPORTANT]
> - В разделе заголовков укажите `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Раздел "Текст" состоит из нескольких частей:
>   - Первая часть — метаданные, необходимые для UDF.
>   - Вторая часть — логика вычислений javascript.
> - В разделе `userDefinedBoundary` замените идентификаторы GUID `SpaceId` и `Machers`.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourManagementApiUrl* | Полный URL-путь к нужному API управления  |

Текст:

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourSpaceIdentifier* | Идентификатор пространства  |
| *yourMatcherIdentifier* | Идентификатор необходимого сопоставителя |

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

Параметр *telemetry* предоставляет атрибуты **SensorId** и **Message** (соответствующие отправленному датчиком сообщению). Параметр *ExecutionContext* предоставляет следующие атрибуты.

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

В следующем примере будет записано сообщение, если чтение данных телеметрии датчика превысит предварительно заданный порог. Если параметры диагностики включены в экземпляре Digital Twins, журналы из определяемых пользователем функций будут переадресованы.

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

Следующий код активирует уведомление, если уровень температуры превысит предопределенную константу.

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

Более сложный пример кода UDF см. [на сайте Github](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Создание назначения роли

Нам нужно создать назначение ролей для выполнения определяемой пользователем функции. В противном случае у нее не будет надлежащих прав для взаимодействия с API управления и выполнения действий с объектами графа. Действия, которые выполняет определяемая пользователем функция, не освобождаются от управления доступом на основе ролей в API управления Digital Twins. Их можно ограничить по областям, указав определенные роли или пути управления доступом. Дополнительные сведения см. в статье [Управление доступом на основе ролей](./security-role-based-access-control.md).

1. Запросите роли и получите идентификатор роли, которую вы хотите назначить UDF; передайте их приведенному ниже **RoleId**.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourManagementApiUrl* | Полный URL-путь к нужному API управления  |

2. **ObjectId** будет созданным ранее идентификатором UDF.
3. Найдите значение **пути**, запрашивая пространства с помощью `fullpath`.
4. Скопируйте возвращенное значение `spacePaths`. Оно понадобится вам далее.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourManagementApiUrl* | Полный URL-путь к нужному API управления  |
| *yourSpaceName* | Имя необходимого пространства |

4. Теперь вставьте возвращенное значение `spacePaths` в **путь**, чтобы создать назначение ролей определяемой пользователем функции.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourManagementApiUrl* | Полный URL-путь к нужному API управления  |
| *yourDesiredRoleIdentifier* | Идентификатор необходимой роли |
| *yourUserDefinedFunctionId* | Идентификатор необходимой UDF |
| *yourAccessControlPath* | Путь для управления доступом |

## <a name="send-telemetry-to-be-processed"></a>Отправка данных телеметрии для обработки

Данные телеметрии, созданные датчиком, описанным в графе, должны активировать выполнение отправленной определяемой пользователем функции. Как только обработчик данных соберет данные телеметрии, для вызова определяемой пользователем функции будет создан план выполнения.

1. Извлеките сопоставители для датчика, с которого было выполнено чтение.
1. В зависимости от того, что именно сопоставители успешно оценили, извлеките связанные определяемые пользователем функции.
1. Выполните каждую определяемую пользователем функцию.

## <a name="client-reference"></a>Справочник клиента

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Получив идентификатор пространства, извлекает пространство из графа.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *id*  | `guid` | идентификатор пространства |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Получив идентификатор датчика, извлекает датчик из графа.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *id*  | `guid` | идентификатор датчика |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Получив идентификатора устройства, извлекает устройство из графа.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | идентификатор устройства |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Получив идентификатор датчика и тип его данных, извлекает текущее значение для этого датчика.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | идентификатор датчика |
| *dataType*  | `string` | тип данных датчика |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Получив идентификатор пространства и имя значения, извлекает текущее значение свойства этого пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | идентификатор пространства |
| *valueName* | `string` | имя свойства пространства |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Получив идентификатор датчика и тип его данных, извлекает исторические значения для этого датчика.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | идентификатор датчика |
| *dataType* | `string` | тип данных датчика |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Получив идентификатор пространства и имя значения, извлекает исторические значения свойства этого пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | идентификатор пространства |
| *valueName* | `string` | имя свойства пространства |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Получив идентификатор пространства, извлекает дочерние пространства для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | идентификатор пространства |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Получив идентификатор пространства, извлекает дочерние датчики для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | идентификатор пространства |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Получив идентификатор пространства, извлекает дочерние устройства для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | идентификатор пространства |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Получив идентификатор устройства, извлекает дочерние датчики для этого родительского устройства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | идентификатор устройства |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Получив идентификатор пространства, извлекает его родительское пространство.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | идентификатор пространства |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Получив идентификатор датчика, извлекает его родительское пространство.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | идентификатор датчика |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Получив идентификатор устройства, извлекает его родительское пространство.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | идентификатор устройства |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Получив идентификатор датчика, извлекает его родительское устройство.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | идентификатор датчика |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Получив идентификатор пространства, извлекает свойство и его значение из пространства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | идентификатор пространства |
| *propertyName* | `string` | имя свойства пространства |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Получив идентификатор датчика, извлекает свойство и его значение из датчика.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | идентификатор датчика |
| *propertyName* | `string` | имя свойства датчика |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Получив идентификатор устройства, извлекает свойство и его значение с устройства.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | идентификатор устройства |
| *propertyName* | `string` | имя свойства устройства |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Устанавливает значение в объекте датчика с заданным типом данных.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | идентификатор датчика |
| *dataType*  | `string` | тип данных датчика |
| *значение*  | `string` | value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Устанавливает значение в объекте пространства с заданным типом данных.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | идентификатор пространства |
| *dataType* | `string` | тип данных |
| *значение* | `string` | value |

### <a name="logmessage"></a>log(Message)

Записывает сообщение в определяемой пользователем функции.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *message* | `string` | сообщение для записи |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Отправляет настраиваемое уведомление для рассылки.

**Вид**: глобальная функция.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | идентификатор объекта графа (например, пространство, датчик, идентификатор устройства)|
| *topologyObjectType*  | `string` | (Например, пространство, датчик, устройство)|
| *payload*  | `string` | полезные данные JSON, отправляемые с уведомлением |

## <a name="return-types"></a>Типы возвращаемого значения

Ниже приведены модели, описывающие объекты, возвращаемые из ссылки на клиент.

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

Возвращает родительское пространство текущего пространства.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Возвращает дочерние датчики текущего пространства.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Возвращает дочерние устройства текущего пространства.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Возвращает расширенное свойство и его значение для текущего пространства.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | имя расширенного свойства |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Возвращает значение текущего пространства.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | имя значения |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Возвращает исторические значения текущего пространства.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | имя значения |

#### <a name="notifypayload"></a>Notify(payload)

Отправляет уведомление с указанными полезными данными.

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

Возвращает родительское пространство текущего устройства.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Возвращает дочерние датчики текущего устройства.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Возвращает расширенное свойство и его значение для текущего устройства.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | имя расширенного свойства |

#### <a name="notifypayload"></a>Notify(payload)

Отправляет уведомление с указанными полезными данными.

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

Возвращает родительское пространство текущего датчика.

#### <a name="device--device"></a>Device() ⇒ `device`

Возвращает родительское устройство текущего датчика.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Возвращает расширенное свойство и его значение для текущего датчика.

| Параметр  | type                | ОПИСАНИЕ  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | имя расширенного свойства |

#### <a name="value--value"></a>Value() ⇒ `value`

Возвращает значение текущего датчика.

#### <a name="history--value"></a>History() ⇒ `value[]`

Возвращает исторические значения текущего датчика.

#### <a name="notifypayload"></a>Notify(payload)

Отправляет уведомление с указанными полезными данными.

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

### <a name="extended-property"></a>Свойство Extended

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Дополнительная информация

Сведения о создании конечных точек Digital Twins для отправки событий см. в статье [Исходящий трафик и конечные точки](how-to-egress-endpoints.md).

Дополнительные сведения о конечных точках Digital Twins см. в статье [Маршрутизация событий и сообщений](concepts-events-routing.md).
