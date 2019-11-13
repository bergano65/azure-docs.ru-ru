---
title: Краткое руководство. Использование разделов и подписок служебной шины Azure с Node.js
description: Краткое руководство. Узнайте, как использовать разделы и подписки служебной шины в Azure в приложении Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: fa6f40eba02ffe171dc521f952e0d00fc35fc7e6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721659"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Краткое руководство. Использование разделов и подписок служебной шины с Node.js и пакетом azure/service-bus
> [!div class="op_multi_selector" title1="Язык программирования" title2="Пакет Node.js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Из этого учебника вы узнаете, как написать программу Node.js для отправки сообщений в раздел служебной шины и получения сообщений из подписки служебной шины с помощью нового пакета [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus). Этот пакет использует более быстрый [протокол AMQP 1.0](service-bus-amqp-overview.md) вместо [интерфейсов REST API для служебной шины](/rest/api/servicebus/service-bus-runtime-rest), как в старом пакете [azure-sb](https://www.npmjs.com/package/azure-sb). Примеры кода написаны на JavaScript.

## <a name="prerequisites"></a>Предварительные требования
- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать [преимущества подписчика MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Если у вас нет подходящих раздела и подписки, создайте их по инструкциям из статьи [Краткое руководство. Создание раздела служебной шины и подписок на него с помощью портала Azure](service-bus-quickstart-topics-subscriptions-portal.md). Запишите строку подключения к экземпляру служебной шины и имена созданных раздела и подписки. Эти значения вам понадобятся для приведенных ниже примеров.

> [!NOTE]
> - Примеры для этого учебника вы можете скопировать и запустить с помощью [Nodejs](https://nodejs.org/). Указания по созданию приложения Node.js можно найти в статьях [Создание веб-приложений Node.js в Azure](../app-service/app-service-web-get-started-nodejs.md) или [Build and deploy a Node.js application to an Azure Cloud Service](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (Построение и развертывание приложения Node.js в облачной службе Azure).
> - Новый пакет [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) пока не поддерживает создание разделов и подписок. Чтобы создать их программным образом, используйте пакет [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Использование диспетчера пакета Node (NPM) для установки пакета
Чтобы установить пакет npm для служебной шины, откройте командную строку, где `npm` сохранен в переменной окружения path, перейдите в папку, в которой вы хотите сохранить примеры, и выполните эту команду.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел
Взаимодействие с разделом служебной шины начинается с создания экземпляра класса [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) и его применения для создания экземпляра класса [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient). Получив клиент раздела, вы сможете создать отправителя и использовать его методы [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) и [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) для отправки сообщений.

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте файл с именем `send.js` и вставьте в него приведенный ниже код. Этот код отправляет в указанный раздел 10 сообщений.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Введите строку подключения и имя раздела в приведенный выше код.
4. Затем в окне командной строки выполните команду `node send.js`, которая запускает этот файл. 

Поздравляем! Вы отправили сообщения в очередь служебной шины.

Сообщения имеют несколько стандартных свойств, например `label` и `messageId`, которые можно задать при отправке. Если вы хотите применить пользовательские свойства, используйте объект JSON `userProperties` для хранения пар "ключ — значение" с пользовательскими данными.

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Ограничения количества сообщений в разделе нет, но существует ограничение на общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки
Взаимодействие с подпиской служебной шины начинается с создания экземпляра класса [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) и применения его для создания экземпляра класса [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient). Получив клиент подписки, вы сможете создать приемник и использовать его методы [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) и [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) для получения сообщений.

1. Откройте редактор, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте файл с именем `recieve.js` и вставьте в него приведенный ниже код. Этот код пытается получить из указанной подписки 10 сообщений. Реальное число полученных сообщений будет зависеть от количества доступных в подписке сообщений и задержки сети.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Введите строку подключения, имена раздела и подписки в приведенный выше код.
4. Затем в окне командной строки выполните команду `node receiveMessages.js`, которая запускает этот файл.

Поздравляем! Вы получили сообщения из подписки служебной шины.

Метод [createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) принимает перечисление `ReceiveMode` со значениями [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) и [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Не забудьте [сопоставить сообщения](message-transfers-locks-settlement.md#settling-receive-operations), если вы используете для сообщения режим `PeekLock` с любым из методов `complete()`, `abandon()`, `defer()` или `deadletter()`.

## <a name="subscription-filters-and-actions"></a>Фильтры и действия подписки
Служебная шина поддерживает [фильтры и действия для подписок](topic-filters.md), которые позволяют фильтровать поступающие в подписку сообщения и изменять их свойства.

После получения экземпляра `SubscriptionClient` вы можете использовать приведенные ниже методы для получения, добавления и удаления правил, которые управляют фильтрами и действиями в подписке.

- getRules
- addRule
- removeRule

У каждой подписки есть правило по умолчанию с фильтром true, который разрешает все входящие сообщения. При добавлении нового правила не забудьте удалить фильтр по умолчанию, чтобы применялся фильтр в новом правиле. Если в подписке нет правил, она не будет получать сообщения.

> [!NOTE]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель служебной шины позволяет без труда подключаться к пространству имен служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта или возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительных сведений ознакомьтесь со следующими ресурсами:

- [Очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md)
- Просмотрите другие [примеры Node.js для служебной шины на сайте GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript).
- [Центр разработчика Node.js](https://azure.microsoft.com/develop/nodejs/)


