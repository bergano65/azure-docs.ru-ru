---
title: Краткое руководство. Отправка и получение событий с помощью Node.js (Центры событий Azure)
description: Краткое руководство. В статье описано, как создать приложение Node.js, которое отправляет сообщения в Центры событий Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: ded2c83bc648e509c8cf00236cdf453b9c61af53
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720570"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Краткое руководство. Отправка и получение событий через Центры событий Azure с помощью Node.js

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом руководстве описано, как создавать приложения Node.js для отправки и получения событий через концентратор событий.

> [!NOTE]
> Вы можете скачать это краткое руководство в качестве примера с сайта [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), заменить строки `EventHubConnectionString` и `EventHubName` значениями для своего концентратора событий и выполнить этот пример. Или следуйте инструкциям из этого руководства, чтобы создать собственное решение.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

- Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- Node.js версии 8.x или более поздней. Скачайте последнюю версию LTS с сайта [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (рекомендуется) или любой другой интерфейс IDE.
- **Создайте пространство имен Центров событий и концентратор событий**. Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа Центров событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните процедуру, описанную в [этой статье](event-hubs-create.md), а затем перейдите к следующим шагам в этом руководстве. Получите строку подключения для пространства имен концентратора событий, следуя инструкциям из статьи [Получение строки подключения на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Строка подключения понадобится нам позже.


### <a name="install-npm-package"></a>Установка пакета npm
Чтобы установить [пакет npm для Центров событий](https://www.npmjs.com/package/@azure/event-hubs), откройте командную строку, где `npm` сохранен в переменной окружения path, перейдите в папку, в которой вы хотите сохранить примеры, и выполните эту команду.

```shell
npm install @azure/event-hubs
```

Чтобы установить [пакет npm для узла обработчика событий](https://www.npmjs.com/package/@azure/event-processor-host), выполните приведенную ниже команду.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Отправка событий

Из этого раздела вы узнаете, как создать приложение Node.js, которое отправляет события в концентратор событий. 

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com). 
2. Создайте файл с именем `send.js` и вставьте в него приведенный ниже код. Получите строку подключения для пространства имен концентратора событий, следуя [Получение строки подключения на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

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
3. Введите строку подключения и имя Центра событий в приведенный выше код.
4. Затем в окне командной строки выполните команду `node send.js`, которая запускает этот файл. Она отправит 100 событий в ваш Центр событий.

Поздравляем! Вы отправили события в концентратор событий.


## <a name="receive-events"></a>Получение событий

Из этого раздела вы узнаете, как создать приложение Node.js, которое получает события из одного раздела группы потребителей по умолчанию в концентраторе событий. 

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com). 
2. Создайте файл с именем `receive.js` и вставьте в него приведенный ниже код.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

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
3. Введите строку подключения и имя Центра событий в приведенный выше код.
4. Затем выполните в окне командной строки команду `node receive.js`, которая запускает этот файл. Она получит события из одного раздела группы потребителей по умолчанию в указанном Центре событий.

Поздравляем! Теперь вы получили события из концентратора событий.

## <a name="receive-events-using-event-processor-host"></a>Получение событий с помощью узла обработчика событий

В этом разделе показано, как получать события от концентратора событий, используя [EventProcessorHost](event-hubs-event-processor-host.md) Azure в приложении Node.js. Класс EventProcessorHost (EPH) помогает эффективно получать события от концентратора событий, создавая приемники для всех секций в группе потребителей концентратора событий. Он регулярно фиксирует метаданные для полученных сообщений в Azure Storage Blob. Благодаря такому подходу позже можно легко продолжить получать сообщения с того места, где вы остановились.

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com). 
2. Создайте файл с именем `receiveAll.js` и вставьте в него приведенный ниже код.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

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
3. Введите в приведенный выше код строку подключения и имя Центра событий, в также строку подключения к хранилищу BLOB-объектов Azure.
4. Затем выполните в окне командной строки команду `node receiveAll.js`, которая запускает этот файл.

Поздравляем! Теперь вы получили сообщения из концентратора событий с помощью узла обработчика событий. Так вы получите события из всех разделов группы потребителей по умолчанию в указанном Центре событий.

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими статьями:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Функции и терминология в Центрах событий Azure](event-hubs-features.md)
- [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)
- Просмотрите другие примеры Node.js для [Центров событий](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) и [узла обработчика событий](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) на сайте GitHub.
