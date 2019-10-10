---
title: Использование разделов и подписок служебной шины Azure с Node.js | Документация Майкрософт
description: Узнайте, как использовать разделы и подписки служебной шины в Azure в приложении Node.js.
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
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: f927274e1e866a9cba72330280316cc5ee7d8047
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178057"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Использование разделов и подписок служебной шины с Node. js и пакетом Azure/Service-Bus
> [!div class="op_multi_selector" title1="Язык программирования" title2="Пакета Node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

В этом руководстве вы узнаете, как написать программу Node. js для отправки сообщений в раздел служебной шины и получать сообщения из подписки служебной шины с помощью нового пакета [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Этот пакет использует более быстрый [протокол AMQP 1,0](service-bus-amqp-overview.md) , в то время как более старый пакет [Azure-SB](https://www.npmjs.com/package/azure-sb) использовал [API среды выполнения служебной шины](/rest/api/servicebus/service-bus-runtime-rest). Примеры написаны на JavaScript.

## <a name="prerequisites"></a>Предварительные требования
- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Если у вас нет раздела и подписки для работы, выполните действия, описанные в статье [использование портал Azure для создания разделов и подписок служебной шины](service-bus-quickstart-topics-subscriptions-portal.md) , чтобы создать их. Запишите строку подключения для экземпляра служебной шины и имена созданного раздела и подписки. Мы будем использовать эти значения в примерах.

> [!NOTE]
> - Этот учебник работает с примерами, которые можно скопировать и запустить с помощью [NodeJS](https://nodejs.org/). Инструкции по созданию приложения Node. js см. в статье [Создание и развертывание приложения Node. js на веб-сайте Azure](../app-service/app-service-web-get-started-nodejs.md)или в [облачной службе Node. js с помощью Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Новый пакет [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) еще не поддерживает создание топЦис и подписок. Используйте пакет [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) , если вы хотите создать их программными средствами.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Использование диспетчера пакета Node (NPM) для установки пакета
Чтобы установить пакет NPM для служебной шины, откройте командную строку с `npm` по ее пути, измените каталог на папку, в которой вы хотите получить примеры, а затем выполните эту команду.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел
Взаимодействие с разделом служебной шины начинается с создания экземпляра класса [сервицебусклиент](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) и его использования для создания экземпляра класса [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) . После создания раздела клиент можно создать отправителя и использовать для отправки сообщений метод [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) или [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) .

1. Откройте любой редактор, например [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте файл с именем `send.js` и вставьте в него приведенный ниже код. Этот код отправляет 10 сообщений в раздел.

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
3. Введите строку подключения и имя раздела в приведенном выше коде.
4. Затем выполните команду `node send.js` в командной строке, чтобы выполнить этот файл. 

Поздравляем! Вы только что отправляли сообщения в очередь служебной шины.

Сообщения имеют стандартные свойства, такие как `label` и `messageId`, которые можно задать при отправке. Если вы хотите задать пользовательские свойства, используйте `userProperties`, который является объектом JSON, который может содержать пары "ключ-значение" пользовательских данных.

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Количество сообщений, хранящихся в разделе, не ограничено, но существует ограничение общего размера сообщений, хранящихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки
Взаимодействие с подпиской служебной шины начинается с создания экземпляра класса [сервицебусклиент](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) и его использования для создания экземпляра класса [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) . После создания клиента подписки можно создать приемник и использовать для него метод [рецеивемессажес](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) или [регистермессажехандлер](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) для получения сообщений.

1. Откройте любой редактор, например [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте файл с именем `recieve.js` и вставьте в него приведенный ниже код. Этот код попытается получить 10 сообщений от вашей подписки. Фактическое количество получаемых данных зависит от количества сообщений в подписке и задержки сети.

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
3. Введите строку подключения и имена раздела и подписки в приведенном выше коде.
4. Затем выполните команду `node receiveMessages.js` в командной строке, чтобы выполнить этот файл.

Поздравляем! Вы только что получили сообщения от подписки служебной шины.

Метод [креатерецеивер](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) принимает `ReceiveMode`, который является перечислением со значениями [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) и [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Не забывайте [сопоставлять сообщения](message-transfers-locks-settlement.md#settling-receive-operations) , если используется режим `PeekLock` с помощью любого из методов `complete()`, `abandon()`, `defer()` или `deadletter()` для сообщения.

## <a name="subscription-filters-and-actions"></a>Фильтры и действия подписки
Служебная шина поддерживает [фильтры и действия в подписках](topic-filters.md), что позволяет фильтровать входящие сообщения в подписку и изменять их свойства.

Получив экземпляр `SubscriptionClient`, вы можете использовать приведенные ниже методы для получения, добавления и удаления правил в подписке для управления фильтрами и действиями.

- Правила
- аддруле
- ремоверуле

У каждой подписки есть правило по умолчанию, которое использует фильтр true, чтобы разрешить все входящие сообщения. При добавлении нового правила не забудьте удалить фильтр по умолчанию, чтобы фильтр в новом правиле работал. Если у подписки нет правил, то она не получит сообщений.

> [!NOTE]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель служебной шины позволяет без труда подключаться к пространству имен служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта или возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения см. в следующих ресурсах.

- [Очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md)
- Извлечение других [примеров NodeJS для служебной шины на GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Центр разработчика Node.js](https://azure.microsoft.com/develop/nodejs/)


