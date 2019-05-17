---
title: Отправки и получения событий с помощью Node.js — концентраторы событий Azure | Документация Майкрософт
description: В статье описано, как создать приложение Node.js, которое отправляет сообщения в Центры событий Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539346"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Отправлять события или получения событий из концентраторов событий Azure с помощью Node.js

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

Это руководство содержит инструкции для создания приложений Node.js для отправки событий или получать события из концентратора событий.

> [!NOTE]
> Вы можете скачать это краткое руководство в качестве примера с сайта [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), заменить строки `EventHubConnectionString` и `EventHubName` значениями для своего концентратора событий и выполнить этот пример. Или следуйте инструкциям из этого руководства, чтобы создать собственное решение.

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

- Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- Node.js версии 8.x или более поздней. Скачайте последнюю версию LTS с сайта [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (рекомендуется) или любой другой интерфейс IDE.
- **Создать пространство имен концентраторов событий и концентратора событий**. Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа Центров событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните процедуру, описанную в [этой статье](event-hubs-create.md), а затем перейдите к следующим шагам в этом руководстве. Затем получите строку подключения для пространства имен концентратора событий, следуя указаниям из статьи: [Получение строки подключения на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Строка подключения понадобится нам позже.


### <a name="install-npm-package"></a>Установите пакет npm
Чтобы установить [пакета npm для концентраторов событий](https://www.npmjs.com/package/@azure/event-hubs), откройте командную строку с `npm` в пути, перейдите в каталог на папку, где вы хотите иметь ваших примеров и затем выполните следующую команду

```shell
npm install @azure/event-hubs
```

Чтобы установить [пакета npm для узла обработчика событий](https://www.npmjs.com/package/@azure/event-processor-host), выполните указанную ниже команду, вместо этого

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Отправка событий

В этом разделе показано, как создать приложение Node.js, которое отправляет события в концентратор событий. 

1. Откройте предпочитаемый редактор, такой как [Visual Studio Code](https://code.visualstudio.com). 
2. Создайте файл с именем `send.js` и вставьте ниже кода в него.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Введите строку подключения и имя концентратора событий в приведенном выше коде
4. Затем выполните команду `node send.js` в командной строке, чтобы выполнить этот файл. Этот параметр отправит 100 событий в концентратор событий

Поздравляем! Теперь вы можете отправлять события в концентратор событий.


## <a name="receive-events"></a>Получение событий

В этом разделе показано, как создать приложение Node.js, которое получает события из одной секции группы потребителей по умолчанию в концентраторе событий. 

1. Откройте предпочитаемый редактор, такой как [Visual Studio Code](https://code.visualstudio.com). 
2. Создайте файл с именем `receive.js` и вставьте ниже кода в него.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Введите строку подключения и имя концентратора событий в приведенном выше коде.
4. Затем выполните команду `node receive.js` в командной строке, чтобы выполнить этот файл. Это будет получать события из одного из разделов по умолчанию группы потребителей в концентраторе событий

Поздравляем! Теперь вы можете получать события из концентратора событий.

## <a name="receive-events-using-event-processor-host"></a>Получение событий с помощью узла обработчика событий

В этом разделе показано, как получать события из концентратора событий с помощью Azure [EventProcessorHost](event-hubs-event-processor-host.md) в приложении Node.js. Класс EventProcessorHost (EPH) помогает эффективно получать события от концентратора событий, создавая приемники для всех секций в группе потребителей концентратора событий. Он регулярно фиксирует метаданные для полученных сообщений в Azure Storage Blob. Благодаря такому подходу позже можно легко продолжить получать сообщения с того места, где вы остановились.

1. Откройте предпочитаемый редактор, такой как [Visual Studio Code](https://code.visualstudio.com). 
2. Создайте файл с именем `receiveAll.js` и вставьте ниже кода в него.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Введите строку подключения и имя концентратора событий в приведенном выше коде, а также строку подключения для службы хранилища BLOB-объектов Azure
4. Затем выполните команду `node receiveAll.js` в командной строке, чтобы выполнить этот файл.

Поздравляем! Теперь вы можете получать события из концентратора событий с помощью узла обработчика событий. Это будет получать события из всех разделов по умолчанию группы потребителей в концентраторе событий

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Возможностями и терминологией в концентраторах событий Azure](event-hubs-features.md)
- [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)
- См. другие примеры Node.js для [концентраторов событий](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) и [Event Processor Host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) на GitHub
