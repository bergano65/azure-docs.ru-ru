---
title: включить файл
description: включить файл
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: a50f09dd52ccf4d35fd3803967898a4a25e3299f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945628"
---
## <a name="prerequisites"></a>Предварительные требования
Перед началом работы нужно сделать следующее:

- Создайте учетную запись Azure с активной подпиской. Дополнительные сведения см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Установите [Node.js](https://nodejs.org/en/download/), активную версию LTS и версию Maintenance LTS (рекомендуются версии 8.11.1 и 10.14.1).
- Создайте ресурс Служб коммуникации Azure. Дополнительные сведения см. в статье [Краткое руководство по созданию ресурсов Служб коммуникации и управлению ими](../../create-communication-resource.md). Для работы с этим кратким руководством необходимо записать **конечную точку**.
- [Маркер доступа пользователя](../../access-tokens.md). Обязательно задайте для области значение chat и запишите строку маркера, а также строку userId.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-web-application"></a>Создание веб-приложения

Откройте терминал или командное окно, создайте каталог для своего приложения и перейдите к нему.

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
Воспользуйтесь командой `npm init -y`, чтобы создать файл **package.json** с параметрами по умолчанию.

```console
npm init -y
```

Используйте текстовый редактор, чтобы создать файл с именем **start-chat.js** в корневом каталоге проекта. В следующих разделах показано, как добавить в этот файл весь исходный код для примера из этого краткого руководства.

### <a name="install-the-packages"></a>Установка пакетов

Используйте команду `npm install`, чтобы установить следующие клиентские библиотеки Служб коммуникации для JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

Параметр `--save` указывает библиотеку как зависимость в файле пакета **package.json**.

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

В этом кратком руководстве для объединения ресурсов приложения используется webpack. Выполните следующую команду, чтобы установить пакеты npm webpack, webpack-cli и webpack-dev-server, а также указать их в качестве зависимостей разработки в файле **package.json**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Создайте файл **index.html** в корневом каталоге проекта. Мы будем использовать этот файл в качестве шаблона для добавления возможностей чата с помощью клиентской библиотеки чата Служб коммуникации Azure для JavaScript.

Ниже приведен код:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
Создайте файл в корневом каталоге проекта с именем **client.js**, чтобы включить логику приложения для этого краткого руководства. 

### <a name="create-a-chat-client"></a>Создание клиента чата

Чтобы создать клиент чата в веб-приложении, вы будете использовать конечную точку службы связи и маркер доступа, который был создан в рамках предварительных требований. Маркеры доступа пользователей позволяют создавать клиентские приложения, которые напрямую проходят проверку подлинности в Службах коммуникации Azure. После создания этих маркеров на сервере передайте их обратно клиентскому устройству. Для передачи маркера клиенту чата необходимо использовать класс `AzureCommunicationUserCredential` из `Common client library`.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
Замените значение **ENDPOINT** на значение конечной точки, созданной ранее в рамках работы со статьей [Краткое руководство по созданию ресурсов Служб коммуникации и управлению ими](../../create-communication-resource.md).
Замените значение **USER_ACCESS_TOKEN** маркером, созданным при работе со статьей о [маркере доступа пользователя](../../access-tokens.md).
Добавьте этот код в файл **client.js**.


### <a name="run-the-code"></a>Выполнение кода
Чтобы создать и запустить приложение, используйте `webpack-dev-server`. Выполните следующую команду, чтобы создать пакет узла приложения на локальном веб-сервере:
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Откройте веб-браузер и перейдите по адресу http://localhost:8080/.
В консоли средств разработчика в браузере должно отобразиться следующее:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Объектная модель 
Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки чата Служб коммуникации для JavaScript.

| Имя                                   | Описание                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Этот класс требуется для реализации всех функций чата. Его можно создать с помощью сведений о подписке и использовать для создания, получения и удаления потоков. |
| ChatThreadClient | Этот класс требуется для реализации всех функций потоков чата. Вы получаете экземпляр с помощью ChatClient и используете его для отправки, получения, обновления и удаления сообщений, добавления, удаления и получения пользователей, отправки уведомлений о вводе и уведомления о прочтении и подписки на чат. |


## <a name="start-a-chat-thread"></a>Запуск потока чата

Для создания потока чата используйте метод `createThread`.

`createThreadRequest` используется для описания запроса потока:

- Для указания раздела в этом чате используйте `topic`. Раздел можно обновить после создания потока чата с помощью функции `UpdateThread`. 
- Используйте `members`, чтобы получить список участников, добавляемых в поток чата.

При разрешении метод `createChatThread` возвращает `threadId`, который используется для выполнения операций с только что созданным потоком чата, например для добавления участников в поток, отправки, удаления сообщения и т. д.

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

Замените значения **USER_ID_FOR_JACK** и **USER_ID_FOR_GEETA** идентификаторами пользователей, полученными на предыдущем шаге (Создание пользователей и выдача [маркеров доступа пользователей](../../access-tokens.md)).

При обновлении вкладки браузера в консоли должны отобразиться следующие сведения.
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>Получение клиента потока чата

Метод `getChatThreadClient` возвращает `chatThreadClient` для потока, который уже существует. Его можно использовать для выполнения операций в созданном потоке: добавления участников, отправки сообщения и т. д. thread_id — уникальный идентификатор имеющегося потока чата.

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
Добавьте этот код вместо комментария `<CREATE CHAT THREAD CLIENT>` в файл **client.js**, обновите вкладку браузера и проверьте консоль, вы увидите:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Отправка сообщения в поток чата

Используйте метод `sendMessage` для отправки сообщения чата в созданный вами поток, идентифицируемый с помощью threadId.

`sendMessageRequest` описывает обязательные поля запроса сообщения чата.

- Используйте `content`, чтобы указать содержимое сообщения в чате.

`sendMessageOptions` описывает необязательные поля запроса сообщения чата.

- Используйте `priority`, чтобы указать уровень приоритета сообщения чата, например "нормальный" или "высокий". Это свойство можно использовать для отображения в приложении индикатора пользовательского интерфейса, чтобы привлечь внимание к сообщению или выполнить пользовательскую бизнес-логику.   
- Используйте `senderDisplayName`, чтобы указать отображаемое имя отправителя.

`sendChatMessageResult` ответа содержит идентификатор, который является уникальным идентификатором сообщения.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
Добавьте этот код вместо комментария `<SEND MESSAGE TO A CHAT THREAD>` в файл **client.js**, обновите вкладку браузера и проверьте консоль.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Получение сообщений из потока чата

С помощью оповещений в реальном времени можно подписываться на прослушивание новых входящих сообщений и обновлять текущие сообщения в памяти соответствующим образом. Службы коммуникации Azure поддерживают [список событий, на которые можно подписываться](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Добавьте этот код вместо комментария `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` в файл **client.js**.
Обновите вкладку браузера, и в консоли отобразится сообщение `Notification chatMessageReceived`.

Кроме того, можно получать сообщения чата, выполняя опрос метода `listMessages` через определенные интервалы. 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
Добавьте этот код вместо комментария `<LIST MESSAGES IN A CHAT THREAD>` в файл **client.js**.
Обновите вкладку. В консоли необходимо найти список сообщений, отправленных в этом потоке чата.


`listMessages` возвращает последнюю версию сообщения, включая все изменения или удаления, произошедшие с сообщением, с помощью `updateMessage` и `deleteMessage`.
Для удаленных сообщений `chatMessage.deletedOn` возвращает значение даты и времени, указывающее, когда это сообщение было удалено. Для измененных сообщений `chatMessage.editedOn` возвращает значение даты и времени, указывающее, когда это сообщение было изменено. Доступ к исходному времени создания сообщения можно получить с помощью `chatMessage.createdOn`, который можно использовать для упорядочения сообщений.

`listMessages` возвращает различные типы сообщений, которые могут быть идентифицированы с помощью `chatMessage.type`. Это следующие типы:

- `Text`: обычное сообщение чата, отправляемое участником потока.

- `ThreadActivity/TopicUpdate`: системное сообщение, указывающее на изменение темы.

- `ThreadActivity/AddMember`: системное сообщение о том, что один или несколько участников были добавлены в цепочку чата.

- `ThreadActivity/RemoveMember`: системное сообщение о том, что участник был удален из цепочки чата.

Дополнительные сведения см. в разделе о [типах сообщений](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Добавление пользователя в качестве участника в поток чата

После создания потока чата можно добавлять и удалять пользователей. Добавляя пользователей, вы предоставляете им доступ для отправки сообщений в поток чата, а также добавления или удаления других участников. Перед вызовом метода `addMembers` убедитесь, что вы получили новый маркер доступа и удостоверение для этого пользователя. Пользователю потребуется маркер доступа для инициализации своего клиента чата.

`addMembersRequest` описывает объект запроса, а в `members` перечислены элементы, добавляемые в поток чата.
- Свойство `user` (обязательное) — это пользователь, который должен быть добавлен в поток чата.
- Свойство `displayName` (необязательное) — это отображаемое имя для участника потока.
- Свойство `shareHistoryTime` (необязательное) — это время, в течение которого участнику предоставляется доступ к журналу чата. Чтобы предоставить общий доступ к журналу с момента запуска потока чата, установите для этого свойства любую дату, равную или меньше времени создания потока. Чтобы не использовать журнал до момента добавления участника, задайте для него текущую дату. Чтобы предоставить общий доступ к части журнала, задайте для него дату по своему усмотрению.

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
Замените **NEW_MEMBER_USER_ID** [новым идентификатором пользователя](../../access-tokens.md) и добавьте этот код вместо комментария `<ADD NEW MEMBER TO THREAD>` в файл **client.js**.

## <a name="list-users-in-a-chat-thread"></a>Вывод списка пользователей в потоке чата
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
Добавьте этот код вместо комментария `<LIST MEMBERS IN A THREAD>` в файл **client.js**, обновите вкладку браузера и проверьте консоль. Вы увидите сведения о пользователях чата:

## <a name="remove-user-from-a-chat-thread"></a>Удаление пользователя из потока чата

Подобно добавлению участника, можно удалить элементы из потока чата. Для удаления необходимо отслеживать идентификаторы добавленных участников.

Используйте метод `removeMember`, где `member` — это пользователь, удаляемый из потока.

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
Замените значение **MEMBER_ID** идентификатором пользователя, который использовался на предыдущем шаге (<NEW_MEMBER_USER_ID>).
Добавьте этот код вместо комментария `<REMOVE MEMBER FROM THREAD>` в файл **client.js**.
