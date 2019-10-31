---
title: Экспорт данных IoT Central Azure | Документация Майкрософт
description: Экспорт данных из приложения IoT Central Azure в концентраторы событий Azure, служебную шину Azure и хранилище BLOB-объектов Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 118d2b42bc14a943aa3fa60b34aa1c151d5dea4c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176303"
---
# <a name="export-your-azure-iot-central-datapreview-features"></a>Экспорт данных IoT Central Azure (Предварительная версия компонентов)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Этот раздел предназначен для администраторов.*

В этой статье описывается, как использовать функцию непрерывного экспорта данных в Azure IoT Central для экспорта данных в концентраторы событий Azure, служебную шину Azure или экземпляры хранилища BLOB-объектов Azure. Данные экспортируются в формате JSON и могут включать данные телеметрии, сведения об устройстве и сведения о шаблонах устройств. Используйте экспортированные данные для:

- Подробные сведения о теплом пути и аналитика. Этот параметр включает в себя запуск настраиваемых правил в Azure Stream Analytics, запуск пользовательских рабочих процессов в Azure Logic Apps или их передачу с помощью функций Azure для преобразования.
- Аналитика холодного пути, например моделей обучения в Машинное обучение Azure или долгосрочном анализе тенденций в Microsoft Power BI.

> [!Note]
> С момента включения экспорта непрерывных данных вы получаете только поступающие данные. Сейчас невозможно получить данные за то время, когда непрерывный экспорт данных был отключен. Чтобы сохранить больше исторических данных, включите экспорт непрерывных данных раньше.

## <a name="prerequisites"></a>Технические условия

Вы должны быть администратором в своем приложении IoT Central.

## <a name="set-up-export-destination"></a>Настройка места назначения экспорта

Назначение экспорта должно существовать до настройки непрерывного экспорта данных.

### <a name="create-event-hubs-namespace"></a>Создание пространства имен Центров событий

Если у вас нет пространства имен концентраторов событий для экспорта, выполните следующие действия.

1. Создайте [пространство имен Центров событий на портале Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Дополнительные сведения см. в [документации по Центрам событий Azure](../../event-hubs/event-hubs-create.md).

2. Выберите подписку. Вы можете экспортировать данные в другие подписки, которые не входят в ту же подписку, что и приложение IoT Central с оплатой по мере использования. В этом случае вы подключаетесь, используя строку подключения.

3. Создайте концентратор событий в пространстве имен в Центрах событий. Перейдите к пространству имен и выберите **+ Концентратор событий** в верхней части, чтобы создать экземпляр концентратора событий.

### <a name="create-service-bus-namespace"></a>Создание пространства имен Служебной шины

Если у вас нет пространства имен служебной шины для экспорта, выполните следующие действия.

1. Создайте [новое пространство имен служебной шины в портал Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Дополнительные сведения см. в [документации по Служебной шине Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Выберите подписку. Вы можете экспортировать данные в другие подписки, которые не входят в ту же подписку, что и приложение IoT Central с оплатой по мере использования. В этом случае вы подключаетесь, используя строку подключения.

3. Перейдите к пространству имен Служебной шины и выберите **+ Очередь** или **+ Раздел** в верхней части, чтобы создать очередь или раздел для экспорта.

Если в качестве назначения экспорта выбрана служебная шина, то очереди и разделы не должны включать сеансы или обнаружение повторяющихся записей. Если любая из этих функций включена, некоторые сообщения не будут поступать в очередь или раздел.

### <a name="create-storage-account"></a>Создание учетной записи хранения

Если у вас нет существующей учетной записи хранения Azure для экспорта, выполните следующие действия.

1. Создайте [учетную запись хранения на портале Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Вы можете узнать больше о создании новых [учетных записей хранения BLOB-объектов Azure](https://aka.ms/blobdocscreatestorageaccount) или [учетных записей хранения Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Если вы решили экспортировать данные в учетную запись хранения Azure Data Lake Storage v2, необходимо выбрать **блобстораже** в качестве **типа учетной записи**.
    - Вы можете экспортировать данные в учетные записи хранения в подписках, отличных от подписки с оплатой по мере использования IoT Central приложении. В этом случае вы подключаетесь с использованием строки подключения.

2. Создайте контейнер в учетной записи хранения. Войдите в свою учетную запись хранения. Выберите **Обзор BLOB-объектов** в разделе **Служба BLOB-объектов**. Выберите **+ Контейнер** в верхней части экрана, чтобы создать контейнер.

## <a name="set-up-continuous-data-export"></a>Настройка экспорта непрерывных данных

Теперь, когда у вас есть место назначения для экспорта данных, выполните следующие действия, чтобы настроить непрерывный экспорт данных.

1. Войдите в приложение IoT Central.

2. На левой панели выберите **Экспорт данных**.

    > [!Note]
    > Если вы не видите элемент Экспорт данных в левой области, вы не являетесь администратором в приложении. Обратитесь к администратору, чтобы настроить экспорт данных.

3. Нажмите кнопку **+ создать** в правом верхнем углу. Выберите в качестве места назначения для экспорта один из **концентраторов событий Azure**, **служебную шину Azure**или **хранилище BLOB-объектов Azure** . Максимальное число экспортов на приложение равно пяти.

    ![Создание непрерывного экспорта данных](media/howto-export-data-pnp/export-new2.png)

4. В раскрывающемся списке выберите **пространство имен концентраторов событий**, **пространство имен служебной шины**, **пространство имен учетной записи хранения**или **введите строку подключения**.

    - Учетные записи хранения, пространства имен концентраторов событий и пространства имен служебной шины отображаются только в той же подписке, что и приложение IoT Central. Если вы хотите выполнить экспорт за пределы этой подписки, выберите **Enter a connection string** (Ввести строку подключения) и перейдите к шагу 5.
    - В течение семи пробных версий приложений единственным способом настройки непрерывного экспорта данных является строка подключения. В 7-дневных пробных приложениях нет связанной подписки Azure.

    ![Создать новый концентратор событий](media/howto-export-data-pnp/export-eh.png)

5. (Необязательно.) Если вы выбрали **Enter a connection string** (Ввести строку подключения), открывается новое окно, в которое нужно вставить строку подключения. Чтобы получить строку подключения, сделайте следующее:
    - Концентраторы событий или служебная шина, перейдите к пространству имен в портал Azure.
        - В разделе **Параметры**выберите **политики общего доступа** .
        - Выберите значение по умолчанию **RootManageSharedAccessKey** или создайте политику.
        - Скопируйте основную или дополнительную строку подключения.
    - Учетная запись хранения, перейдите к учетной записи хранения в портал Azure:
        - В разделе **Параметры**выберите **ключи доступа** .
        - Скопируйте строку подключения key1 или строку подключения Key2.

6. В раскрывающемся списке выберите концентратор событий, очередь, раздел или контейнер.

7. В разделе **данные для экспорта**выберите типы данных для экспорта, задав для параметра Тип значение **вкл**.

8. Чтобы включить непрерывный экспорт данных, убедитесь, **что включен режим** **экспорта данных** . Щелкните **Сохранить**.

9. Через несколько минут данные отобразятся в выбранном месте назначения.

## <a name="data-format"></a>Формат данных

Данные экспортируются в концентратор событий или в очередь или раздел служебной шины практически в реальном времени.

Данные экспортируются в учетную запись хранения один раз в минуту, при этом каждый файл содержит пакет изменений с момента последнего экспортированного файла. Экспортированные данные помещаются в три папки в формате JSON. Пути по умолчанию в вашей учетной записи хранения:

- Данные телеметрии: _{Container}/{АПП-ИД}/телеметри/{ИИИИ}/{мм}/{дд}/{ХХ}/{мм}/{филенаме}_
- Устройства: _{Container}/{АПП-ИД}/девицес/{ИИИИ}/{мм}/{дд}/{ХХ}/{мм}/{филенаме}_
- Шаблоны устройств: _{Container}/{АПП-ИД}/девицетемплатес/{ИИИИ}/{мм}/{дд}/{ХХ}/{мм}/{филенаме}_

Вы можете просмотреть экспортированные файлы в портал Azure, перейдя к файлу и выбрав вкладку **Edit BLOB (изменить большой двоичный объект** ).

Экспортированные данные телеметрии содержат все сообщения, отправленные в IoT Central, а не только сами значения телеметрии. Данные экспортированных устройств содержат изменения свойств и метаданных всех устройств, а экспортированные шаблоны устройств содержат изменения во всех шаблонах устройств. Экспортированные данные находятся в свойстве body и имеют формат JSON.

### <a name="telemetry"></a>Телеметрия

Новое сообщение экспортируется сразу после того, как IoT Central получает сообщения с устройства.

- Каждое экспортированное сообщение в концентраторах событий и служебной шине содержит полное сообщение, отправленное устройством в свойстве body в формате JSON.
- Экспортированные файлы в хранилище BLOB-объектов используют тот же формат, что и файлы сообщений, экспортированные [службой маршрутизации сообщений центра Интернета вещей](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) в хранилище BLOB-объектов. Убедитесь, что устройства отправляют сообщения с `contentType: application/JSON` и `contentEncoding:utf-8` (или `utf-16`, `utf-32`). Пример см. в документации по центру [Интернета вещей](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

Устройства, отправляющие данные телеметрии, представлены идентификаторами устройств (см. следующие разделы). Чтобы получить имена устройств, экспортируйте данные устройства и сопоставьте каждое сообщение с помощью **коннектиондевицеид** , который соответствует идентификатору **deviceId** сообщения устройства.

В следующем примере показано сообщение о данных телеметрии, полученных в концентраторе событий или в очереди или разделе служебной шины.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

В следующем примере показана запись в формате JSON в хранилище BLOB-объектов:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Устройства

Сообщения, содержащие данные устройств, отправляются в концентратор событий либо очередь или раздел служебной шины каждые несколько минут. Новый моментальный снимок записывается в хранилище BLOB-объектов один раз в минуту. Каждое сообщение или моментальный снимок содержит данные о:

- новых устройствах, которые были добавлены;
- Устройства с измененными значениями свойств

Каждое сообщение или запись в моментальном снимке представляет одно или несколько изменений устройства с момента последнего экспортированного сообщения. Сведения включают:

- идентификатор (`@id`) устройства в IoT Central;
- имя (`name`) устройства;
- идентификатор устройства (`deviceId`) из [Службы подготовки устройств](/azure/iot-central/core/howto-connect-nodejs);
- сведения о шаблоне устройства;
- Значения свойств

Устройства, удаленные с момента создания последнего пакета сообщений, не экспортируются. В настоящее время в сообщениях экспорта нет никаких индикаторов касательно удаленных устройств.

Шаблон устройства, к которому принадлежит каждое устройство, представлен идентификатором шаблона устройства. Чтобы получить имя шаблона устройства, обязательно экспортируйте и данные шаблона устройства.

В следующем примере показано сообщение с данными устройства в концентраторе событий, очереди или разделе служебной шины:

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Экспортируемые файлы содержат одну строку для каждой записи. В следующем примере показана запись в формате JSON.

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Шаблоны устройств

Сообщения, содержащие данные шаблонов устройств, отправляются в концентратор событий, очередь или раздел служебной шины каждые несколько минут. Новый моментальный снимок записывается один раз в минуту в хранилище BLOB-объектов. Таким образом, каждые несколько минут пакет сообщений поступает с данными о:

Сообщения, содержащие данные шаблона устройства, отправляются в концентратор событий или в очередь или раздел служебной шины каждые несколько минут. Новый моментальный снимок записывается в хранилище BLOB-объектов один раз в минуту. Каждое сообщение или моментальный снимок содержит данные о:

- Новые добавленные или обновленные шаблоны устройств
- Шаблоны устройств с измененными моделями возможностей, облачными свойствами, переопределениями и начальными значениями

Каждое сообщение или запись моментального снимка представляет одно или несколько изменений шаблона устройства с момента последнего экспортированного сообщения. Данные, отправляемые в каждое сообщение или запись, включают:

- версию (`@id`) шаблона устройства;
- версию (`name`) шаблона устройства;
- версию (`version`) шаблона устройства;
- Устройство `capabilityModel` включая его `interfaces`, а также определения телеметрии, свойств и команд
- определения `cloudProperties`
- Переопределения и начальные значения, встроенные с `capabilityModel`

Шаблоны устройств, удаленные с момента создания последнего пакета сообщений, не экспортируются. В настоящее время в сообщениях экспорта нет никаких индикаторов касательно удаленных шаблонов устройств.

В следующем примере показано сообщение шаблона устройства в концентраторе событий или в очереди или разделе служебной шины:

```json
{
  "body":{
    "@id":"<template-id>",
    "@type":"DeviceModelDefinition",
    "displayName":"Airbox",
    "capabilityModel":{
      "@id":"<id>",
      "@type":"CapabilityModel",
      "implements":[
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"EnvironmentalSensor",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "comment":"Requires temperature and humidity sensors.",
            "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
            "displayName":"Environmental Sensor",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current temperature on the device",
                "displayName":"Temperature",
                "name":"temp",
                "schema":"double",
                "unit":"Units/Temperature/celsius",
                "valueDetail":{
                  "@id":"<id>",
                  "@type":"ValueDetail/NumberValueDetail",
                  "minValue":{
                    "@value":"50"
                  }
                },
                "visualizationDetail":{
                  "@id":"<id>",
                  "@type":"VisualizationDetail"
                }
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current humidity on the device",
                "displayName":"Humidity",
                "name":"humid",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current PM2.5 on the device",
                "displayName":"PM2.5",
                "name":"pm25",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"T&H Sensor Model Name",
                "displayName":"T&H Sensor Model",
                "name":"thsensormodel",
                "schema":"string"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"PM2.5 Sensor Model Name",
                "displayName":"PM2.5 Sensor Model",
                "name":"pm25sensormodel",
                "schema":"string"
              }
            ]
          }
        },
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"urn_azureiot_DeviceManagement_DeviceInformation",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "displayName":"Device information",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
                "displayName":"Total storage",
                "name":"totalStorage",
                "displayUnit":"kilobytes",
                "schema":"long"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
                "displayName":"Total memory",
                "name":"totalMemory",
                "displayUnit":"kilobytes",
                "schema":"long"
              }
            ]
          }
        }
      ],
      "displayName":"AAEONAirbox52"
    },
    "solutionModel":{
      "@id":"<id>",
      "@type":"SolutionModel",
      "cloudProperties":[
        {
          "@id":"<id>",
          "@type":"CloudProperty",
          "displayName":"Color",
          "name":"Color",
          "schema":"string",
          "valueDetail":{
            "@id":"<id>",
            "@type":"ValueDetail/StringValueDetail"
          },
          "visualizationDetail":{
            "@id":"<id>",
            "@type":"VisualizationDetail"
          }
        }
      ]
    },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Экспортируемые файлы содержат одну строку для каждой записи. В следующем примере показана запись в формате JSON.

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знаете, как экспортировать данные в Центры событий Azure и Служебную шину Azure, переходите к следующему шагу:

> [!div class="nextstepaction"]
> [Активация Функций Azure с помощью веб-перехватчиков в Azure IoT Central](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
