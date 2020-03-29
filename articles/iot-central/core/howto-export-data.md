---
title: Экспорт ваших данных Azure IoT Central Документы Майкрософт
description: Как экспортировать данные из приложения Azure IoT Central в концентраторы событий Azure, шины служб ы Azure и хранилище Azure Blob
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157914"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Экспорт данных IoT в пункты назначения в Azure

*Эта статья предназначена для администраторов.*

В этой статье описывается, как использовать функцию непрерывного экспорта данных в Azure IoT Central для экспорта данных в **концентраторы Azure Event,** **Azure Service Bus**или **экземпляры хранения Azure Blob.** Данные экспортируются в формате JSON и могут включать телеметрию, информацию об устройстве и шаблонах устройств. Используйте экспортированные данные для:

- Теплый путь идеи и аналитики. Эта опция включает в себя запуск пользовательских правил в Azure Stream Analytics, запуск пользовательских рабочих процессов в приложениях логики Azure или передачу их через функции Azure для преобразования.
- Аналитика холодного пути, такая как обучающие модели в области машинного обучения Azure или долгосрочный анализ тенденций в Microsoft Power BI.

> [!Note]
> С момента включения экспорта непрерывных данных вы получаете только поступающие данные. Сейчас невозможно получить данные за то время, когда непрерывный экспорт данных был отключен. Чтобы сохранить больше исторических данных, включите экспорт непрерывных данных раньше.

## <a name="prerequisites"></a>Предварительные требования

Вы должны быть администратором в приложении IoT Central или иметь разрешения на экспорт данных.

## <a name="set-up-export-destination"></a>Настройка места назначения экспорта

Пункт назначения экспорта должен существовать до настройки непрерывного экспорта данных.

### <a name="create-event-hubs-namespace"></a>Создание пространства имен Центров событий

Если у вас нет существующего пространства имен Event Hubs для экспорта, выполните следующие действия:

1. Создайте [пространство имен Центров событий на портале Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Дополнительные сведения см. в [документации по Центрам событий Azure](../../event-hubs/event-hubs-create.md).

2. Выберите подписку. Вы можете экспортировать данные в другие подписки, которые не находятся в той же подписке, что и ваше приложение IoT Central. В этом случае вы подключаетесь с помощью строки соединения.

3. Создайте концентратор событий в пространстве имен в Центрах событий. Перейдите к пространству имен и выберите **+ Концентратор событий** в верхней части, чтобы создать экземпляр концентратора событий.

### <a name="create-service-bus-namespace"></a>Создание пространства имен Служебной шины

Если у вас нет существующего пространства имен Service Bus для экспорта, выполните следующие действия:

1. Создайте [новое пространство имен сервисного автобуса на портале Azure.](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) Дополнительные сведения см. в [документации по Служебной шине Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Выберите подписку. Вы можете экспортировать данные в другие подписки, которые не находятся в той же подписке, что и ваше приложение IoT Central. В этом случае вы подключаетесь с помощью строки соединения.

3. Перейдите к пространству имен Служебной шины и выберите **+ Очередь** или **+ Раздел** в верхней части, чтобы создать очередь или раздел для экспорта.

При выборе сервисного автобуса в качестве экспортного пункта для очередей и тем не должно быть включено сеансы или дублирование detection. Если любая из этих функций включена, некоторые сообщения не будут поступать в очередь или раздел.

### <a name="create-storage-account"></a>Создание учетной записи хранения

Если у вас нет существующей учетной записи хранения Azure для экспорта, выполните следующие действия:

1. Создайте [учетную запись хранения на портале Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Вы можете узнать больше о создании новых [учетных записей хранения Azure Blob или](https://aka.ms/blobdocscreatestorageaccount) [учетных записей хранения данных Azure Data Lake.](../../storage/blobs/data-lake-storage-quickstart-create-account.md) Экспорт данных может записывать данные только в учетные записи хранения данных, поддерживающих блокировку. Ниже приводится список известных совместимых типов учетных записей хранения: 

    |Уровень производительности|Тип учетной записи|
    |-|-|
    |Standard|Общая цель V2|
    |Standard|Общая цель V1|
    |Standard|Хранилище BLOB-объектов|
    |Premium|Блок Blob хранения|

2. Создайте контейнер в учетной записи хранения. Войдите в свою учетную запись хранения. Выберите **Обзор BLOB-объектов** в разделе **Служба BLOB-объектов**. Выберите **+ Контейнер** в верхней части экрана, чтобы создать контейнер.

## <a name="set-up-continuous-data-export"></a>Настройка экспорта непрерывных данных

Теперь, когда у вас есть пункт назначения для экспорта данных, выполните эти шаги, чтобы создать непрерывный экспорт данных.

1. Войдите в приложение IoT Central.

2. В левой панели выберите **экспорт данных.**

    > [!Note]
    > Если вы не видите экспорт данных в левом стеле, то у вас нет разрешений на настройку экспорта данных в приложении. Обратитесь к администратору, чтобы настроить экспорт данных.

3. Выберите **новую** кнопку в правом верхнем рядом. Выберите один из **концентраторов событий Azure,** **автобус службы Azure**или **хранилище Azure Blob** в качестве места назначения вашего экспорта. Максимальное количество экспорта в одну заявку составляет пять.

    ![Создание непрерывного экспорта данных](media/howto-export-data/new-export-definition.png)

4. В поле списка выпадающих списков выберите **пространство имен event Hubs,** **пространство имен Service Bus,** **пространство имен учетной записи хранилища**или **введите строку подключения.**

    - Вы видите только учетные записи хранения данных, пространства имен событий концентраторов и пространства имен Service Bus в той же подписке, что и приложение IoT Central. Если вы хотите выполнить экспорт за пределы этой подписки, выберите **Enter a connection string** (Ввести строку подключения) и перейдите к шагу 5.
    - Для приложений, созданных с использованием свободного тарифного плана, единственный способ настроить непрерывный экспорт данных — это строка подключения. Приложения, в которых есть бесплатный тарифный план, не имеют связанной подписки Azure.

    ![Создание нового концентратора событий](media/howto-export-data/export-event-hub.png)

5. (Необязательно.) Если вы выбрали **Enter a connection string** (Ввести строку подключения), открывается новое окно, в которое нужно вставить строку подключения. Чтобы получить строку подключения, сделайте следующее:
    - Концентраторы событий или сервисный автобус, перейдите в пространство имен на портале Azure.
        - В **настройках**выберите **политики общего доступа**
        - Выберите значение по умолчанию **RootManageSharedAccessKey** или создайте политику.
        - Скопируйте основную или дополнительную строку подключения.
    - Учетная запись хранилища, перейдите на учетную запись хранения на портале Azure:
        - Под **настройками**выберите **ключи доступа**
        - Копирование строки подключения key1 или строки соединения key2

6. Выберите концентратор событий, очередь, тему или контейнер из окна списка выпадающих.

7. Под **данными для экспорта**выберите типы данных для экспорта, установив тип **On.**

8. Чтобы включить непрерывный экспорт данных, убедитесь, что **включенный** переключатель **включен.** Нажмите кнопку **Сохранить**.

9. Через несколько минут ваши данные отображаетесь в выбранном пункте назначения.

## <a name="export-contents-and-format"></a>Экспортное содержимое и формат

Экспортированные телеметрические данные содержат полноту сообщения, отправленное устройствами в IoT Central, а не только сами значения телеметрии. Данные об экспортированных устройствах содержат изменения в свойствах и метаданных всех устройств, а шаблоны экспортированных устройств содержат изменения ко всем шаблонам устройств.

Для концентраторов событий и сервисного автобуса данные экспортируются в режиме почти реального времени. Данные расположены в свойстве тела и в формате JSON (см. ниже для примеров).

Для Blob Storage данные экспортируются один раз в минуту, при этом каждый файл содержит пакет изменений с момента последнего экспортируемого файла. Экспортированные данные размещаются в трех папках в формате JSON. Пути по умолчанию в вашей учетной записи хранения:

- Телеметрия: _«контейнер»/«приложение-id»/телеметрия/«YYYY»/«ММ»/«ММ»/«Дд»/«мм»/«Файлит»_
- Устройства: _«контейнерный» /(app-id)/устройства/«YYYY»/«ММ»/«Дххх»/«мм»/«Файлиное имя»_
- Шаблоны устройств: _«контейнер»/«приложение-id»/устройствоШаблоны/«YYYY»/«ММ»/«Дд»/хх/мм/»)_

Вы можете просматривать экспортированные файлы на портале Azure, перемещаясь по файлу и выбирая вкладку **Edit blob.**


## <a name="telemetry"></a>Телеметрия

Для концентраторов событий и сервисного автобуса новое сообщение быстро экспортируется после того, как IoT Central получает сообщение с устройства, и каждое экспортируемое сообщение содержит полное сообщение, отправленное устройством в свойстве тела в формате JSON.

Для Blob Storage сообщения раскладываются и экспортируются один раз в минуту. Экспортированные файлы используют тот же формат, что и файлы сообщений, экспортированные [в направлении сообщений IoT Hub](../../iot-hub/tutorial-routing.md) в хранилище каблов. 

> [!NOTE]
> Для blob Storage убедитесь, что ваши `contentType: application/JSON` `contentEncoding:utf-8` устройства `utf-16`отправляют `utf-32`сообщения, которые имеют и (или , ). Например, можно ознакомиться с [документацией Концентратора IoT.](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)

Устройство, отправлявававемое телеметрией, представлено идентификатором устройства (см. следующие разделы). Чтобы получить имена устройств, экспортировать данные устройства и соотнести каждое сообщение с помощью **подключенияDeviceId,** которое соответствует **устройствуИд** сообщения устройства.

Это пример сообщения, полученного в концентраторе событий или очереди service Bus или теме.

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

Это пример записи, экспортируемой на хранение капли:

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

## <a name="devices"></a>Устройства

Каждое сообщение или запись в моментальном снимке представляет одно или несколько изменений в устройстве и его свойствах устройства и облака с момента последнего экспортируемого сообщения. В том числе:

- идентификатор (`id`) устройства в IoT Central;
- имя (`displayName`) устройства;
- Id шаблон устройства в`instanceOf`
- `simulated`флаг, правда, если устройство является смоделированным устройством
- `provisioned`флаг, правда, если устройство было подготовлено
- `approved`флаг, правда, если устройство было одобрено для отправки данных
- Значения свойств
- `properties`включая значения свойств устройств и облачных свойств

Удаленные устройства не экспортируются. В настоящее время в сообщениях экспорта нет никаких индикаторов касательно удаленных устройств.

Для концентраторов событий и сервисного шины сообщения, содержащие данные об устройстве, отправляются в концентратор событий или очередь Service Bus или тему практически в режиме реального времени, так как он отображается в IoT Central. 

Для Blob Storage новый снимок, содержащий все изменения с момента последнего написанного, экспортируется один раз в минуту.

Это пример сообщения об устройствах и данных свойств в концентраторе событий или очереди Service Bus или теме:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
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
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Это пример снимка, содержащего устройства и данные о свойствах в Blob Storage. Экспортированные файлы содержат одну строку на запись.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Шаблоны устройств

Каждое сообщение или запись моментального снимка представляет собой одно или несколько изменений в шаблоне опубликованного устройства с момента последнего экспортируемого сообщения. Информация, отправленная в каждом сообщении или записи включает в себя:

- `id`шаблона устройства, который `instanceOf` соответствует потоку устройств выше
- версию (`displayName`) шаблона устройства;
- Устройство, `capabilityModel` включая его, `interfaces`и телеметрию, свойства и определения команд
- `cloudProperties`Определения
- Переопределения и начальные значения, соотнесенные с`capabilityModel`

Удаленные шаблоны устройств не экспортируются. В настоящее время в сообщениях экспорта нет никаких индикаторов касательно удаленных шаблонов устройств.

Для концентраторов событий и сервисного шины сообщения, содержащие данные шаблонов устройств, отправляются в концентратор событий или очередь Service Bus или тему практически в режиме реального времени, так как он отображается в IoT Central. 

Для Blob Storage новый снимок, содержащий все изменения с момента последнего написанного, экспортируется один раз в минуту.

Это пример сообщения о данных шаблонов устройств в концентраторе событий или очереди Service Bus или теме:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
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

Это пример снимка, содержащего устройства и данные о свойствах в Blob Storage. Экспортированные файлы содержат одну строку на запись.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>Уведомление об изменении формата данных

> [!Note]
> Этот изменение не влияет на формат телеметрических потоков данных. Затрагиваются только потоки данных устройств и шаблонов устройств.

Если в приложении предварительного просмотра есть существующий экспорт данных с включенными *потоками шаблонов* *устройств* и устройств, необходимо обновить экспорт до **30 июня 2020 года.** Это относится к экспорту в Azure Blob Storage, Azure Event Hubs и Azure Service Bus.

Начиная с 3 февраля 2020 года, все новые экспорты в приложениях с устройствами и шаблонами устройств включен будет иметь формат данных, описанный выше. Весь экспорт, созданный до этого, будет оставаться в старом формате данных до 30 июня 2020 года, после чего эти экспортные товары будут автоматически перенесены в новый формат данных. Новый формат данных соответствует [свойству устройства, свойству устройства,](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties) [облачному свойству устройства](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) и объектам [шаблона устройства](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) в общедоступном API IoT Central. [device](https://docs.microsoft.com/rest/api/iotcentral/devices/get) 
 
Для **устройств**– заметные различия между старым форматом данных и новым форматом данных включают:
- `@id`для устройства удаляется, `deviceId` переименовано в`id` 
- `provisioned`флаг добавляется для описания статуса подготовки устройства
- `approved`флаг добавляется для описания состояния утверждения устройства
- `properties`включая свойства устройств и облаков, совпадает с объектами в общедоступном API

Для **шаблонов устройств**заметные различия между старым форматом данных и новым форматом данных включают:

- `@id`для шаблона устройства переименован в`id`
- `@type`для шаблона устройства переименован `types`в , и теперь массив

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Устройства (формат, унизанный по состоянию на 3 февраля 2020 года)
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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Шаблоны устройств (формат, унипраженный по состоянию на 3 февраля 2020 года)
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

Теперь, когда вы знаете, как экспортировать данные в концентраторы событий Azure, Azure Service Bus и Azure Blob Storage, перейдите к следующему шагу:

> [!div class="nextstepaction"]
> [Как создать веб-крючки](./howto-create-webhooks.md)
