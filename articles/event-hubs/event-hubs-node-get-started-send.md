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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203233"
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
- Клон [репозитория GitHub пример](https://github.com/Azure/azure-event-hubs-node) на вашем компьютере. 


## <a name="send-events"></a>Отправка событий
В этом разделе показано, как создать приложение Node.js, которое отправляет события в концентратор событий. 

### <a name="install-nodejs-package"></a>Установка пакета Node.js
Установите пакет Node.js для Центров событий Azure на компьютере. 

```shell
npm install @azure/event-hubs
```

Если вы еще не клонировали репозиторий Git, как упоминалось в предварительных требованиях, загрузите [пример](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) из GitHub. 

Клонированный пакет SDK содержит несколько примеров, которые показывают, как отправлять события в концентратор событий с помощью Node.js. В этом кратком руководстве используется пример **simpleSender.js**. Чтобы увидеть получение событий, откройте другой терминал и получите события с помощью [примера получения](event-hubs-node-get-started-receive.md).

1. Откройте проект в Visual Studio Code. 
2. Создайте файл с именем **.env** в папке **client**. Скопируйте и вставьте в него пример переменных среды из **sample.env** в корневой папке.
3. Настройте строку подключения, имя концентратора событий и конечную точку хранилища. Инструкции по получению строки подключения для концентратора событий см. в разделе [Создание пространства имен в Центрах событий](event-hubs-create.md#create-an-event-hubs-namespace).
4. В интерфейсе командной строки Azure перейдите по пути к папке **client**. Установите пакеты узла и создайте проект, выполнив следующие команды:

    ```shell
    npm i
    npm run build
    ```
5. Запустите отправку событий, выполнив следующую команду: 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>Просмотр примера кода 
Просмотрите пример кода в файле simpleSender.js, чтобы отправить события в концентратор событий.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Прежде чем выполнять этот скрипт, не забудьте задать переменные среды. Вы можете настроить их в командной строке, как показано в следующем примере, или использовать [пакет dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>Получение событий
В этом руководстве показано, как получать события от концентратора событий, используя [EventProcessorHost](event-hubs-event-processor-host.md) Azure в приложении Node.js. Класс EventProcessorHost (EPH) помогает эффективно получать события от концентратора событий, создавая приемники для всех секций в группе потребителей концентратора событий. Он регулярно фиксирует метаданные для полученных сообщений в Azure Storage Blob. Благодаря такому подходу позже можно легко продолжить получать сообщения с того места, где вы остановились.

Код из этого краткого руководства доступен [на сайте GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>Клонирование репозитория Git
Скачайте или клонируйте [пример](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) с GitHub. 

### <a name="install-the-eventprocessorhost"></a>Установка EventProcessorHost
Установите EventProcessorHost для модуля Центров событий. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Получение событий с помощью EventProcessorHost
Клонированный пакет SDK содержит несколько примеров, которые показывают, как получать события от концентратора событий с помощью Node.js. В этом кратком руководстве используется пример **singleEPH.js**. Чтобы увидеть получение событий, откройте другой терминал и отправьте события с помощью [примера отправки](event-hubs-node-get-started-send.md).

1. Откройте проект в Visual Studio Code. 
2. Создайте файл с именем **.env** в папке **processor**. Скопируйте и вставьте в него пример переменных среды из **sample.env** в корневой папке.
3. Настройте строку подключения, имя концентратора событий и конечную точку хранилища. Вы можете скопировать строку подключения для концентратора событий из поля **Строка подключения — первичный ключ** в разделе **RootManageSharedAccessKey** на странице Центра событий на портале Azure. Подробные сведения см. в [этом разделе](event-hubs-create.md#create-an-event-hubs-namespace).
4. В интерфейсе командной строки Azure перейдите к папке **processor**. Установите пакеты узла и создайте проект, выполнив следующие команды:

    ```shell
    npm i
    npm run build
    ```
5. Получите события с помощью узла обработчика событий путем выполнения следующей команды:

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>Просмотр примера кода 
Ниже приведен пример кода для получения событий от концентратора событий с помощью Node.js. Вы можете вручную создать файл sampleEph.js и запустить его, чтобы получать события в концентраторе событий. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

Прежде чем выполнять этот скрипт, не забудьте задать переменные среды. Вы можете настроить это в командной строке, как показано в следующем примере, или использовать [пакет dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Дополнительные примеры можно найти [здесь](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Возможностями и терминологией в концентраторах событий Azure](event-hubs-features.md)
- [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)
- Просмотрите другие примеры Node.js для Центров событий на сайте [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
