---
title: Обработчики событий и направления - Azure Event Grid IoT Edge Документы Майкрософт
description: Обработчики событий и направления в Event Grid на краю
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849751"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Обработчики событий и направления в Event Grid на краю

Обработчик события — это место, где событие предназначено для дальнейших действий или для обработки события. С модулем Event Grid on Edge обработчик событий может находиться на том же устройстве края, другом устройстве или в облаке. Вы можете использовать любой WebHook для обработки событий или отправлять события одному из наиболее обработчиков, таким как Azure Event Grid.

В этой статье содержится информация о том, как настроить каждую из них.

## <a name="webhook"></a>Вебхук

Чтобы опубликовать конечную точку WebHook, установите: `endpointType` `WebHook`

* конечный пункт: URL-адрес конечной точки WebHook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Сетка событий Azure

Чтобы опубликовать в точке облака облака Azure `eventGrid` Event Grid, установите: `endpointType`

* endpointUrl: Url-адрес сетки событий в облаке
* sasKey: Событие Сетка Тема SAS ключ
* topicName: Имя для штампа всех исходящих событий в Event Grid. Имя темы полезно при размещении на тему Event Grid Domain.

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

## <a name="iot-edge-hub"></a>Концентратор IoT Edge

Чтобы опубликовать модуль Edge Hub, `endpointType` `edgeHub` установите:

* выход: выход, на котором модуль Event Grid будет маршрутизировать события, которые соответствуют этой подписке edgeHub. Например, события, которые соответствуют нижеподписной подписке, будут записаться на /сообщения/модули/eventgridmodule/выходы/образецSub4.

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

## <a name="event-hubs"></a>Центры событий

Чтобы опубликовать в концентратор `eventHub` событий, установите: `endpointType`

* строка подключения String: Строка подключения для конкретного концентратора событий, на который вы ориентируетесь, генерируемая с помощью политики общего доступа.

    >[!NOTE]
    > Строка соединения должна быть специфична сущностью. Использование строки подключения пространства имен не работает. Можно создать строку соединения, связанную с сущностью, перенаправившись в определенный концентратор событий, который вы хотели бы опубликовать в портале Azure, и нажав **на политики общего доступа** для создания новой строки connecection.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Очереди служебной шины

Чтобы опубликовать в очередь автобуса `endpointType` службы, установите: `serviceBusQueue`

* строка подключения String: Строка подключения для конкретной очереди шин обслуживания, на которой вы ориентируетесь, генерируемая с помощью политики общего доступа.

    >[!NOTE]
    > Строка соединения должна быть специфична сущностью. Использование строки подключения пространства имен не работает. Создайте строку соединения, связанную с сущностью, перенаправившись в конкретную очередь в автобусслужбы, которую вы хотели бы опубликовать в портале Azure, и нажав **на политики общего доступа** для создания новой строки connecection.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Разделы шины обслуживания

Чтобы опубликовать тему автобуса службы, установите `endpointType` и `serviceBusTopic` предоставьте:

* строка подключения String: Строка подключения для конкретной темы автобуса службы, на которую вы ориентируетесь, генерируемой с помощью политики общего доступа.

    >[!NOTE]
    > Строка соединения должна быть специфична сущностью. Использование строки подключения пространства имен не работает. Создайте строку соединения, связанную с сущностью, перенаправившись по конкретной теме шины службы, которую вы хотели бы опубликовать в портале Azure, и нажав **на политики общего доступа** для создания новой строки connecection.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Очереди службы хранилища

Чтобы опубликовать очередь хранения, `endpointType` установите: `storageQueue`

* название очереди: Имя очереди хранения, в которое вы публикуете.
* connectionString: Строка подключения для учетной записи хранилища, в ней находится очередь хранения.

    >[!NOTE]
    > Unline Концентраторы событий, Очереди автобусов обслуживания, и Темы шины обслуживания, строка соединения, используемая для очередей хранения, не является специфическим объектом. Вместо этого он должен, но строка подключения для учетной записи хранения.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```