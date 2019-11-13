---
title: Краткое руководство. Использование очередей azure/service-bus в Node.js
description: Краткое руководство. Сведения о том, как использовать очереди служебной шины в Azure в приложении Node.js с помощью пакета azure/service-bus.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 9901ccd6bb1abf27bb1141c618d0bfde167b9cc3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721687"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Краткое руководство. Использование очередей служебной шины в Azure с помощью Node.js и пакета azure/service-bus
> [!div class="op_multi_selector" title1="Язык программирования" title2="Пакет Node.js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Из этого учебника вы узнаете, как написать программу Node.js для отправки и получения сообщений через очередь служебной шины с помощью нового пакета [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus). Этот пакет использует более быстрый [протокол AMQP 1.0](service-bus-amqp-overview.md) вместо [интерфейсов REST API для служебной шины](/rest/api/servicebus/service-bus-runtime-rest), как в старом пакете [azure-sb](https://www.npmjs.com/package/azure-sb). Примеры кода написаны на JavaScript.

## <a name="prerequisites"></a>Предварительные требования
- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать [преимущества подписчика MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Если у вас нет подходящей очереди служебной шины, [создайте ее с помощью портала Azure](service-bus-quickstart-portal.md). Запишите строку подключения к экземпляру служебной шины и имя созданной очереди. Эти значения вам понадобятся для приведенных ниже примеров.

> [!NOTE]
> - Примеры для этого учебника вы можете скопировать и запустить с помощью [Nodejs](https://nodejs.org/). Указания по созданию приложения Node.js можно найти в статьях [Создание веб-приложений Node.js в Azure](../app-service/app-service-web-get-started-nodejs.md) или [Build and deploy a Node.js application to an Azure Cloud Service](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (Построение и развертывание приложения Node.js в облачной службе Azure).
> - Новый пакет [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) пока не поддерживает создание очередей. Чтобы создать их программным образом, используйте пакет [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Использование диспетчера пакета Node (NPM) для установки пакета
Чтобы установить пакет npm для служебной шины, откройте командную строку, где `npm` сохранен в переменной окружения path, перейдите в папку, в которой вы хотите сохранить примеры, и выполните эту команду.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
Взаимодействие с очередью служебной шины начинается с создания экземпляра класса [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) и применения его для создания экземпляра класса [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient). Получив клиент очереди, вы сможете создать отправителя и использовать его методы [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) и [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) для отправки сообщений.

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте файл с именем `send.js` и вставьте в него приведенный ниже код. Этот код отправляет в указанную очередь 10 сообщений.

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
3. Введите строку подключения и имя очереди в приведенный выше код.
4. Затем в окне командной строки выполните команду `node send.js`, которая запускает этот файл.

Поздравляем! Вы отправили сообщения в очередь служебной шины.

Сообщения имеют несколько стандартных свойств, например `label` и `messageId`, которые можно задать при отправке. Если вы хотите применить пользовательские свойства, используйте объект JSON `userProperties` для хранения пар "ключ — значение" с пользовательскими данными.

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Число сообщений в очереди может быть любым, но действует ограничение на общий размер сообщений в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
Взаимодействие с очередью служебной шины начинается с создания экземпляра класса [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) и применения его для создания экземпляра класса [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient). Получив клиент очереди, вы сможете создать приемник и использовать его методы [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) и [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) для получения сообщений.

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте файл с именем `recieve.js` и вставьте в него приведенный ниже код. Этот код пытается получить из указанной очереди 10 сообщений. Реальное число полученных сообщений будет зависеть от количества доступных в очереди сообщений и задержки сети.

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
3. Введите строку подключения и имя очереди в приведенный выше код.
4. Затем в окне командной строки выполните команду `node receiveMessages.js`, которая запускает этот файл.

Поздравляем! Вы получили сообщения из очереди служебной шины.

Метод [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) принимает перечисление `ReceiveMode` со значениями [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) и [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Не забудьте [сопоставить сообщения](message-transfers-locks-settlement.md#settling-receive-operations), если вы используете для сообщения режим `PeekLock` с любым из методов `complete()`, `abandon()`, `defer()` или `deadletter()`.

> [!NOTE]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель служебной шины позволяет без труда подключаться к пространству имен служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта или возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Дополнительная информация
Для получения дополнительных сведений ознакомьтесь со следующими ресурсами:
- [Очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md)
- Просмотрите другие [примеры Node.js для служебной шины на сайте GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript).
- [Центр разработчика Node.js](https://azure.microsoft.com/develop/nodejs/)

