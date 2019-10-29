---
title: Пересылка событий сетки событий в IoTHub — служба "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Пересылка событий сетки событий в IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 772fccc96f757c6165238e4041ce98c17c647880
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992305"
---
# <a name="tutorial-forward-events-to-iothub"></a>Учебник. Пересылка событий в IoTHub

В этой статье рассматриваются все шаги, необходимые для перенаправления событий сетки событий в другие IoT Edge модули, IoTHub с использованием маршрутов. Это может потребоваться по следующим причинам.

* Продолжайте использовать любые существующие инвестиции, уже имеющиеся на месте, с маршрутизацией edgeHub
* Предпочитать маршрутизацию всех событий с устройства только через центр Интернета вещей

Для работы с этим руководством необходимо понимать следующие понятия.

- [Основные понятия службы "Сетка событий Azure"](concepts.md)
- [Центр IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Технические условия 
Для работы с этим руководством вам потребуется:

* **Подписка Azure** . Создайте [бесплатную учетную запись](https://azure.microsoft.com/free) , если она еще не создана. 
* **Центр Интернета вещей Azure и устройство IOT Edge** . выполните действия, описанные в разделе Краткое руководство для устройств [Linux](../../iot-edge/quickstart-linux.md) или [Windows](../../iot-edge/quickstart.md) , если у вас его еще нет.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Создание раздела

Как издатель события, необходимо создать раздел «Сетка событий». Раздел относится к конечной точке, в которую издатели могут отправить события.

1. Создайте topic4. JSON со следующим содержимым. Дополнительные сведения о полезных данных см. в [документации по API](api.md) .

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Выполните следующую команду, чтобы создать раздел. Должен возвращаться код состояния HTTP, 200 ОК.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Выполните следующую команду, чтобы проверить, успешно ли создан раздел. Должен возвращаться код состояния HTTP, 200 ОК.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Пример выходных данных:

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

Подписчики могут регистрироваться для событий, опубликованных в разделе. Чтобы получить любое событие, им потребуется создать подписку на сетку событий в интересующей теме.

1. Создайте subscription4. JSON со следующим содержимым. Дополнительные сведения о полезных данных см. в [документации по API](api.md) .

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
   > `endpointType` указывает, что подписчик `edgeHub`. `outputName` указывает выходные данные, на которых модуль сетки событий будет маршрутизировать события, соответствующие этой подписке, в edgeHub. Например, события, соответствующие указанной выше подписке, будут записываться в `/messages/modules/eventgridmodule/outputs/sampleSub4`.
2. Выполните следующую команду, чтобы создать подписку. Должен возвращаться код состояния HTTP, 200 ОК.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Выполните следующую команду, чтобы проверить, успешно ли создана подписка. Должен возвращаться код состояния HTTP, 200 ОК.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Пример выходных данных:

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

## <a name="set-up-an-edge-hub-route"></a>Настройка маршрута концентратора ребра

Обновите маршрут концентратора ребра, чтобы переслать события подписки на события, перенаправляемые в IoTHub, следующим образом:

1. Войдите на [портал Azure](https://ms.portal.azure.com)
1. Перейдите в **центр Интернета вещей**.
1. Выберите **IOT Edge** в меню.
1. Выберите идентификатор целевого устройства в списке устройств.
1. Щелкните **Set Modules** (Настроить модули).
1. Нажмите кнопку **Далее** и в разделе маршруты.
1. В маршрутах добавьте новый маршрут.

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
   > Приведенный выше маршрут пересылает все события, соответствующие этой подписке, пересылаемым в центр Интернета вещей. Вы можете использовать функции [маршрутизации пограничных концентраторов](../../iot-edge/module-composition.md) для дальнейшей фильтрации и направить события службы "Сетка событий" в другие модули IOT Edge.

## <a name="setup-iot-hub-route"></a>Настройка маршрута центра Интернета вещей

Ознакомьтесь с руководством по [маршрутизации](../../iot-hub/tutorial-routing.md) центра Интернета вещей, чтобы настроить маршрут из центра Интернета вещей, чтобы можно было просматривать события, пересылаемые из модуля службы "Сетка событий".

## <a name="publish-an-event"></a>Публикация события

1. Создайте event4. JSON со следующим содержимым. Дополнительные сведения о полезных данных см. в [документации по API](api.md) .

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

1. Выполните следующую команду, чтобы опубликовать событие:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Проверка доставки событий

Действия по просмотру событий см. в [руководстве по маршрутизации](../../iot-hub/tutorial-routing.md) центра Интернета вещей.

## <a name="cleanup-resources"></a>Очистка ресурсов

* Выполните следующую команду, чтобы удалить раздел и все его подписки на границе:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Удалите все ресурсы, созданные при настройке маршрутизации IoTHub в облаке.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы создали раздел "Сетка событий", подписку на концентратор ребра и опубликованные события. Теперь, когда вы знакомы с основными шагами для пересылки в центр ребра, ознакомьтесь со следующими статьями:

* Использование фильтров маршрутов [концентратора ребра](../../iot-edge/module-composition.md) для событий секционирования
* Настройка сохраняемости модуля службы "Сетка событий" в [Linux](persist-state-linux.md) или [Windows](persist-state-windows.md)
* Следуйте инструкциям [по](configure-client-auth.md) настройке проверки подлинности клиента
* Пересылка событий в службу "Сетка событий Azure" в облаке, следуя этому [учебнику](forward-events-event-grid-cloud.md)
