---
title: Публикация, подписка на события в облаке - Azure Event Grid IoT Edge Документы Майкрософт
description: Публикация, подписка на события в облаке с помощью Webhook с Event Grid на IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844601"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Учебник: Публикуйте, подписывайтесь на события в облаке

В этой статье проходят все этапы, необходимые для публикации и подписки на события с помощью Event Grid on IoT Edge. В этом учебнике используется функция Azure в качестве обработчика событий. Для дополнительных типов [event handlers](event-handlers.md)назначения см.

Просмотрите [концепции Event Grid,](concepts.md) чтобы понять, что такое тема сетки событий и подписка, прежде чем продолжить работу.

## <a name="prerequisites"></a>Предварительные требования 
Для выполнения шагов, описанных в данном учебнике, потребуется следующее.

* **Подписка Azure** - Создайте [бесплатную учетную запись,](https://azure.microsoft.com/free) если у вас ее еще нет. 
* **Устройство Azure IoT Hub и IoT Edge** - Выполните последующие шаги в быстром запуске для устройств [Linux](../../iot-edge/quickstart-linux.md) или [Windows,](../../iot-edge/quickstart.md) если у вас его еще нет.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Создание функции Azure на портале Azure

Выполните действия, изложенные в [учебнике,](../../azure-functions/functions-create-first-azure-function.md) чтобы создать функцию Azure. 

Замените фрагмент кода следующим кодом:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

В новой функции выберите **URL-адрес функции** в правом верхнем виде, выберите ключ по умолчанию **(Функция),** а затем выберите **Copy.** Вы будете использовать значение URL функции позже в учебнике.

> [!NOTE]
> Обратитесь к документации [Azure Functions](../../azure-functions/functions-overview.md) для получения дополнительных примеров и учебников по реагированию на события, использующие триггеры событий EventGrid.

## <a name="create-a-topic"></a>Создание раздела

Как издатель события, необходимо создать тему сетки событий. Тема относится к конечной точке, где издатели могут отправлять события.

1. Создайте topic2.json со следующим содержанием. Ознакомиться с нашими [документами API](api.md) можно с информацией о полезной нагрузке.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Выполнить следующую команду, чтобы создать тему. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Выполнить следующую команду для проверки темы был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Образец вывода:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Создание подписки на событие

Подписчики могут зарегистрироваться на события, опубликованные на тему. Чтобы получить любое событие, подписчикам необходимо создать подписку на сетку событий по интересуемым.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Создание subscription2.json со следующим содержанием. Для получения подробной информации о полезной нагрузке обратитесь к нашей [документации API.](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > Конечная **точкаType** указывает, что абонент webhook.  Конечная **точкаUrl** определяет URL, на котором абонент слушает события. Этот URL-адрес соответствует образицы функции Azure, который вы установили ранее.
2. Выполнить следующую команду для создания подписки. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Выполнить следующую команду для проверки подписки был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Образец вывода:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Публикация события

1. Создайте event2.json со следующим содержанием. Для получения подробной информации о полезной нагрузке обратитесь к нашей [документации API.](api.md)

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. Выполнить следующую команду для публикации события

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Проверка доставки событий

Вы можете просмотреть событие, представленное на портале Azure, в соответствии с опцией **Monitor** вашей функции.

## <a name="cleanup-resources"></a>Очистка ресурсов

* Выполнить следующую команду, чтобы удалить тему и все ее подписки

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Удалите функцию Azure, созданную на портале Azure.

## <a name="next-steps"></a>Дальнейшие действия

В этом уроке вы создали тему сетки событий, подписку и опубликованные события. Теперь, когда вы знаете основные шаги, смотрите следующие статьи:

* Для устранения неполадок с использованием Azure Event Grid на IoT Edge [см.](troubleshoot.md)
* Создание/обновление подписки с [помощью фильтров.](advanced-filtering.md)
* Настройка сохранения модуля Event Grid на [linux](persist-state-linux.md) или [Windows](persist-state-windows.md)
* Следуйте [документации](configure-client-auth.md) для настройки проверки подлинности клиента
* Перенаправляйте события в Azure Event Grid в облаке, следуя этому [учебнику](forward-events-event-grid-cloud.md)
* [Мониторинг тем и подписки на краю](monitor-topics-subscriptions.md)
