---
title: Как использовать очереди служебной шины Azure в Node.js — azure и служебной шины | Документация Майкрософт
description: Узнайте, как использовать очереди служебной шины в Azure в приложении Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988350"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Как использовать очереди служебной шины с Node.js и пакет azure и служебной шины
> [!div class="op_multi_selector" title1="Язык программирования" title2="Пакета node.js"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

В этом руководстве вы узнаете, как написать программу Nodejs, чтобы отправлять и получать сообщения из очереди служебной шины с помощью нового [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) пакета. Этот пакет использует быстрее [протокол AMQP 1.0](service-bus-amqp-overview.md) то время как более старую версию [azure-sb](https://www.npmjs.com/package/azure-sb) пакет, используемый [API среды выполнения служебной шины REST](/rest/api/servicebus/service-bus-runtime-rest). Примеры написаны на языке JavaScript.

## <a name="prerequisites"></a>Технические условия
- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать ваши [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Если у вас нет очереди для работы с, выполните шаги [с помощью портала Azure создать очередь служебной шины](service-bus-quickstart-portal.md) статью, чтобы создать очередь. Запишите строку подключения для служебной шины экземпляра и имя созданной очереди. Мы будем использовать эти значения в образцах.

> [!NOTE]
> - В этом руководстве используется с примерами, которые можно скопировать и запустить с помощью [Nodejs](https://nodejs.org/). Инструкции по созданию приложения Node.js см. в разделе [Создание и развертывание приложения Node.js на веб-сайте Azure](../app-service/app-service-web-get-started-nodejs.md), или [Node.js в облачной службе с помощью Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - Новый [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) пакет не поддерживает создание очередей еще. Используйте [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) пакета, если вы хотите создать их программно.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Использование диспетчера пакета Node (NPM) для установки пакета
Чтобы установить пакет npm для служебной шины, откройте командную строку с `npm` в пути, перейдите в каталог на папку, где вы хотите иметь ваших примеров и затем выполните следующую команду.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
Взаимодействие с Service Bus очереди начинается с создания экземпляра [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) класс и использовать его для создания [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) класса. Когда клиент очереди, можно создать отправителя и использовать [отправки](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) или [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) метод для отправки сообщений.

1. Откройте предпочитаемый редактор, такой как [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте файл с именем `send.js` и вставьте ниже кода в него. Этот код отправит 10 сообщений в очередь.

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
3. Введите строку подключения и имя очереди, в приведенном выше коде.
4. Затем выполните команду `node send.js` в командной строке, чтобы выполнить этот файл.

Поздравляем! Вы просто отправлять сообщения в очередь служебной шины.

Сообщения имеют некоторые стандартные свойства, такие как `label` и `messageId` , которые можно задать при отправке. Если вы хотите задать все настраиваемые свойства, используйте `userProperties`, который представляет собой объект json, который может содержать пары ключ значение пользовательских данных.

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Нет ограничений на количество сообщений в очереди, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
Взаимодействие с Service Bus очереди начинается с создания экземпляра [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) класс и использовать его для создания [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) класса. Когда клиент очереди, можно создать получатель и использовать либо [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) или [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) метод для получения сообщений.

1. Откройте предпочитаемый редактор, такой как [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте файл с именем `recieve.js` и вставьте ниже кода в него. Этот код будет предпринята попытка получить 10 сообщений из очереди. Фактическое число получаемых зависит от количества сообщений в очереди и задержку сети.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
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
3. Введите строку подключения и имя очереди, в приведенном выше коде.
4. Затем выполните команду `node receiveMessages.js` в командной строке, чтобы выполнить этот файл.

Поздравляем! Вы просто получать сообщения из очереди служебной шины.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) метод принимает `ReceiveMode` которого является перечислением со значениями [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) и [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations). Не забудьте [сопоставления сообщений](message-transfers-locks-settlement.md#settling-receive-operations) при использовании `PeekLock` режиме с помощью любого из `complete()`, `abandon()`, `defer()`, или `deadletter()` методов в сообщения.

> [!NOTE]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель служебной шины позволяет без труда подключаться к пространству имен служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта или возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. следующие ресурсы.
- [Очереди, разделы и подписки](service-bus-queues-topics-subscriptions.md)
- Просмотрите другие [Nodejs примеры для служебной шины на сайте GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Центр разработчика Node.js](https://azure.microsoft.com/develop/nodejs/)

