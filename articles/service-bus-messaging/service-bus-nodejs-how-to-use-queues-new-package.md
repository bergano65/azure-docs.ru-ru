---
title: Как использовать очереди шины Azure или службы в Node. js
description: Узнайте, как использовать очереди служебной шины в Azure из приложения Node. js с помощью пакета Azure/Service-Bus.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 58049855cc27d51134b9f76a773f32f49c6381b6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790311"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Как использовать очереди служебной шины с Node. js и пакетом Azure/Service-Bus
> [!div class="op_multi_selector" title1="Язык программирования" title2="Пакета Node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

В этом руководстве вы узнаете, как написать программу NodeJS для отправки и получения сообщений из очереди служебной шины с помощью нового пакета [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Этот пакет использует более быстрый [протокол AMQP 1,0](service-bus-amqp-overview.md) , в то время как более старый пакет [Azure-SB](https://www.npmjs.com/package/azure-sb) использовал [API среды выполнения служебной шины](/rest/api/servicebus/service-bus-runtime-rest). Примеры написаны на JavaScript.

## <a name="prerequisites"></a>Технические условия
- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Если у вас нет очереди для работы, выполните действия, описанные в статье [использование портал Azure для создания очереди служебной шины](service-bus-quickstart-portal.md) для создания очереди. Запишите строку подключения для экземпляра служебной шины и имя созданной очереди. Мы будем использовать эти значения в примерах.

> [!NOTE]
> - Этот учебник работает с примерами, которые можно скопировать и запустить с помощью [NodeJS](https://nodejs.org/). Инструкции по созданию приложения Node. js см. в статье [Создание и развертывание приложения Node. js на веб-сайте Azure](../app-service/app-service-web-get-started-nodejs.md)или в [облачной службе Node. js с помощью Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Новый пакет [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) еще не поддерживает создание очередей. Используйте пакет [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) , если вы хотите создать их программными средствами.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Использование диспетчера пакета Node (NPM) для установки пакета
Чтобы установить пакет NPM для служебной шины, откройте командную строку с `npm` в ее пути, измените каталог на папку, в которой вы хотите получить примеры, а затем выполните эту команду.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
Взаимодействие с очередью служебной шины начинается с создания экземпляра класса [сервицебусклиент](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) и его использования для создания экземпляра класса [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Получив клиент очереди, можно создать отправителя и использовать для отправки сообщений метод [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) или [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) .

1. Откройте любой редактор, например [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте файл с именем `send.js` и вставьте в него приведенный ниже код. Этот код будет передавать в очередь 10 сообщений.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Введите строку подключения и имя своей очереди в приведенном выше коде.
4. Затем выполните команду `node send.js` в командной строке, чтобы выполнить этот файл.

Поздравляем! Вы только что отправляли сообщения в очередь служебной шины.

Сообщения имеют некоторые стандартные свойства, такие как `label` и `messageId`, которые можно задать при отправке. Если вы хотите задать пользовательские свойства, используйте `userProperties`, который является объектом JSON, который может содержать пары "ключ-значение" пользовательских данных.

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Количество сообщений, хранящихся в очереди, не ограничено, но существует ограничение общего размера сообщений, хранящихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
Взаимодействие с очередью служебной шины начинается с создания экземпляра класса [сервицебусклиент](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) и его использования для создания экземпляра класса [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Получив клиент очереди, можно создать приемник и использовать для него метод [рецеивемессажес](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) или [регистермессажехандлер](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) для получения сообщений.

1. Откройте любой редактор, например [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте файл с именем `recieve.js` и вставьте в него приведенный ниже код. Этот код попытается получить 10 сообщений из очереди. Фактическое количество получаемых данных зависит от количества сообщений в очереди и задержки сети.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Введите строку подключения и имя своей очереди в приведенном выше коде.
4. Затем выполните команду `node receiveMessages.js` в командной строке, чтобы выполнить этот файл.

Поздравляем! Вы только что получили сообщения из очереди служебной шины.

Метод [креатерецеивер](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) принимает `ReceiveMode`, который является перечислением со значениями [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) и [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Не забывайте [сопоставлять сообщения](message-transfers-locks-settlement.md#settling-receive-operations) , если вы используете `PeekLock` режим с помощью любого из методов `complete()`, `abandon()`, `defer()`или `deadletter()` для сообщения.

> [!NOTE]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель служебной шины позволяет без труда подключаться к пространству имен служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта или возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в следующих ресурсах.
- [Очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md)
- Извлечение других [примеров NodeJS для служебной шины на GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Центр разработчиков Node.js.](https://azure.microsoft.com/develop/nodejs/)

