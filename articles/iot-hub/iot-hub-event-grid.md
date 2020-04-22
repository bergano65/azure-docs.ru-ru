---
title: Центр Интернета вещей Azure и служба "Сетка событий" | Документация Майкрософт
description: Используйте службу "Сетка событий Azure" для активации процессов на основе действий, выполняемых в Центре Интернета вещей.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a67d90a0888c39938f07c294f8e161ce98fd945a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732498"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Реагирование на события в Центре Интернета вещей с использованием службы "Сетка событий" для запуска действий

Центр Интернета вещей Azure интегрируется со службой "Сетка событий Azure", чтобы вы могли отправлять оповещения о событиях в другие службы и активировать нисходящие процессы. Настройте свои бизнес-приложения для ожидания передачи данных событий Центра Интернета вещей, чтобы реагировать на критические события более надежным, масштабируемым и безопасным способом.Например, создайте приложение, которое обновляет базу данных, создает рабочий билет и доставляет уведомление по электронной почте каждый раз, когда новое устройство IoT зарегистрировано в вашем концентраторе IoT.

[Сетка событий Azure](../event-grid/overview.md) — это полностью управляемая служба маршрутизации событий, использующая принцип "публикации — подписки". Эта служба поддерживает такие службы Azure, как [Функции Azure](../azure-functions/functions-overview.md) и [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), и может доставлять оповещения о событиях в службы за пределами Azure с помощью веб-перехватчиков. Полный список обработчиков событий, которые поддерживает служба "Сетка событий", см. в статье [An introduction to Azure Event Grid](../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure").

![Архитектура службы "Сетка событий Azure"](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Доступность по регионам

Интеграция со службой "Сетка событий" доступна для центров IoT, расположенных в регионах, где поддерживается эта служба. Наиболее актуальный список регионов см. в статье [Общие сведения о службе "Сетка событий Azure"](../event-grid/overview.md).

## <a name="event-types"></a>Типы событий

Центр Интернета вещей публикует следующие типы событий:

| Тип события | Описание |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Публикуется при регистрации устройства в Центре Интернета вещей. |
| Microsoft.Devices.DeviceDeleted | Публикуется при удалении устройства из Центра Интернета вещей. |
| Microsoft.Devices.DeviceConnected | Публикуется при подключении устройства к Центру Интернета вещей. |
| Microsoft.Devices.DeviceDisconnected | Публикуется при отключении устройства от Центра Интернета вещей. |
| Microsoft.Devices.DeviceTelemetry | Опубликовано при отправке телеметрического сообщения устройства в концентратор IoT |

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

Телеметрическое сообщение устройства должно быть в действительном формате JSON с набором contentType для **приложения/json** и contentEncoding, установленным на **UTF-8** в [свойствах системы](iot-hub-devguide-routing-query-syntax.md#system-properties)сообщений. Оба этих свойства являются нечувствительными к случаю. Если кодирование содержимого не установлено, то IoT Hub будет писать сообщения в закодированном формате base 64.

Вы можете обогатить события телеметрии устройства до их публикации в Event Grid, выбрав конечную точку в качестве сетки событий. Для получения дополнительной [информации](iot-hub-message-enrichments-overview.md)см.

Следующий пример показывает схему события телеметрии устройства:

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

Подробное описание каждого свойства приведено в [схеме событий Azure Event Grid для Концентратора IoT.](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>События фильтра

Подписки на события IoT Hub могут фильтровать события в зависимости от типа события, содержимого данных и предмета, то есть имени устройства.

Event Grid позволяет [фильтровать](../event-grid/event-filtering.md) типы событий, предметы и содержимое данных. При создании подписки Event Grid можно подписаться на выбранные события IoT. Фильтры тем в службе "Сетка событий Azure" работают на основе соответствия префиксов **начинается с** и суффиксов **оканчивается на**. В фильтре используется оператор `AND`, чтобы события с темой, которая соответствует и префиксу, и суффиксу, доставлялись подписчику.

Тема событий Интернета вещей использует следующий формат:

```json
devices/{deviceId}
```

Event Grid также позволяет фильтровать атрибуты каждого события, включая содержимое данных. Это позволяет выбрать, какие события доставляются на основе содержимого телеметрического сообщения. Пожалуйста, [ознакомьтесь с расширенной фильтрацией](../event-grid/event-filtering.md#advanced-filtering) для просмотра примеров. Для фильтрации на телеметрическом телесете необходимо установить содержимоеType для **приложения/json** и contentEncoding к **UTF-8** в [свойствах системы](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)сообщений. Оба этих свойства являются нечувствительными к случаю.

Для нетелеметрических событий, таких как DeviceConnected, DeviceDisconnected, DeviceCreated и DeviceDeleted, фильтрация сетки событий может быть использована при создании подписки. Для событий телеметрии, в дополнение к фильтрации в Event Grid, пользователи могут также фильтровать на близнецах устройств, свойствах сообщений и теле через запрос на передачу сообщений. 

Когда вы подписываетесь на события телеметрии через Event Grid, IoT Hub создает маршрут сообщения по умолчанию для отправки сообщений исходного типа данных в Event Grid. Для получения дополнительной информации о разгроме [сообщений](iot-hub-devguide-messages-d2c.md)см. Этот маршрут будет виден на портале под IoT Hub > маршрутизируя сообщения. Только один маршрут к Event Grid создается независимо от количества подписок EG, созданных для телеметрических событий. Таким образом, если вам нужно несколько подписок с различными фильтрами, вы можете использовать оператора OR в этих запросах на одном маршруте. Создание и удаление маршрута контролируется через подписку телеметрических событий через Event Grid. Нельзя создавать или удалять маршрут в сетку событий с помощью маршрутизации концентратора IoT.

Для фильтрации сообщений перед отправкой телеметрических данных можно обновить [запрос на выгоду.](iot-hub-devguide-routing-query-syntax.md) Обратите внимание, что запрос на разгром может быть применен к телу сообщения только в том случае, если тело является JSON. Необходимо также установить содержимоеType для **приложения/json** и contentEncoding к **UTF-8** в [свойствах системы](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)сообщений.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Ограничения для событий подключения и отключения устройства

Для получения событий состояния состояния соединения устройства устройство должно сделать либо 'D2C Отправить телеметрию' или 'C2D Получить сообщение' операция с Iot Hub. Однако обратите внимание, что если устройство использует протокол АМЗП для подключения к Iot Hub, рекомендуется, чтобы они сделали операцию 'C2D Receive Message', в противном случае их уведомления о состоянии соединения могут быть задержаны на несколько минут. Если ваше устройство использует протокол MQTT, связь C2D в Центре Интернета вещей будет открыта. Для АМЗП вы можете открыть ссылку C2D, позвонив в [Receive Async API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)для IoT Hub C sDK или [клиента устройства для AM-P.](iot-hub-amqp-support.md#device-client)

Связь D2C открыта, если вы отправляете телеметрию. 

Если соединение устройства мерцает, что означает, что устройство часто подключается и отключается, мы не будем отправлять каждое состояние соединения, но будем публиковать текущее состояние соединения, сделанное на периодическом снимке, пока не будет продолжаться мерцание. Получение одного и того же состояния соединения с различными числами последовательности или различных событий состояния соединения означает, что в состоянии соединения устройства произошло изменение.

## <a name="tips-for-consuming-events"></a>Советы по потреблению событий

Для работы с приложениями, обрабатывающими события Центра Интернета вещей, нужно следовать таким рекомендациям:

* Несколько подписок могут быть настроены для маршрутизации событий в один и тот же обработчик событий, поэтому не думайте, что события от конкретного источника. Всегда проверяйте тему сообщения, чтобы убедиться, что оно поступает из ожидаемого Центра Интернета вещей.

* Не следует предполагать, что все получаемые события имеют ожидаемые типы. Всегда проверяйте eventType перед обработкой сообщения.

* Сообщения могут прибывать не по порядку или с задержкой. Используйте поле etag, чтобы понять, актуальна ли информация об объектах для созданных устройств или удаленных событий.

## <a name="next-steps"></a>Следующие шаги

* [Send email notifications about Azure IoT Hub events using Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) (Отправка уведомлений электронной почты о событиях в Центре Интернета вещей Azure с помощью Logic Apps)

* См. дополнительные сведения об [упорядочении событий подключения и отключения устройств](iot-hub-how-to-order-connection-state-events.md).

* [Подробнее о сетке событий](../event-grid/overview.md)

* [Сравнение маршрутизации сообщений со службой "Сетка событий" и без нее для Центра Интернета вещей](iot-hub-event-grid-routing-comparison.md)

* [Узнайте, как использовать телеметрию IoT для реализации пространственной аналитики IoT с помощью Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
