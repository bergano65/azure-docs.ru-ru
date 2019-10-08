---
title: Использование очередей служебной шины Azure в Node. js
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
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 1aba29f8ed7cacb8f2911ae2d37358869e6a7730
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001122"
---
# <a name="use-service-bus-queues-in-azure-with-nodejs-and-the-azure-sb-package"></a>Использование очередей служебной шины в Azure с Node. js и пакетом Azure-SB
> [!div class="op_multi_selector" title1="Язык программирования" title2="Пакета Node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

В этом руководстве вы узнаете, как создавать приложения Node. js для отправки и получения сообщений из очереди служебной шины Azure с помощью пакета [Azure-SB](https://www.npmjs.com/package/azure-sb) . Примеры написаны на JavaScript и используют [модуль Azure](https://www.npmjs.com/package/azure) Node. js, который внутренне использует пакет Azure-SB.

Пакет [Azure-SB](https://www.npmjs.com/package/azure-sb) использует [интерфейсы API среды выполнения служебной шины](/rest/api/servicebus/service-bus-runtime-rest). Вы можете повысить скорость работы, используя новый [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) , который использует более быстрый [протокол AMQP 1,0](service-bus-amqp-overview.md). Дополнительные сведения о новом пакете см. в статье [Использование очередей служебной шины с пакетами Node. js и @azure/service-bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package). в противном случае продолжите чтение, чтобы увидеть, как использовать пакет [Azure](https://www.npmjs.com/package/azure) .

## <a name="prerequisites"></a>Предварительные требования
- Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Если у вас нет очереди для работы, выполните действия, описанные в статье [использование портал Azure для создания очереди служебной шины](service-bus-quickstart-portal.md) для создания очереди.
    1. Ознакомьтесь с кратким **обзором** **очередей**служебной шины. 
    2. Создайте **пространство имен**служебной шины. 
    3. Возвращает **строку подключения**. 

        > [!NOTE]
        > Вы создадите **очередь** в пространстве имен служебной шины с помощью Node. js в этом руководстве. 
 

## <a name="create-a-nodejs-application"></a>Создание приложения Node.js
Создайте пустое приложение Node.js. Указания по созданию приложения Node.js можно найти в статьях [Начало работы с веб-приложениями Node.js в службе приложений Azure][Create and deploy a Node.js application to an Azure Website] или [Построение и развертывание приложения Node.js в облачной службе Azure][Node.js Cloud Service] (с помощью Windows PowerShell).

## <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Чтобы использовать служебную шину Azure, необходимо скачать и запустить пакет Azure для Node.js. Пакет содержит набор библиотек, взаимодействующих со службами REST Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Использование диспетчера пакета Node (NPM) для получения пакета
1. Используя командное окно **Windows PowerShell для Node.js**, перейдите в папку **C:\\node\\sbqueues\\WebRole1**, в которой создан пример приложения.
2. Введите **NPM Install Azure** в командном окне, что должно привести к результату, как в следующем примере:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Выполнив команду **ls** вручную, можно убедиться, что папка **node_modules** создана. В этой папке найдите пакет **Azure** , который содержит библиотеки, необходимые для доступа к очередям служебной шины.

### <a name="import-the-module"></a>Импорт модуля
С помощью Блокнота или другого текстового редактора добавьте в начало файла **server.js** приложения следующее:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Настройка подключения к служебной шине Azure
Модуль Azure считывает переменную среды `AZURE_SERVICEBUS_CONNECTION_STRING`, чтобы получить сведения, необходимые для подключения к служебной шине. Если эта переменная среды не задана, необходимо указать сведения об учетной записи при вызове `createServiceBusService`.

Пример настройки переменных среды в [портал Azure][Azure portal] для веб-сайта Azure см. в разделе [веб-приложение Node. js с хранилищем][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Создать очередь
Объект **ServiceBusService** позволяет работать с очередями служебной шины. Следующий код создает объект **ServiceBusService**. Добавьте его в начало файла **server.js** после оператора импорта модуля Аzure.

```javascript
var serviceBusService = azure.createServiceBusService();
```

Вызов `createQueueIfNotExists` для объекта **ServiceBusService** возвращает указанную очередь (при ее наличии) или создает новую очередь с указанным именем. В следующем коде используется метод `createQueueIfNotExists`, чтобы создать очередь `myqueue` или подключиться к ней.

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Метод `createServiceBusService` также поддерживает дополнительные параметры, позволяющие переопределить настройки очереди по умолчанию, такие как срок жизни сообщения или максимальный размер очереди. В следующем примере показано, как установить максимальный размер очереди 5 ГБ и срок жизни 1 минуту.

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Фильтры
Используя **ServiceBusService**, к выполняемым операциям можно применить дополнительные операции фильтрации. К операциям фильтрации могут относиться ведение журнала, автоматический повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

```javascript
function handle (requestOptions, next)
```

Выполнив предварительную обработку параметров запроса, метод должен вызвать `next`, передавая обратный вызов со следующей сигнатурой:

```javascript
function (returnObject, finalCallback, next)
```

В этом обратном вызове и после обработки `returnObject` (ответ от запроса к серверу) обратный вызов должен либо вызвать `next`, если он существует, чтобы продолжить обработку других фильтров, либо вызвать метод `finalCallback`, который завершает вызов службы.

Два фильтра (`ExponentialRetryPolicyFilter` и `LinearRetryPolicyFilter`), которые позволяют реализовать логику повторных попыток, входят в состав пакета Azure SDK для Node.js. Следующий код создает объект `ServiceBusService`, который использует метод `ExponentialRetryPolicyFilter`.

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
Чтобы отправить сообщение в очередь служебной шины, приложение вызывает метод `sendQueueMessage` для объекта **ServiceBusService**. Сообщения, отправляемые в очереди служебной шины и получаемые из них, — это объекты **BrokeredMessage**, которые имеют набор стандартных свойств (таких как **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав строку как сообщение. Все необходимые стандартные свойства будут заполнены значениями по умолчанию.

В следующем примере показано, как отправить тестовое сообщение в очередь с именем `myqueue` с помощью метода `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Количество сообщений, хранящихся в очереди, не ограничено, но существует ограничение общего размера сообщений, хранящихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
Сообщения извлекаются из очереди с помощью метода `receiveQueueMessage` для объекта **ServiceBusService**. По умолчанию прочитанные сообщения удаляются из очереди. Но можно прочитать (просмотреть) сообщение и заблокировать его без удаления из очереди, задав для необязательного параметра `isPeekLock` значение **true**.

Поведение по умолчанию при чтении и удалении сообщения в рамках операции получения является самой простой моделью, что лучше всего подходит для сценариев, в которых приложение может не обрабатывать сообщения при возникновении сбоя. Чтобы понять это поведение, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Так как служебная шина будет помечать сообщение как использованное, тогда, когда приложение перезапускается и снова начнет потреблять сообщения, оно пропустит сообщение, которое было использовано до сбоя.

Если для параметра `isPeekLock` задано значение **true**, то получение становится операцией с двумя этапами, что позволяет поддерживать приложения, которые не могут допускать отсутствующих сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Обработав сообщение (или сохранив его в надежном месте для последующей обработки), приложение вызывает метод `deleteMessage` и указывает сообщение, которое будет удалено как параметр, таким образом завершая второй этап процесса получения. Метод `deleteMessage` помечает сообщение как использованное и удаляет его из очереди.

В следующем примере показано, как получать и обрабатывать сообщения с помощью метода `receiveQueueMessage`. Сначала приложение получает и удаляет сообщение в примере, затем получает его с помощью параметра `isPeekLock`, для которого задано значение **true**, и удаляет с помощью метода `deleteMessage`.

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Как обрабатывать сбои приложения и нечитаемые сообщения
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если по какой-либо причине приложению-получателю не удается обработать сообщение, оно вызывает метод `unlockMessage` для объекта **ServiceBusService**. Это приведет к тому, что служебная шина разблокирует сообщение в очереди и сделает его доступным для повторного получения одним и тем же приложением или другим приложением, которое использует приложение.

Кроме того, имеется время ожидания, связанное с сообщением, заблокированным в очереди, и если приложение не может обработать сообщение до истечения времени ожидания блокировки (например, если приложение аварийно завершает работу), служебная шина автоматически разблокирует сообщение и сделает его доступно для повторного получения.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода `deleteMessage`, сообщение будет повторно доставлено в приложение после его перезапуска. Такой подход часто вызывается *по крайней мере один раз при обработке*, то есть каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях одно и то же сообщение может быть доставлено снова. Если сценарий не допускает обработки дубликатов, разработчики приложений должны добавить в свое приложение дополнительную логику для обработки повторяющихся сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным в рамках попыток доставки.

> [!NOTE]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель служебной шины позволяет без труда подключаться к пространству имен служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта или возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Следующие шаги
Дополнительную информацию об очередях см. в следующих ресурсах.

* [Очереди, разделы и подписки][Queues, topics, and subscriptions]
* Репозиторий [пакетов SDK Azure для Node][Azure SDK for Node] на сайте GitHub
* [Центр разработчика Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
