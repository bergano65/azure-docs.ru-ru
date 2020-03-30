---
title: Публикация, подписка на события локально - Azure Event Grid IoT Edge Документы Майкрософт
description: Публикация, подписка на события локально с помощью Webhook с Event Grid на IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281006"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Учебник: Публиковать, подписаться на события локально

В этой статье вы можете просмотреть все шаги, необходимые для публикации и подписки на события с помощью Event Grid на IoT Edge.

> [!NOTE]
> Чтобы узнать о темах и подписках [Event Grid Concepts](concepts.md)Azure Event Grid, см.

## <a name="prerequisites"></a>Предварительные требования 
Для выполнения шагов, описанных в данном учебнике, потребуется следующее.

* **Подписка Azure** - Создайте [бесплатную учетную запись,](https://azure.microsoft.com/free) если у вас ее еще нет. 
* **Устройство Azure IoT Hub и IoT Edge** - Выполните шаги в быстром запуске для устройств [Linux](../../iot-edge/quickstart-linux.md) или [Windows,](../../iot-edge/quickstart.md) если у вас его еще нет.

## <a name="deploy-event-grid-iot-edge-module"></a>Развертывание модуля Event Grid IoT Edge

Существует несколько способов развертывания модулей на устройстве IoT Edge, и все они работают для Azure Event Grid на IoT Edge. В этой статье описаны шаги по развертыванию Event Grid на IoT Edge с портала Azure.

>[!NOTE]
> В этом уроке вы развернете модуль Event Grid без настойчивости. Это означает, что любые темы и подписки, которые вы создаете в этом учебнике, будут удалены при передислокации модуля. Для получения дополнительной информации о том, как [Persist state in Linux](persist-state-linux.md) настроить настойчивость, [Persist state in Windows](persist-state-windows.md)см. Для производственных нагрузок мы рекомендуем установить модуль Event Grid с сохранением.


### <a name="select-your-iot-edge-device"></a>Выберите устройство IoT Edge

1. Войти на [портал Azure](https://portal.azure.com)
1. Перейдите в Центр Интернета вещей.
1. Выберите **IoT Edge** из меню в разделе **Автоматическое управление устройствами.** 
1. Нажмите на идентификатор целевого устройства из списка устройств
1. Выберите **набор модулей.** Держите страницу открытой. Вы продолжите шаги в следующем разделе.

### <a name="configure-a-deployment-manifest"></a>Настройка манифеста развертывания

Манифест развертывания — это документ JSON, в котором определены развертываемые модули, способ передачи данных между этими модулями и требуемые свойства для двойников модулей. Портал Azure имеет мастер, который будет просматривать вас, создавая манифест развертывания, вместо того, чтобы создавать документ JSON вручную.  В этом мастере есть три шага: **Добавление модулей**, **Указание маршрутов** и **Просмотр развертываний**.

### <a name="add-modules"></a>Добавление модулей

1. В разделе **Модули развертывания** выберите **Добавить**
1. Из типов модулей в списке выпадающих выбросов выберите **модуль IoT Edge**
1. Предоставьте имя, изображение, контейнер создать варианты контейнера:

   * **Имя**: eventgridmodule
   * **Изображение URI**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Параметры создания контейнера.**

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Нажмите кнопку **Сохранить**.
 1. Перейти к следующему разделу, чтобы добавить модуль подписчика Azure Event Grid, прежде чем развертывать их вместе.

    >[!IMPORTANT]
    > В этом уроке вы развернете модуль Event Grid с отключением клиентской аутентификации. Для производственных нагрузок мы рекомендуем включить аутентификацию клиента. Для получения дополнительной информации о том, как настроить модуль Event Grid безопасно, смотрите [безопасности и аутентификации](security-authentication.md).
    > 
    > Если вы используете Azure VM в качестве устройства края, добавьте правило входящего порта, чтобы разрешить входящий трафик в порту 4438. Для получения инструкций по добавлению правила, смотрите [Как открыть порты для VM](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Развертывание Модуля IoT Edge абонента событий

В этом разделе показано, как развернуть другой модуль IoT, который будет выступать в качестве обработчика событий, к которому могут быть доставлены события.

### <a name="add-modules"></a>Добавление модулей

1. В разделе **Модули развертывания** выберите **Добавить** еще раз. 
1. Из типов модулей в списке выпадающих выбросов выберите **модуль IoT Edge**
1. Предоставьте имя, изображение и контейнер, создайте параметры контейнера:

   * **Имя**: абонент
   * **Изображение URI**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Параметры создания контейнеров:** Нет
1. Нажмите кнопку **Сохранить**.
1. Нажмите **далее,** чтобы продолжить раздел маршрутов

 ### <a name="setup-routes"></a>Маршруты настройки

Сохранить маршруты по умолчанию и выбрать **Далее,** чтобы продолжить раздел обзора

### <a name="submit-the-deployment-request"></a>Отправить запрос на развертывание

1. В разделе обзора отображается манифест развертывания JSON, созданный на основе выделения в предыдущем разделе. Подтвердите, что вы видите оба модуля: **eventgridmodule** и **абонента,** перечисленных в JSON. 
1. Просмотрите сведения о развертывании, а затем выберите **Отправить**. После отправки развертывания вы возвращаетесь на страницу **устройства.**
1. В **разделе Модули,** убедитесь, что оба **Eventgrid** и **абонента** модули перечислены. Кроме того, убедитесь, что **указанные в развертывании** и **сообщенные** столбцы устройств настроены на **Yes.**

    Запуск модуля на устройстве может занять несколько секунд. Затем информация о нем передается в Центр Интернета вещей. Обновите страницу, чтобы увидеть обновленное состояние.

## <a name="create-a-topic"></a>Создание раздела

Как издатель события, необходимо создать тему сетки событий. В Azure Event Grid тема относится к точке конечных точек, по которой издатели могут отправлять события.

1. Создайте topic.json со следующим содержанием. Для получения подробной информации о полезной нагрузке смотрите нашу [документацию API](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Выполнить следующую команду для создания темы сетки событий. Подтвердите, что код `200 OK`состояния HTTP .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Выполнить следующую команду для проверки темы был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Образец вывода:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Создание подписки на событие

Подписчики могут зарегистрироваться на события, опубликованные на тему. Чтобы получить любое событие, необходимо создать подписку Event Grid на интересуемую тему.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Создайте subscription.json со следующим содержанием. Для получения подробной информации о полезной нагрузке ознакомьтесь с нашей [документацией API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > Свойство **endpointType** указывает, что абонентом является **Webhook.**  Конечная **точкаUrl** определяет URL, на котором абонент слушает события. Этот URL-адрес соответствует образцу абонента Azure, который вы развернули ранее.
2. Выполнить следующую команду, чтобы создать подписку на тему. Подтвердите, что код `200 OK`состояния HTTP .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Выполнить следующую команду для проверки подписки был создан успешно. КОД статуса HTTP 200 OK должен быть возвращен.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Образец вывода:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Публикация события

1. Создайте event.json со следующим содержанием. Для получения подробной информации о полезной нагрузке смотрите нашу [документацию API](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Выполнить следующую команду для публикации события.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Проверка доставки событий

1. SSH или RDP в ваш IoT Edge VM.
1. Проверьте журналы абонентов:

    В Windows запустите следующую команду:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   В Linux выполните следующую команду:

    ```sh
    sudo docker logs subscriber
    ```

    Образец вывода:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Очистка ресурсов

* Выполнить следующую команду, чтобы удалить тему и все ее подписки.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Удалите модуль абонента с устройства IoT Edge.


## <a name="next-steps"></a>Дальнейшие действия
В этом уроке вы создали тему сетки событий, подписку и опубликованные события. Теперь, когда вы знаете основные шаги, смотрите следующие статьи: 

- Для устранения неполадок с использованием Azure Event Grid на IoT Edge [см.](troubleshoot.md)
- Создание/обновление подписки с [помощью фильтров.](advanced-filtering.md)
- Включить сохранение модуля Event Grid на [Linux](persist-state-linux.md) или [Windows](persist-state-windows.md)
- Следуйте [документации](configure-client-auth.md) для настройки проверки подлинности клиента
- Перекажив события в Azure Functions в облаке, следуя этому [учебнику](pub-sub-events-webhook-cloud.md)
- [Реагировать на события хранения Blob на IoT Edge](react-blob-storage-events-locally.md)
- [Мониторинг тем и подписки на краю](monitor-topics-subscriptions.md)

