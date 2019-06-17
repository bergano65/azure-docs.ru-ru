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
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65992135"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Как использовать разделы служебной шины и подписки с помощью Node.js и пакет azure и служебной шины
> [!div class="op_multi_selector" title1="Язык программирования" title2="Пакета node.js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

В этом руководстве вы узнаете, как написать программу Node.js для отправки сообщений в раздел служебной шины и получения сообщений из подписки служебной шины с помощью нового [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) пакета. Этот пакет использует быстрее [протокол AMQP 1.0](service-bus-amqp-overview.md) то время как более старую версию [azure-sb](https://www.npmjs.com/package/azure-sb) пакет, используемый [API среды выполнения служебной шины REST](/rest/api/servicebus/service-bus-runtime-rest). Примеры написаны на языке JavaScript.

## <a name="prerequisites"></a>Технические условия
- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать ваши [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Если у вас нет раздела и подписки для работы с, выполните шаги [с помощью портала Azure для создания служебной шины разделов и подписок](service-bus-quickstart-topics-subscriptions-portal.md) статье, чтобы создать их. Запишите строку подключения для экземпляра Service Bus и имена раздела и подписки, которую вы создали. Мы будем использовать эти значения в образцах.

> [!NOTE]
> - В этом руководстве используется с примерами, которые можно скопировать и запустить с помощью [Nodejs](https://nodejs.org/). Инструкции по созданию приложения Node.js см. в разделе [Создание и развертывание приложения Node.js на веб-сайте Azure](../app-service/app-service-web-get-started-nodejs.md), или [Node.js в облачной службе с помощью Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Новый [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) пакет не поддерживает создание еще topcis и подписок. Используйте [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) пакета, если вы хотите создать их программно.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Использование диспетчера пакета Node (NPM) для установки пакета
Чтобы установить пакет npm для служебной шины, откройте командную строку с `npm` в пути, перейдите в каталог на папку, где вы хотите иметь ваших примеров и затем выполните следующую команду.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел
Взаимодействие с Service Bus раздел начинается с создания экземпляра [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) класс и использовать его для создания [TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) класса. Когда клиент раздела, можно создать отправителя и использовать [отправки](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) или [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) метод для отправки сообщений.

1. Откройте предпочитаемый редактор, такой как [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте файл с именем `send.js` и вставьте ниже кода в него. Этот код отправит 10 сообщений в раздел.

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

Поздравляем! Вы просто отправлять сообщения в очередь служебной шины.

Сообщения имеют некоторые стандартные свойства, такие как `label` и `messageId` , которые можно задать при отправке. Если вы хотите задать все настраиваемые свойства, используйте `userProperties`, который представляет собой объект json, который может содержать пары ключ значение пользовательских данных.

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Нет ограничений на количество сообщений, содержащихся в разделе, но есть ограничение на общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки
Взаимодействие с Service Bus подписки начинается с создания экземпляра [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) класс и использовать его для создания [SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) класса. Когда клиент подписки, можно создать получатель и использовать либо [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) или [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) метод для получения сообщений.

1. Откройте предпочитаемый редактор, такой как [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте файл с именем `recieve.js` и вставьте ниже кода в него. Этот код будет предпринята попытка получить 10 сообщений из подписки. Фактическое число получаемых зависит от количества сообщений в подписке и задержки в сети.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
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
3. Введите строку подключения и имена разделов и подписок в приведенном выше коде.
4. Затем выполните команду `node receiveMessages.js` в командной строке, чтобы выполнить этот файл.

Поздравляем! Вы просто получать сообщения из подписки служебной шины.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) метод принимает `ReceiveMode` которого является перечислением со значениями [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) и [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Не забудьте [сопоставления сообщений](message-transfers-locks-settlement.md#settling-receive-operations) при использовании `PeekLock` режиме с помощью любого из `complete()`, `abandon()`, `defer()`, или `deadletter()` методов в сообщения.

## <a name="subscription-filters-and-actions"></a>Фильтры подписки и действия
Служебная шина поддерживает [фильтры и действия по подписке на](topic-filters.md), который служит для фильтрации входящих сообщений для подписки и для редактирования их свойств.

Получив экземпляр `SubscriptionClient` можно использовать указанных ниже методов для создания, добавлять и удалять правила подписки для управления, фильтры и действия.

- getRules
- addRule
- removeRule

Каждая подписка имеет правило по умолчанию, которое использует значение true, фильтр, чтобы разрешить все входящие сообщения. Когда вы добавляете новое правило, не забудьте удалить фильтр по умолчанию в порядке для фильтра в новое правило для работы. Если подписка не имеет правил, он получит ни одного сообщения.

> [!NOTE]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель служебной шины позволяет без труда подключаться к пространству имен служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта или возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения см. следующие ресурсы.

- [Очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md)
- Просмотрите другие [Nodejs примеры для служебной шины на сайте GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Центр разработчика Node.js](https://azure.microsoft.com/develop/nodejs/)


