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
ms.openlocfilehash: a2c49a6ba269321d1903565ace3ebaae3f3b917e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673859"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Реагирование на события в Центре Интернета вещей с использованием службы "Сетка событий" для запуска действий

Центр Интернета вещей Azure интегрируется со службой "Сетка событий Azure", чтобы вы могли отправлять оповещения о событиях в другие службы и активировать нисходящие процессы. Настройте свои бизнес-приложения для ожидания передачи данных событий Центра Интернета вещей, чтобы реагировать на критические события более надежным, масштабируемым и безопасным способом. Например создавайте приложение, которое обновляет базу данных, создает билет рабочих и доставляет уведомление по электронной почте каждый раз при регистрации нового устройства Интернета вещей к центру Интернета вещей. 

[Сетка событий Azure](../event-grid/overview.md) — это полностью управляемая служба маршрутизации событий, использующая принцип "публикации — подписки". Эта служба поддерживает такие службы Azure, как [Функции Azure](../azure-functions/functions-overview.md) и [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), и может доставлять оповещения о событиях в службы за пределами Azure с помощью веб-перехватчиков. Полный список обработчиков событий, которые поддерживает служба "Сетка событий", см. в статье [An introduction to Azure Event Grid](../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure"). 

![Архитектура службы "Сетка событий Azure"](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Доступ по регионам

Интеграция со службой "Сетка событий" доступна для центров IoT, расположенных в регионах, где поддерживается эта служба. Наиболее актуальный список регионов см. в статье [Общие сведения о службе "Сетка событий Azure"](../event-grid/overview.md). 

## <a name="event-types"></a>Типы событий

Центр Интернета вещей публикует следующие типы событий: 

| Тип события | ОПИСАНИЕ |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Публикуется при регистрации устройства в Центре Интернета вещей. |
| Microsoft.Devices.DeviceDeleted | Публикуется при удалении устройства из Центра Интернета вещей. |
| Microsoft.Devices.DeviceConnected | Публикуется при подключении устройства к Центру Интернета вещей. |
| Microsoft.Devices.DeviceDisconnected | Публикуется при отключении устройства от Центра Интернета вещей. |

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

Подписки событий Центра Интернета вещей могут фильтровать события по типу событий и имени устройства. Фильтры тем в службе "Сетка событий Azure" работают на основе соответствия префиксов **начинается с** и суффиксов **оканчивается на**. В фильтре используется оператор `AND`, чтобы события с темой, которая соответствует и префиксу, и суффиксу, доставлялись подписчику. 

Тема событий Интернета вещей использует следующий формат:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Ограничения для событий подключения и отключения устройства

Чтобы получить событие подключения и отключения устройства, необходимо открыть связь D2C или C2D для вашего устройства. Если ваше устройство использует протокол MQTT, связь C2D в Центре Интернета вещей будет открыта. AMQP, можно открыть ссылку C2D путем вызова [получать асинхронные API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). 

Связь D2C открыта, если вы отправляете телеметрию. Если подключение к устройству мерцание, значит, устройство подключается и отключает часто, мы не будет отправлять каждого состояния одного соединения, но будет публиковать состояние соединения, помещать каждую минуту. В случае сбоя Центра Интернета вещей мы опубликуем состояние подключения устройства сразу же после того, как сбой будет устранен. Если устройство отключается во время этого сбоя, событие отключения устройства будет опубликовано в течение 10 минут.

## <a name="tips-for-consuming-events"></a>Советы по потреблению событий

Для работы с приложениями, обрабатывающими события Центра Интернета вещей, нужно следовать таким рекомендациям:

* Можно настроить несколько подписок для маршрутизации событий тот же обработчик событий, поэтому не следует предполагать, что события поступают из определенного источника. Всегда проверяйте тему сообщения, чтобы убедиться, что оно поступает из ожидаемого Центра Интернета вещей. 

* Не следует предполагать, что все получаемые события имеют ожидаемые типы. Всегда проверяйте eventType перед обработкой сообщения.

* Сообщения могут прибывать не по порядку или с задержкой. Используйте поле ETag, чтобы понять, является ли информация об объектах актуальной.

## <a name="next-steps"></a>Дальнейшие действия

* [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Отправка уведомлений электронной почты о событиях в Центре Интернета вещей Azure с помощью Logic Apps)
* См. дополнительные сведения об [упорядочении событий подключения и отключения устройств](iot-hub-how-to-order-connection-state-events.md).
* [Общие сведения о службе "Сетка событий Azure"](../event-grid/overview.md)
* [Сравнение маршрутизации сообщений со службой "Сетка событий" и без нее для Центра Интернета вещей](iot-hub-event-grid-routing-comparison.md)
