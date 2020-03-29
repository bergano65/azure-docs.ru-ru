---
title: Переадресованные события Event Grid на IoTHub - Azure Event Grid IoT Edge Документы Майкрософт
description: Переадресованные события Event Grid на IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844706"
---
# <a name="tutorial-forward-events-to-iothub"></a>Учебник: Переадресованные события на IoTHub

В этой статье рассматриваются все шаги, необходимые для переадресовывании событий Event Grid на другие модули IoT Edge, IoTHub с использованием маршрутов. Вы можете сделать это по следующим причинам:

* Продолжайте использовать любые существующие инвестиции, уже существующие с помощью routing edgeHub
* Предпочитаюмаршрутть все события с устройства только через IoT Hub

Чтобы завершить этот учебник, вы должны понять следующие понятия:

- [Концепции сетки событий](concepts.md)
- [Концентратор IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Предварительные требования 
Для выполнения шагов, описанных в данном учебнике, потребуется следующее.

* **Подписка Azure** - Создайте [бесплатную учетную запись,](https://azure.microsoft.com/free) если у вас ее еще нет. 
* **Устройство Azure IoT Hub и IoT Edge** - Выполните последующие шаги в быстром запуске для устройств [Linux](../../iot-edge/quickstart-linux.md) или [Windows,](../../iot-edge/quickstart.md) если у вас его еще нет.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Создание раздела

Как издатель события, необходимо создать тему сетки событий. Тема относится к конечной точке, где издатели могут затем отправлять события.

1. Создайте topic4.json со следующим содержанием. Ознакомиться с нашими [документами API](api.md) можно с информацией о полезной нагрузке.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Выполнить следующую команду, чтобы создать тему. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Выполнить следующую команду для проверки темы был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Образец вывода:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Создание подписки на события

Подписчики могут зарегистрироваться на события, опубликованные на тему. Чтобы получить любое событие, им необходимо создать подписку на сетку событий по интересуемым теме.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Создайте subscription4.json с нижеприведенным контентом. Для получения подробной информации о полезной нагрузке обратитесь к нашей [документации API.](api.md)

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > Указывается, `endpointType` что абонент . `edgeHub` В `outputName` спецификации указан выход, на котором модуль Event Grid будет маршрутизировать события, которые соответствуют этой подписке edgeHub. Например, события, которые соответствуют вышеуказанной подписке, будут записаны на `/messages/modules/eventgridmodule/outputs/sampleSub4`.
2. Выполнить следующую команду для создания подписки. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Выполнить следующую команду для проверки подписки был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Образец вывода:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Настройка маршрута концентратора края

Обновление маршрута концентратора края для перенаправления событий подписки на IoTHub следующим образом:

1. Войти на [портал Azure](https://ms.portal.azure.com)
1. Перейдите к **концентратору IoT**.
1. Выберите **IoT Edge** из меню
1. Выберите идентификатор целевого устройства из списка устройств.
1. Выберите **набор модулей.**
1. Выберите **Далее** и раздел маршрутов.
1. В маршрутах добавьте новый маршрут

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Например,

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Вышеупомянутый маршрут будет направлять любые события, соответствующие этой подписке, которая будет направлена в концентратор IoT. Функции [маршрутизации концентратора Edge](../../iot-edge/module-composition.md) можно использовать для дальнейшего фильтрации и маршрутизации Event Grid на другие модули IoT Edge.

## <a name="setup-iot-hub-route"></a>Настройка маршрута IoT концентратора

Ознакомьтесь с учебником по [маршруту IoT Hub,](../../iot-hub/tutorial-routing.md) чтобы настроить маршрут из концентратора IoT, чтобы можно было просматривать события, переадресованные из модуля Event Grid. Используйте `true` для запроса, чтобы сохранить учебник простым.  



## <a name="publish-an-event"></a>Публикация события

1. Создайте event4.json со следующим содержанием. Ознакомиться с нашими [документами API](api.md) можно с информацией о полезной нагрузке.

    ```json
        [
          {
            "id": "eventId-iothub-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. Выполнить следующую команду для публикации события:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Проверка доставки событий

Ознакомьтесь с [руководством](../../iot-hub/tutorial-routing.md) по настройкам Концентратора IoT для просмотра событий.

## <a name="cleanup-resources"></a>Очистка ресурсов

* Выполнить следующую команду, чтобы удалить тему и все ее подписки на краю:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Удалите любые ресурсы, созданные при настройке и в облаке, и при настройке routing IoTHub.

## <a name="next-steps"></a>Дальнейшие действия

В этом уроке вы создали тему сетки событий, подписку концентратора края и опубликованные события. Теперь, когда вы знаете основные шаги, чтобы перейти к краю концентратор, см.

* Для устранения неполадок с использованием Azure Event Grid на IoT Edge [см.](troubleshoot.md)
* Используйте фильтры [концентратора края](../../iot-edge/module-composition.md) для событий раздела
* Настройка сохранения модуля Event Grid на [linux](persist-state-linux.md) или [Windows](persist-state-windows.md)
* Следуйте [документации](configure-client-auth.md) для настройки проверки подлинности клиента
* Перенаправляйте события в Azure Event Grid в облаке, следуя этому [учебнику](forward-events-event-grid-cloud.md)
* [Мониторинг тем и подписки на краю](monitor-topics-subscriptions.md)