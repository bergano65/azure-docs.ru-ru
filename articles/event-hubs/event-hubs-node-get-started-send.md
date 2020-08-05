---
title: Отправка и получение событий через Центры событий Azure с помощью JavaScript (устаревший)
description: В статье описано, как создать приложение JavaScript, которое отправляет события или получает их из службы "Центры событий Azure" с помощью старого пакета azure/event-hubs версии 2.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: ffa6b821d617731bd6ee5cbb9bec8bd6d856a7a6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414128"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-javascript-azureevent-hubs-version-2"></a>Краткое руководство. Отправка и получение событий через Центры событий Azure с помощью JavaScript (@azure/event-hubs версия 2)
В этом кратком руководстве показано, как создать приложения JavaScript для отправки и получения событий через концентратор событий с помощью пакета JavaScript azure/event-hubs версии 2. 

> [!WARNING]
> В этом кратком руководстве используется старый пакет azure/event-hubs версии 2. Краткое руководство по использованию пакета **последней версии 5** см. [Отправка событий или получение событий из концентраторов событий с помощью Node.js (azure/event-hubs версии 5)](get-started-node-send-v2.md) Чтобы начать использовать в приложении новый пакет вместо старого, ознакомьтесь с руководством по [переходу с azure/eventhubs версии 1 на версию 5](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md). 


## <a name="prerequisites"></a>Предварительные требования

Если вы впервые используете Центры событий Azure, ознакомьтесь с общими сведениями в [этой статье](event-hubs-about.md), прежде чем приступить к работе с этим руководством. 

Для работы с данным руководством необходимо следующее:

- **Подписка Microsoft Azure.** Чтобы использовать службы Azure, в том числе Центры событий Azure, потребуется действующая подписка.  Если у вас еще нет учетной записи Azure, [зарегистрируйтесь для работы с бесплатной пробной версией](https://azure.microsoft.com/free/) или [активируйте преимущества для подписчиков MSDN при создании учетной записи](https://azure.microsoft.com).
- Node.js версии 8.x или более поздней. Скачайте последнюю версию LTS с сайта [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (рекомендуется) или любой другой интерфейс IDE.
- **Создайте пространство имен Центров событий и концентратор событий**. Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа Центров событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните процедуру, описанную в [этой статье](event-hubs-create.md), а затем перейдите к следующим шагам в этом руководстве. Получите строку подключения для пространства имен концентратора событий, следуя инструкциям из статьи [Получение строки подключения на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Строка подключения понадобится нам позже.


### <a name="install-npm-package"></a>Установка пакета npm
Чтобы установить [пакет npm для Центров событий](https://www.npmjs.com/package/@azure/event-hubs/v/2.1.0), откройте командную строку, где `npm` сохранен в переменной окружения path, перейдите в папку, в которой вы хотите сохранить примеры, и выполните эту команду.

```shell
npm install @azure/event-hubs@2
```

Чтобы установить [пакет npm для узла обработчика событий](https://www.npmjs.com/package/@azure/event-processor-host), выполните приведенную ниже команду.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Отправка событий

Из этого раздела вы узнаете, как создать приложение JavaScript, которое отправляет события в концентратор событий. 

> [!NOTE]
> Вы можете скачать это краткое руководство в качестве примера с сайта [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client), заменить строки `EventHubConnectionString` и `EventHubName` значениями для своего концентратора событий и выполнить этот пример. Или следуйте инструкциям из этого руководства, чтобы создать собственное решение.

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com). 
2. Создайте файл с именем `send.js` и вставьте в него приведенный ниже код. Получите строку подключения для пространства имен концентратора событий, следуя [Получение строки подключения на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs@2");

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

Из этого раздела вы узнаете, как создать приложение JavaScript, которое получает события из одного раздела группы потребителей по умолчанию в концентраторе событий. 

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com). 
2. Создайте файл с именем `receive.js` и вставьте в него приведенный ниже код.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs@2");

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
4. Затем в окне командной строки выполните команду `node receive.js`, которая запускает этот файл. Она получит события из одного раздела группы потребителей по умолчанию в указанном Центре событий.

Поздравляем! Теперь вы получили события из концентратора событий.

## <a name="receive-events-using-event-processor-host"></a>Получение событий с помощью узла обработчика событий

В этом разделе показано, как получать события от концентратора событий, используя [EventProcessorHost](event-hubs-event-processor-host.md) Azure в приложении JavaScript. Класс EventProcessorHost (EPH) помогает эффективно получать события от концентратора событий, создавая приемники для всех секций в группе потребителей концентратора событий. Он регулярно фиксирует метаданные для полученных сообщений в Azure Storage Blob. Благодаря такому подходу позже можно легко продолжить получать сообщения с того места, где вы остановились.

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
4. Затем в окне командной строки выполните команду `node receiveAll.js`, которая запускает этот файл.

Поздравляем! Теперь вы получили сообщения из концентратора событий с помощью узла обработчика событий. Так вы получите события из всех разделов группы потребителей по умолчанию в указанном Центре событий.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Функции и терминология в Центрах событий Azure](event-hubs-features.md)
- [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)
- Просмотрите другие примеры JavaScript для [Центров событий](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) и [узла обработчика событий](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) на сайте GitHub.
