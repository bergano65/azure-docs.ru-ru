---
title: Передние события края в облако Event Grid - Azure Event Grid IoT Edge Документы Майкрософт
description: Передние события края в облако Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844723"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Учебник: Переадресованные события в облако Event Grid

В этой статье просматривались все шаги, необходимые для переноса событий edge в сетку событий в облаке Azure. Вы можете сделать это по следующим причинам:

* Реагируйте на события края в облаке.
* Передние события в Сетку событий в облаке и используйте концентраторы Azure Event или Azure Storage для буферизации событий перед их обработкой в облаке.

 Чтобы завершить этот учебник, вам нужно иметь представление о концепциях Event Grid на [краю](concepts.md) и [Azure](../concepts.md). Для дополнительных типов [event handlers](event-handlers.md)назначения см. 

## <a name="prerequisites"></a>Предварительные требования 
Для выполнения шагов, описанных в данном учебнике, потребуется следующее.

* **Подписка Azure** - Создайте [бесплатную учетную запись,](https://azure.microsoft.com/free) если у вас ее еще нет. 
* **Устройство Azure IoT Hub и IoT Edge** - Выполните последующие шаги в быстром запуске для устройств [Linux](../../iot-edge/quickstart-linux.md) или [Windows,](../../iot-edge/quickstart.md) если у вас его еще нет.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Создание темы сетки событий и подписки в облаке

Создайте тему сетки событий и подписку в облаке, следуя [этому учебнику.](../custom-event-quickstart-portal.md) `topicURL`Запишите, `sasKey`и `topicName` недавно созданная тема, которую вы будете использовать позже в учебнике.

Например, если вы создали `testegcloudtopic` тему, названную в Западной ЧАСТИ США, значения будут выглядеть примерно так:

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **ТемаИмя**:`testegcloudtopic`
* **SasKey**: Доступно под **AccessKey** вашей темы. Используйте **ключ1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Создание темы сетки событий на краю

1. Создавайте topic3.json со следующим содержанием. Ознакомиться с нашими [документами API](api.md) можно с информацией о полезной нагрузке.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Выполнить следующую команду, чтобы создать тему. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Выполнить следующую команду для проверки темы был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Образец вывода:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Создание подписки Event Grid на краю

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Создайте subscription3.json со следующим содержанием. Ознакомиться с нашими [документами API](api.md) можно с информацией о полезной нагрузке.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > В **endpointUrl** указывается, что URL-адрес темы Event Grid в облаке. **SasKey** относится к ключевой теме облака Event Grid. Значение в **topicName** будет использоваться для штампа всех исходящих событий в Event Grid. Это может быть полезно при размещении на тему домена Event Grid. Для получения дополнительной информации о [Event domains](../event-domains.md) теме домена Event Grid см.

    Например,
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Выполнить следующую команду для создания подписки. КОД статуса HTTP 200 OK должен быть возвращен.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Выполнить следующую команду для проверки подписки был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Образец вывода:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Опубликовать событие на краю

1. Создайте event3.json со следующим содержанием. Ознакомиться с [документацией API](api.md) можно с информацией о полезной нагрузке.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Выполните следующую команду:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Проверить событие края в облаке

Для получения [информации](../custom-event-quickstart-portal.md)о просмотре событий, поставляемых по теме облака, см.

## <a name="cleanup-resources"></a>Очистка ресурсов

* Выполнить следующую команду, чтобы удалить тему и все ее подписки

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Удалите тему и подписки, созданные в облаке (Azure Event Grid), а также.

## <a name="next-steps"></a>Дальнейшие действия

В этом уроке вы опубликовали событие на краю и перенаправили в Event Grid в облаке Azure. Теперь, когда вы знаете основные шаги, чтобы перейти к Event Grid в облаке:

* Для устранения неполадок с использованием Azure Event Grid на IoT Edge [см.](troubleshoot.md)
* Передайте события На IoTHub, следуя этому [учебнику](forward-events-iothub.md)
* Передвигайте события в Webhook в облаке, следуя этому [учебнику](pub-sub-events-webhook-cloud.md)
* [Мониторинг тем и подписки на краю](monitor-topics-subscriptions.md)
