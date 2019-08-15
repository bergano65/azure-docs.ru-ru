---
title: Справочник по клиентским библиотекам определяемых пользователем функций Azure Digital Twins | Документация Майкрософт
description: Справочник по клиентским библиотекам определяемых пользователем функций Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 08/12/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 14c153a6e5837da28060122a629c8cc5df8262cf
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013773"
---
# <a name="user-defined-functions-client-library-reference"></a>Справочник по клиентским библиотекам определяемых пользователем функций

В этом документе содержится справочная информация о клиентской библиотеке определяемых пользователем функций Azure Digital Twins.

## <a name="helper-methods"></a>Вспомогательные методы

Клиентская библиотека определяет вспомогательные методы для часто используемых операций.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Получив идентификатор пространства, функция извлекает это пространство из графа.

**Вид**: глобальная функция.

| Параметр  | Type                | Описание  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Идентификатор пространства |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Получив идентификатор датчика, функция извлекает этот датчик из графа.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Идентификатор датчика |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Получив идентификатор устройства, функция извлекает это устройство из графа.

**Вид**: глобальная функция.

| Параметр  | Type                | Описание  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Идентификатор устройства |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Получив идентификатор и тип данных датчика, функция извлекает текущее значение для этого датчика.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Идентификатор датчика |
| *dataType*  | `string` | Тип данных датчика |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Получив идентификатор пространства и имя значения, функция извлекает текущее значение для этого свойства пространства.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Идентификатор пространства |
| *valueName* | `string` | Имя свойства пространства |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Получив идентификатор и тип данных датчика, функция извлекает исторические значения для этого датчика.

**Вид**: глобальная функция.

| Параметр  | Type                | Описание  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Идентификатор датчика |
| *dataType* | `string` | Тип данных датчика |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Получив идентификатор пространства и имя значения, функция извлекает исторические значения этого свойства пространства.

**Вид**: глобальная функция.

| Параметр  | Type                | Описание  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |
| *valueName* | `string` | Имя свойства пространства |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Получив идентификатор пространства, функция извлекает дочерние пространства для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Получив идентификатор пространства, функция извлекает дочерние датчики для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | Type                | Описание  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Получив идентификатор пространства, функция извлекает дочерние устройства для этого родительского пространства.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Получив идентификатор устройства, функция извлекает дочерние датчики для этого родительского устройства.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Идентификатор устройства |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Получив идентификатор пространства, функция извлекает родительское пространство.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Идентификатор пространства |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Получив идентификатор датчика, функция извлекает родительское пространство.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Идентификатор датчика |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Получив идентификатор устройства, функция извлекает родительское пространство.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Идентификатор устройства |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Получив идентификатор датчика, функция извлекает родительское устройство.

**Вид**: глобальная функция.

| Параметр  | Type                | Описание  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Идентификатор датчика |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Получив идентификатор пространства, функция извлекает свойство и его значение из пространства.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |
| *propertyName* | `string` | Имя свойства пространства |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Получив идентификатор датчика, функция извлекает свойство и его значение из датчика.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Идентификатор датчика |
| *propertyName* | `string` | Имя свойства датчика |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Получив идентификатор устройства, функция извлекает свойство и его значение из устройства.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Идентификатор устройства |
| *propertyName* | `string` | Имя свойства устройства |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Эта функция устанавливает значение с заданным типом данных в объекте датчика.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Идентификатор датчика |
| *dataType*  | `string` | Тип данных датчика |
| *значение*  | `string` | Значение |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Эта функция устанавливает значение с заданным типом данных в объекте пространства.

**Вид**: глобальная функция.

| Параметр  | Type                | Описание  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Идентификатор пространства |
| *dataType* | `string` | Тип данных |
| *значение* | `string` | Значение |

### <a name="logmessage"></a>log(Message)

Эта функция записывает в журнал сообщение из определяемой пользователем функции.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Сообщение для записи |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Эта функция отправляет настраиваемое уведомление для рассылки.

**Вид**: глобальная функция.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Идентификатор объекта графа. Это может быть идентификатор пространства, датчика, устройства и т. п.|
| *topologyObjectType*  | `string` | Это может быть идентификатор пространства, устройства и т. п.|
| *payload*  | `string` | Полезные данные JSON, отправляемые вместе с уведомлением. |

## <a name="return-types"></a>Возвращаемые типы

Ниже описаны модели ответов, возвращенные из вспомогательных методов клиентского справочника.

### <a name="space"></a>Пробел

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
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

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Имя расширенного свойства |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Эта функция возвращает значение для текущего пространства.

| Параметр  | Type                | Описание  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Имя значения |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Эта функция возвращает исторические значения для текущего пространства.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Имя значения |

#### <a name="notifypayload"></a>Notify(payload)

Эта функция отправляет уведомление с указанными полезными данными.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | полезные данные JSON, которые будут включены в уведомление |

### <a name="device"></a>Устройство

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Методы устройства

#### <a name="parent--space"></a>Parent() ⇒ `space`

Эта функция возвращает родительское пространство текущего устройства.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Эта функция возвращает дочерние датчики текущего устройства.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Эта функция возвращает расширенное свойство и его значение для текущего устройства.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Имя расширенного свойства |

#### <a name="notifypayload"></a>Notify(payload)

Эта функция отправляет уведомление с указанными полезными данными.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | полезные данные JSON, которые будут включены в уведомление |

### <a name="sensor"></a>Датчик

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>Методы датчика

#### <a name="space--space"></a>Space() ⇒ `space`

Эта функция возвращает родительское пространство текущего датчика.

#### <a name="device--device"></a>Device() ⇒ `device`

Эта функция возвращает родительское устройство текущего датчика.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Эта функция возвращает расширенное свойство и его значение для текущего датчика.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Имя расширенного свойства |

#### <a name="value--value"></a>Value() ⇒ `value`

Эта функция возвращает значение для текущего датчика.

#### <a name="history--value"></a>History() ⇒ `value[]`

Эта функция возвращает исторические значения для текущего датчика.

#### <a name="notifypayload"></a>Notify(payload)

Эта функция отправляет уведомление с указанными полезными данными.

| Параметр  | Тип                | Описание  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | полезные данные JSON, которые будут включены в уведомление |

### <a name="value"></a>Значение

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>расширенное свойство

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Следующие шаги

- См. статью [Обработка данных и определяемые пользователем функции](./concepts-user-defined-functions.md).

- См. статью [Создание определяемых пользователем функций в Azure Digital Twins](./how-to-user-defined-functions.md).

- См. статью [Отладка проблем с пользовательскими функциями в Azure Digital Twins](./how-to-diagnose-user-defined-functions.md).
