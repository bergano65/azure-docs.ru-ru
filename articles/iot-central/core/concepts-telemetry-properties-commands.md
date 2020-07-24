---
title: Полезные данные телеметрии, свойств и команд в Azure IoT Central | Документация Майкрософт
description: Шаблоны устройств IoT Central Azure позволяют указать данные телеметрии, свойства и команды, которые должны быть реализованы устройством. Сведения о формате данных, которые устройство может обменивать с IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 108a7940084e99348dc8fdfa0143d5c6855599df
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100311"
---
# <a name="telemetry-property-and-command-payloads"></a>Полезные данные телеметрии, свойств и команд

_Эта статья предназначена для разработчиков устройств_

Шаблон устройства в Azure IoT Central — это схема, которая определяет:

* Данные телеметрии, которые устройство отправляет в IoT Central.
* Свойства, с которыми синхронизируется устройство IoT Central.
* Команды, которые IoT Central вызовы на устройстве.

В этой статье описывается, для разработчиков устройств, полезных данных JSON, которые устройства отправляют и получают для телеметрии, свойств и команд, определенных в шаблоне устройства.

В этой статье не описываются все возможные типы полезных данных телеметрии, свойств и команд, но в примерах показаны все ключи.

В каждом примере показан фрагмент модели возможностей устройства (DCM), который определяет тип и примеры полезных данных JSON, чтобы продемонстрировать, как устройство должно взаимодействовать с IoT Central приложением.

JSON-файл, определяющий DCM, использует [язык определения цифровых двойника (дтдл) v1](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/dtdlv1.md). Эта спецификация включает определение `@id` формата свойства.

Пример кода устройства, демонстрирующий некоторые из этих полезных данных, см. в разделе [Создание и подключение клиентского приложения к приложению IOT Central Azure (Node.js)](tutorial-connect-device-nodejs.md) и [Создание и подключение клиентского приложения к учебникам по приложениям Azure IOT Central (Python)](tutorial-connect-device-python.md) .

## <a name="view-raw-data"></a>Просмотр необработанных данных

IoT Central позволяет просматривать необработанные данные, которые устройство отправляет в приложение. Это представление полезно для устранения неполадок, связанных с полезной нагрузкой, отправленной с устройства. Просмотр необработанных данных, отправляемых устройством:

1. Перейдите на устройство со страницы **устройства** .

1. Перейдите на вкладку **необработанные данные** :

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Представление "необработанные данные"":::
    
    В этом представлении можно выбрать отображаемые столбцы и задать диапазон времени для просмотра. В столбце **несмоделированные данные** отображаются данные с устройства, которые не соответствуют ни одному из определений свойств или телеметрии в шаблоне устройства.


## <a name="telemetry"></a>Телеметрия

### <a name="primitive-types"></a>Примитивные типы

В этом разделе приведены примеры типов данных телеметрии, которые передаются в приложение IoT Central в потоке устройства.

В следующем фрагменте кода из DCM показано определение `boolean` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере:

```json
{ "BooleanTelemetry": true }
```

В следующем фрагменте кода из DCM показано определение `string` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

В следующем фрагменте кода из DCM показано определение `integer` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере:

```json
{ "IntegerTelemetry": 23 }
```

В следующем фрагменте кода из DCM показано определение `double` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере:

```json
{ "DoubleTelemetry": 56.78 }
```

В следующем фрагменте кода из DCM показано определение `dateTime` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит так, как в следующем примере — типы должны соответствовать стандарту `DateTime` ISO 8061:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

В следующем фрагменте кода из DCM показано определение `duration` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере: длительность должна соответствовать стандарту ISO 8601.

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Сложные типы

В этом разделе приведены примеры сложных типов телеметрии, которые передаются в приложение IoT Central в потоке устройства.

В следующем фрагменте кода из DCM показано определение `geopoint` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере. IoT Central отображает значение в виде ПИН-кода на карте:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

В следующем фрагменте кода из DCM показано определение `Enum` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере. Возможные значения: `0` , `1` и `2` , которые отображаются в IOT Central как `Item1` , `Item2` и `Item3` :

```json
{ "EnumTelemetry": 1 }
```

В следующем фрагменте кода из DCM показано определение `Object` типа телеметрии. Этот объект содержит три поля с типами `dateTime` , `integer` и `Enum` :

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@id": "<element id>",
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере. `DateTime`типы должны соответствовать стандартам ISO 8061. Возможные значения для `Property3` : `0` , `1` и, которые отображаются в IOT Central как `Item1` , `Item2` и `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

В следующем фрагменте кода из DCM показано определение `vector` типа телеметрии.

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

Клиент устройства должен отправить данные телеметрии как JSON, который выглядит, как в следующем примере:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Типы событий и состояний

В этом разделе приведены примеры событий телеметрии и состояний, которые устройство отправляет в приложение IoT Central.

В следующем фрагменте кода из DCM показано определение `integer` типа события:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

Клиент устройства должен отправить данные события как JSON, который выглядит, как в следующем примере:

```json
{ "IntegerEvent": 74 }
```

В следующем фрагменте кода из DCM показано определение `integer` типа состояния:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

Клиент устройства должен отправить состояние как JSON, как показано в следующем примере. Возможные значения целочисленного состояния `1` `2` :, или `3` .

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Свойства

> [!NOTE]
> Форматы полезных данных для свойств применяются к приложениям, созданным в или после 07/14/2020.

### <a name="primitive-types"></a>Примитивные типы

В этом разделе приведены примеры типов простых свойств, которые устройство отправляет в приложение IoT Central.

В следующем фрагменте кода из DCM показано определение `boolean` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean"
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства:

```json
{ "BooleanProperty": false }
```

В следующем фрагменте кода из DCM показано определение `boolean` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long"
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства:

```json
{ "LongProperty": 439 }
```

В следующем фрагменте кода из DCM показано определение `date` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date"
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства. `Date`типы должны соответствовать стандартам ISO 8061:

```json
{ "DateProperty": "2020-05-17" }
```

В следующем фрагменте кода из DCM показано определение `duration` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration"
}
```

Клиент устройства должен отправить полезные данные JSON, которые выглядят так, как в следующем примере, как сообщаемое свойство в двойникае устройства. Длительность должна соответствовать требованиям ISO 8601:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

В следующем фрагменте кода из DCM показано определение `float` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float"
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства:

```json
{ "FloatProperty": 1.9 }
```

В следующем фрагменте кода из DCM показано определение `string` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string"
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Сложные типы

В этом разделе приведены примеры сложных типов свойств, которые устройство отправляет в приложение IoT Central.

В следующем фрагменте кода из DCM показано определение `geopoint` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint"
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

В следующем фрагменте кода из DCM показано определение `Enum` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства. Возможные значения: `0` , `1` и, которые отображаются в IOT Central как `Item1` , `Item2` и `Item3` :

```json
{ "EnumProperty": 1 }
```

В следующем фрагменте кода из DCM показано определение `Object` типа свойства. Этот объект содержит два поля с типами `string` и `integer` :

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

В следующем фрагменте кода из DCM показано определение `vector` типа свойства:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector"
}
```

Клиент устройства должен отправить полезные данные JSON, как в следующем примере, как сообщаемое свойство в двойникае устройства:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Типы свойств с возможностью записи

В этом разделе приводятся примеры записываемых типов свойств, получаемых устройством от IoT Central приложения.

IoT Central ждет от устройства отклика на записываемые обновления свойств. Ответное сообщение должно включать `ac` поля и `av` . Поле `ad` является необязательным. Примеры см. в следующих фрагментах кода.

`ac`числовое поле, использующее значения из следующей таблицы:

| Значение | Метка | Описание |
| ----- | ----- | ----------- |
| `'ac': 200` | Завершено | Операция изменения свойства успешно завершена. |
| `'ac': 202`ни`'ac': 201` | Ожидание | Операция изменения свойства находится в состоянии ожидания или выполняется |
| `'ac': 4xx` | Ошибка | Запрошенное изменение свойства недопустимо или содержит ошибку |
| `'ac': 5xx` | Ошибка | Произошла непредвиденная ошибка устройства при обработке запрошенного изменения. |

`av`номер версии, отправляемый на устройство.

`ad`— Это описание строки параметра.

В следующем фрагменте кода из DCM показано определение типа записываемого `string` Свойства.

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

Устройство получает следующие полезные данные из IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

Устройство должно отправить следующие полезные данные JSON в IoT Central после обработки обновления. Это сообщение включает номер версии исходного обновления, полученного от IoT Central. Когда IoT Central получает это сообщение, оно помечает свойство как **синхронизированное** в пользовательском интерфейсе:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

В следующем фрагменте кода из DCM показано определение типа записываемого `Enum` Свойства.

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Устройство получает следующие полезные данные из IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

Устройство должно отправить следующие полезные данные JSON в IoT Central после обработки обновления. Это сообщение включает номер версии исходного обновления, полученного от IoT Central. Когда IoT Central получает это сообщение, оно помечает свойство как **синхронизированное** в пользовательском интерфейсе:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Команды

### <a name="synchronous-command-types"></a>Типы синхронных команд

В следующем фрагменте кода из DCM показано определение синхронной команды, не имеющей параметров и не ожидающей возврата устройством каких-либо данных:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "displayName": {
    "en": "SynchronousCommandBasic"
  },
  "name": "SynchronousCommandBasic"
}
```

Устройство получает пустые полезные данные в запросе и должно возвращать пустую полезную нагрузку в ответе с `200` кодом HTTP-ответа, чтобы указать на успешное выполнение.

В следующем фрагменте кода из DCM показано определение синхронной команды, имеющей целочисленный параметр и ожидающего, что устройство возвращает целочисленное значение:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "SynchronousCommandSimple"
  },
  "name": "SynchronousCommandSimple"
}
```

Устройство получает целочисленное значение в качестве полезных данных запроса. Устройство должно вернуть целочисленное значение в качестве полезных данных ответа с `200` кодом HTTP-ответа, чтобы указать на успешное выполнение.

В следующем фрагменте кода из DCM показано определение синхронной команды, имеющей параметр объекта и ожидающей, что устройство возвращает объект. В этом примере оба объекта имеют целочисленные и строковые поля:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "SynchronousCommandComplex"
  },
  "name": "SynchronousCommandComplex"
}
```

В следующем фрагменте кода показаны примеры полезных данных запроса, отправленные на устройство:

```json
{ "Field1": 56, "Field2": "A string value" }
```

В следующем фрагменте кода показаны примеры полезных данных ответа, отправленных с устройства. Используйте `200` код ответа HTTP, чтобы указать на успешное выполнение:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="asynchronous-command-types"></a>Асинхронные типы команд

В следующем фрагменте кода из DCM показано определение асинхронной команды. Команда имеет целочисленный параметр и ждет, пока устройство возвратит целочисленное значение:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "asynchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "AsynchronousCommandSimple"
  },
  "name": "AsynchronousCommandSimple"
}
```

Устройство получает целочисленное значение в качестве полезных данных запроса. Устройство должно вернуть пустые полезные данные ответа с `202` кодом HTTP-ответа, чтобы указать, что устройство приняло запрос на асинхронную обработку.

Когда устройство закончит обработку запроса, оно должно отправить свойство в IoT Central, как показано в следующем примере. Имя свойства должно совпадать с именем команды:

```json
{
  "AsynchronousCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы уже узнали о шаблонах устройств, мы предлагаем вам ознакомиться с разработкой " [Подключение к Azure IOT Central](./concepts-get-connected.md) ", чтобы узнать больше о том, как регистрировать устройства с IOT Central и как IOT Central защищает подключения устройств.
