---
title: Центр Интернета вещей Azure и служба "Сетка событий" | Документация Майкрософт
description: Используйте службу "Сетка событий Azure" для активации процессов на основе действий, выполняемых в Центре Интернета вещей.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: f08845dbf4168627d0198e81d2092a1fe56c6c89
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733867"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Реагирование на события в Центре Интернета вещей с использованием службы "Сетка событий" для запуска действий

Центр Интернета вещей Azure интегрируется со службой "Сетка событий Azure", чтобы вы могли отправлять оповещения о событиях в другие службы и активировать нисходящие процессы. Настройте свои бизнес-приложения для ожидания передачи данных событий Центра Интернета вещей, чтобы реагировать на критические события более надежным, масштабируемым и безопасным способом. Например создавайте приложение, которое обновляет базу данных, создает билет рабочих и доставляет уведомление по электронной почте каждый раз при регистрации нового устройства Интернета вещей к центру Интернета вещей.

[Сетка событий Azure](../event-grid/overview.md) — это полностью управляемая служба маршрутизации событий, использующая принцип "публикации — подписки". Эта служба поддерживает такие службы Azure, как [Функции Azure](../azure-functions/functions-overview.md) и [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), и может доставлять оповещения о событиях в службы за пределами Azure с помощью веб-перехватчиков. Полный список обработчиков событий, которые поддерживает служба "Сетка событий", см. в статье [An introduction to Azure Event Grid](../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure").

![Архитектура службы "Сетка событий Azure"](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Доступность по регионам

Интеграция со службой "Сетка событий" доступна для центров IoT, расположенных в регионах, где поддерживается эта служба. Все события устройства, за исключением устройств телеметрии являются общедоступными. Событие телеметрии устройства в общедоступной предварительной версии и доступна во всех регионах, за исключением восточной части США, Западная часть США, Западная Европа, [Azure для государственных организаций](/azure/azure-government/documentation-government-welcome), [21Vianet в Azure для Китая](/azure/china/china-welcome), и [Azure для Германии](https://azure.microsoft.com/global-infrastructure/germany/). Наиболее актуальный список регионов см. в статье [Общие сведения о службе "Сетка событий Azure"](../event-grid/overview.md).

## <a name="event-types"></a>Типы событий

Центр Интернета вещей публикует следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Публикуется при регистрации устройства в Центре Интернета вещей. |
| Microsoft.Devices.DeviceDeleted | Публикуется при удалении устройства из Центра Интернета вещей. |
| Microsoft.Devices.DeviceConnected | Публикуется при подключении устройства к Центру Интернета вещей. |
| Microsoft.Devices.DeviceDisconnected | Публикуется при отключении устройства от Центра Интернета вещей. |
| Microsoft.Devices.DeviceTelemetry | Опубликованные при отправке сообщения телеметрии устройства к центру Интернета вещей |

Чтобы настроить события, которые будут публиковаться в каждом Центре Интернета вещей, используйте портал Azure или Azure CLI. Например, ознакомьтесь со статьей [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Отправка уведомлений электронной почты о событиях в Центре Интернета вещей Azure с помощью Logic Apps).

## <a name="event-schema"></a>Схема событий

События Центра Интернета вещей содержат все сведения, необходимые для реагирования на изменения в жизненном цикле устройства. Вы можете идентифицировать событие Центра Интернета вещей, проверив, запускается ли свойство eventType с **Microsoft.Devices**. Дополнительные сведения об использовании свойств событий службы "Сетка событий" см. в статье [Схема событий службы "Сетка событий Azure"](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Схема подключения устройства

В следующем примере показана схема события подключения устройства:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Схема телеметрии устройства

Сообщения телеметрии устройства должен быть в допустимом формате JSON с contentType значение JSON и значение UTF-8 в сообщении contentEncoding [системные свойства](iot-hub-devguide-routing-query-syntax.md#system-properties). Если это значение не задано, центр Интернета вещей будет записывать сообщения в базовый 64 закодированном формате.

Можно обогатить события телеметрии устройства, прежде чем они будут опубликованы в "Сетка событий", выбрав конечную точку в качестве "Сетка событий". Дополнительные сведения см. в разделе [Обзор усовершенствования сообщений](iot-hub-message-enrichments-overview.md).

В следующем примере показана схема события телеметрии устройства:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Схема, созданная устройством

В следующем примере показана схема события создания устройства:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Подробное описание каждого свойства, см. в разделе [схема событий службы "Сетка событий Azure" для центра Интернета вещей](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>События фильтра

Подписки на события центра Интернета вещей можно фильтровать события по типу события, данные и тему, которая является именем устройства.

"Сетка событий" позволяет [фильтрации](../event-grid/event-filtering.md) на содержимое типов, темы и данных события. При создании подписки сетки событий, вы можете подписаться на выбранных событий Интернета вещей. Фильтры тем в службе "Сетка событий Azure" работают на основе соответствия префиксов **начинается с** и суффиксов **оканчивается на**. В фильтре используется оператор `AND`, чтобы события с темой, которая соответствует и префиксу, и суффиксу, доставлялись подписчику.

Тема событий Интернета вещей использует следующий формат:

```json
devices/{deviceId}
```

"Сетка событий" также обеспечивает фильтрацию по атрибутам каждого события, включая содержимое данных. Это позволяет выбрать, какие события доставляются на основе содержимого сообщения телеметрии. См. в разделе [Расширенная фильтрация](../event-grid/event-filtering.md#advanced-filtering) примеры.

Для нетелеметрических событий как DeviceConnected, DeviceDisconnected, DeviceCreated и DeviceDeleted фильтрация "Сетка событий" можно использовать при создании подписки. Для события телеметрии, помимо фильтрации в сетке событий пользователей можно также фильтровать по двойников устройств, свойства сообщения и текст через маршрутизации запросов сообщений. Мы создадим по умолчанию [маршрута](iot-hub-devguide-messages-d2c.md) в центре Интернета вещей на основе подписки сетки событий для данных телеметрии устройства. Это один маршрут может обрабатывать все ваши подписки сетки событий. Чтобы фильтровать сообщения перед отправкой данных телеметрии, вы можете обновить ваш [маршрутизации запроса](iot-hub-devguide-routing-query-syntax.md). Обратите внимание, что маршрутизации запроса могут применяться к тексту сообщения, только если текст JSON.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Ограничения для событий подключения и отключения устройства

Чтобы получить событие подключения и отключения устройства, необходимо открыть связь D2C или C2D для вашего устройства. Если ваше устройство использует протокол MQTT, связь C2D в Центре Интернета вещей будет открыта. AMQP, можно открыть ссылку C2D путем вызова [получать асинхронные API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

Связь D2C открыта, если вы отправляете телеметрию. Если подключение к устройству мерцание, значит, устройство подключается и отключает часто, мы не будет отправлять каждого состояния одного соединения, но будет публиковать состояние соединения, из которых создается моментальный снимок каждую минуту. В случае сбоя Центра Интернета вещей мы опубликуем состояние подключения устройства сразу же после того, как сбой будет устранен. Если устройство отключается во время этого сбоя, событие отключения устройства будет опубликовано в течение 10 минут.

## <a name="tips-for-consuming-events"></a>Советы по потреблению событий

Для работы с приложениями, обрабатывающими события Центра Интернета вещей, нужно следовать таким рекомендациям:

* Можно настроить несколько подписок для маршрутизации событий тот же обработчик событий, поэтому не следует предполагать, что события поступают из определенного источника. Всегда проверяйте тему сообщения, чтобы убедиться, что оно поступает из ожидаемого Центра Интернета вещей.

* Не следует предполагать, что все получаемые события имеют ожидаемые типы. Всегда проверяйте eventType перед обработкой сообщения.

* Сообщения могут прибывать не по порядку или с задержкой. Используйте поле etag, чтобы понять, является ли информация об объектах актуальной созданное устройство или устройство удалено событий.

## <a name="next-steps"></a>Дальнейшие действия

* [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Отправка уведомлений электронной почты о событиях в Центре Интернета вещей Azure с помощью Logic Apps)

* См. дополнительные сведения об [упорядочении событий подключения и отключения устройств](iot-hub-how-to-order-connection-state-events.md).

* [Общие сведения о службе "Сетка событий Azure"](../event-grid/overview.md)

* [Сравнение маршрутизации сообщений со службой "Сетка событий" и без нее для Центра Интернета вещей](iot-hub-event-grid-routing-comparison.md)
