---
title: Получение событий от Центров событий Azure с помощью Node.js | Документация Майкрософт
description: Узнайте, как получать события из Центров событий с помощью Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 6d5b52c8a5dd0306a349cac5e67eecc809005c6f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429190"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Получение событий от Центров событий Azure с помощью Node.js

Концентраторы событий Azure — это высокомасштабируемая система управления событиями, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и другими системами. После сбора событий в концентратор событий вы можете получать и обрабатывать события с помощью внутрипроцессных обработчиков или перенаправлять их в другие аналитические системы. Вы также можете записать данные события в службу хранилища Azure или Azure Data Lake Storage перед их обработкой.  

Общие сведения о службе "Центры событий" см. в [этой статье](event-hubs-about.md).

В этом руководстве показано, как получать события от концентратора событий, используя [EventProcessorHost](event-hubs-event-processor-host.md) Azure в приложении Node.js. Класс EventProcessorHost (EPH) помогает эффективно получать события от концентратора событий, создавая приемники для всех секций в группе потребителей концентратора событий. Он регулярно фиксирует метаданные для полученных сообщений в Azure Storage Blob. Благодаря такому подходу позже можно легко продолжить получать сообщения с того места, где вы остановились.

Код из этого краткого руководства доступен [на сайте GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

> [!NOTE]
>  Чтобы отправлять события в Центры событий с помощью Node.js, ознакомьтесь со статьей: [Send events to Azure Event Hubs using Node.js](event-hubs-node-get-started-send.md) (Отправка событий в Центры событий Azure с помощью Node.js). 

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

- Node.js версии 8.x или более поздней. Скачайте последнюю версию LTS с сайта [https://nodejs.org](https://nodejs.org). Не используйте более старую версию LTS Node.js. 
- Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][], прежде чем начинать работу.

## <a name="create-a-namespace-and-event-hub"></a>Создание пространства имен и концентратора событий
Первым шагом является создание пространства имен Центров событий с помощью портала Azure. Если у вас нет таких сущностей, создайте их, следуя инструкциям в статье [Создание пространства имен Центров событий и концентратора событий с помощью портала Azure](event-hubs-create.md).

## <a name="create-a-storage-account-and-container"></a>Создание учетной записи хранения и контейнера
Для использования класса EventProcessorHost необходимо настроить учетную запись хранения Azure. Такая информация о состоянии, как аренда разделов и контрольные точки в потоке событий, совместно используются получателями через контейнер службы хранилища Azure. Учетную запись хранения Azure можно создать, следуя инструкциям в [этой статье](../storage/common/storage-quickstart-create-account.md).

## <a name="clone-the-git-repository"></a>Клонирование репозитория Git
Скачайте или клонируйте [пример](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) с GitHub. 

## <a name="install-the-eventprocessorhost"></a>Установка EventProcessorHost
Установите EventProcessorHost для модуля Центров событий. 

```nodejs
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>Получение событий с помощью EventProcessorHost
Клонированный пакет SDK содержит несколько примеров, которые показывают, как получать события от концентратора событий с помощью Node.js. В этом кратком руководстве используется пример **singleEPH.js**. Чтобы увидеть получение событий, откройте другой терминал и отправьте события с помощью [примера отправки](event-hubs-node-get-started-send.md).

1. Откройте проект в Visual Studio Code. 
2. Создайте файл с именем **.env** в папке **processor**. Скопируйте и вставьте в него пример переменных среды из **sample.env** в корневой папке.
3. Настройте строку подключения, имя концентратора событий и конечную точку хранилища. Вы можете скопировать строку подключения для концентратора событий из поля **Строка подключения — первичный ключ** в разделе **RootManageSharedAccessKey** на странице Центра событий на портале Azure. Подробные сведения см. в [этом разделе](event-hubs-create.md#create-an-event-hubs-namespace).
4. В интерфейсе командной строки Azure перейдите к папке **processor**. Установите пакеты узла и создайте проект, выполнив следующие команды:

    ```nodejs
    npm i
    npm run build
    ```
5. Получите события с помощью узла обработчика событий путем выполнения следующей команды:

    ```nodejs
    node dist/examples/singleEph.js
    ```

## <a name="review-the-sample-code"></a>Просмотр примера кода 
Ниже приведен пример кода для получения событий от концентратора событий с помощью Node.js. Вы можете вручную создать файл sampleEph.js и запустить его, чтобы получать события в концентраторе событий. 

  ```nodejs
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
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

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Дополнительные примеры можно найти [здесь](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать больше о Центрах событий, посетите следующие страницы:

* [Отправка событий в Центры событий с помощью Go](event-hubs-go-get-started-send.md)
* [Примеры Центров событий](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)
* [Функция "Сбор" в Центрах событий Azure](event-hubs-capture-overview.md)
* [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)

<!-- Links -->
[создайте бесплатную учетную запись Azure]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
