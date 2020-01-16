---
title: Отправка и получение событий с помощью Node.js (Центры событий Azure)
description: В статье описано, как создать приложение Node.js, которое отправляет сообщения в Центры событий Azure.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981615"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Отправка и получение событий через Центры событий Azure с помощью Node.js

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Служба "Центры событий" может обрабатывать и сохранять события и данные, в том числе телеметрические, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в режиме реального времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом руководстве описано, как создавать приложения Node.js для отправки и получения событий через концентратор событий.

> [!IMPORTANT]
> В этом кратком руководстве используется версия 5 пакета SDK скриптов Java для концентраторов событий Azure. Краткое руководство, в котором используется старая версия пакета SDK для сценариев Java, см. в [этой статье](event-hubs-node-get-started-send.md). Если вы используете версию 2 пакета SDK, рекомендуется перенести код в последнюю версию. Дополнительные сведения см. в разделе [руководств по миграции](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md).

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

- Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- Node.js версии 8.x или более поздней. Скачайте последнюю версию LTS с сайта [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (рекомендуется) или любой другой интерфейс IDE.
- **Создайте пространство имен Центров событий и концентратор событий**. Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа Центров событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните процедуру, описанную в [этой статье](event-hubs-create.md), а затем перейдите к следующим шагам в этом руководстве. Затем получите строку подключения для пространства имен концентратора событий, следуя инструкциям из статьи: [Получение строки подключения](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Строка подключения понадобится нам позже.


### <a name="install-npm-packages"></a>Установка пакетов npm
Чтобы установить [пакет npm для Центров событий](https://www.npmjs.com/package/@azure/event-hubs), откройте командную строку, где `npm` сохранен в переменной окружения path, перейдите в папку, в которой вы хотите сохранить примеры, и выполните эту команду.

```shell
npm install @azure/event-hubs
```

Для принимающей стороны необходимо установить два дополнительных пакета. В этом кратком руководстве вы будете использовать хранилище BLOB-объектов Azure для сохранения контрольных точек, чтобы программа не читала уже прочитанные события. Он создает контрольные точки для полученных сообщений с регулярными интервалами в большом двоичном объекте. Благодаря такому подходу позже можно легко продолжить получать сообщения с того места, где вы остановились.

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>Отправка событий

Из этого раздела вы узнаете, как создать приложение Node.js, которое отправляет события в концентратор событий.

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com).
2. Создайте файл с именем `send.js` и вставьте в него следующий код.

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Не забудьте заменить **строку подключения** и значения **имени концентратора событий** в коде.
5. Выполните команду `node send.js`, чтобы выполнить этот файл. В концентратор событий будет отправлен пакет из трех событий.
6. В портал Azure можно проверить, что концентратор событий получил сообщения. Переключитесь в представление **сообщений** в разделе **метрики** . Обновите страницу, чтобы обновить диаграмму. На отображение полученных сообщений может уйти несколько секунд.

    [![убедиться, что концентратор событий получил сообщения](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Полный исходный код с дополнительными информационными комментариями см. в [этом файле на сайте GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js) .

Поздравляем! Вы отправили события в концентратор событий.


## <a name="receive-events"></a>Получение событий
В этом разделе показано, как получить события из концентратора событий с помощью хранилища контрольных точек BLOB-объектов Azure в приложении Node. js. Он регулярно фиксирует метаданные для полученных сообщений в Azure Storage Blob. Благодаря такому подходу позже можно легко продолжить получать сообщения с того места, где вы остановились.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Создание хранилища Azure и контейнера больших двоичных объектов
Выполните следующие действия, чтобы создать учетную запись хранения Azure в контейнере BLOB-объектов.

1. [Создайте учетную запись хранения Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Создание контейнера больших двоичных объектов](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Получение строки подключения к учетной записи хранения](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Запишите строку подключения и имя контейнера. Они будут использоваться в коде получения.

### <a name="write-code-to-receive-events"></a>Написание кода для получения событий

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com).
2. Создайте файл с именем `receive.js` и вставьте в него следующий код. Дополнительные сведения см. в комментариях к коду.
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. Не забудьте указать в коде **следующие значения** :
    - Строка подключения к пространству имен концентраторов событий
    - Имя концентратора событий
    - Строка подключения к учетной записи хранения Azure
    - Имя контейнера больших двоичных объектов
5. Затем в окне командной строки выполните команду `node receive.js`, которая запускает этот файл. В окне должны отобразиться сообщения о полученных событиях.

    > [!NOTE]
    > Полный исходный код с дополнительными информационными комментариями см. в [этом файле на сайте GitHub](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js).

Поздравляем! Теперь вы получили события из концентратора событий. Программа-получатель получит события из всех разделов группы потребителей по умолчанию в концентраторе событий.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с этими примерами на сайте GitHub:

- [Примеры JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Примеры TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
