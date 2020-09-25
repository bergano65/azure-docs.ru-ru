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
ms.openlocfilehash: f32b81184b9a96760e43d0cd64c27459952c5953
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945641"
---
## <a name="prerequisites"></a>Предварительные требования
Перед началом работы нужно сделать следующее:
- Создайте учетную запись Azure с активной подпиской. Дополнительные сведения см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- установить [Visual Studio](https://visualstudio.microsoft.com/downloads/); 
- Создайте ресурс Служб коммуникации Azure. Дополнительные сведения см. в статье [Краткое руководство по созданию ресурсов Служб коммуникации и управлению ими](../../create-communication-resource.md). Для работы с этим кратким руководством необходимо записать **конечную точку**.
- [Маркер доступа пользователя](../../access-tokens.md). Обязательно задайте для области значение chat и запишите строку маркера, а также строку userId.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `ChatQuickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Измените каталог на только что созданную папку приложения и выполните команду `dotnet build`, чтобы скомпилировать приложение.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Установка пакета

Установите клиентскую библиотеку чата Служб коммуникации Azure для .NET.

```PowerShell
dotnet add package Azure.Communication.Chat
``` 

## <a name="object-model"></a>Объектная модель

Указанные ниже классы реализуют некоторые основные функции клиентской библиотеки чата Служб коммуникации для C#.

| Имя                                  | Описание                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Этот класс требуется для реализации всех функций чата. Его можно создать с помощью сведений о подписке и использовать для создания, получения и удаления потоков. |
| ChatThreadClient | Этот класс требуется для реализации всех функций потоков чата. Вы получаете экземпляр с помощью ChatClient и используете его для отправки, получения, обновления и удаления сообщений, добавления, удаления и получения пользователей, а также для отправки уведомлений о вводе и уведомления о прочтении. |

## <a name="create-a-chat-client"></a>Создание клиента чата

Чтобы создать клиент чата, вы будете использовать конечную точку Службы коммуникации и маркер доступа, который был создан в рамках предварительных требований. Чтобы создать пользователя и выдать маркер для передачи клиенту чата, необходимо использовать класс `CommunicationIdentityClient` из клиентской библиотеки `Administration`. Дополнительные сведения о маркерах доступа пользователей см. [здесь](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Запуск потока чата

Для создания потока чата используйте метод `createChatThread`.
- Для указания раздела в этом чате используйте `topic`. Раздел можно обновить после создания потока чата с помощью функции `UpdateThread`.
- Используйте свойство `members` для передачи списка объектов `ChatThreadMember`, добавляемых в поток чата. Объект `ChatThreadMember` инициализирован с помощью объекта `CommunicationUser`. Чтобы получить объект `CommunicationUser`, необходимо передать идентификатор доступа, созданный с помощью инструкции для [создания пользователя](../../access-tokens.md#create-a-user).

Ответ `chatThreadClient`, который используется для выполнения операций с созданным потоком чата для добавления участников в поток, отправки, удаления сообщения и т. д. Он содержит атрибут `Id`, который является уникальным идентификатором потока чата. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Получение клиента потока чата
Метод `GetChatThreadClient` возвращает клиент потока для имеющегося потока. Его можно использовать для выполнения операций в созданном потоке: добавления участников, отправки сообщения и т. д. thread_id — уникальный идентификатор имеющегося потока чата.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = await chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Отправка сообщения в поток чата

Используйте метод `SendMessage` для отправки сообщения в поток, идентифицируемый с помощью threadId.

- Используйте `content`, чтобы указать содержимое сообщения в чате (обязательно).
- Используйте `priority`, чтобы указать уровень приоритета сообщения, например "обычный" или "высокий". Если он не указан, будет использоваться "обычный".
- Используйте `senderDisplayName`, чтобы указать отображаемое имя отправителя. Если не указано, будет использоваться пустое имя.

`SendChatMessageResult` — ответ, возвращенный при отправке сообщения. Содержит идентификатор, который является уникальным идентификатором сообщения.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Получение сообщений из потока чата

Вы можете получать сообщения чата, выполняя опрос метода `GetMessages` на клиенте потока чата через определенные интервалы.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` принимает необязательный параметр `DateTimeOffset`. Если указано это смещение, то вы будете получать сообщения, которые были получены, обновлены или удалены после него. Обратите внимание, что сообщения, полученные до смещения времени, но исправлены или удалены после него, также будут возвращены.

`GetMessages` возвращает последнюю версию сообщения, включая все изменения или удаления, произошедшие с сообщением, с помощью `UpdateMessage` и `DeleteMessage`. Для удаленных сообщений `chatMessage.DeletedOn` возвращает значение даты и времени, указывающее, когда это сообщение было удалено. Для измененных сообщений `chatMessage.EditedOn` возвращает значение даты и времени, указывающее, когда это сообщение было изменено. Доступ к исходному времени создания сообщения можно получить с помощью `chatMessage.CreatedOn`, который также можно использовать для упорядочения сообщений.

`GetMessages` возвращает различные типы сообщений, которые могут быть идентифицированы с помощью `chatMessage.Type`. Это следующие типы:

- `Text`: обычное сообщение чата, отправляемое участником потока.

- `ThreadActivity/TopicUpdate`: системное сообщение, указывающее на изменение темы.

- `ThreadActivity/AddMember`: системное сообщение о том, что один или несколько участников были добавлены в цепочку чата.

- `ThreadActivity/DeleteMember`: системное сообщение о том, что участник был удален из цепочки чата.

Дополнительные сведения см. в разделе о [типах сообщений](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Обновление сообщения

Вы можете обновить сообщение, которое уже было отправлено, вызвав `UpdateMessage` для `ChatThreadClient`.

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Удаление сообщения

Вы можете удалить сообщение, вызвав `DeleteMessage` для `ChatThreadClient`.

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Добавление пользователя в качестве участника в поток чата

После создания потока можно добавлять и удалять пользователей. Добавляя пользователей, вы предоставляете им доступ для возможности отправки сообщений в поток, а также добавления или удаления других участников. Перед вызовом `AddMembers` убедитесь, что вы получили новый маркер доступа и удостоверение для этого пользователя. Пользователю потребуется маркер доступа для инициализации своего клиента чата.

Используйте метод `AddMembers`, чтобы добавить участников потока в определяемый threadId поток.

 - Используйте `members`, чтобы получить список участников, добавляемых в поток чата.
 - `User` (обязательно) — это идентификатор, который вы получаете для этого нового пользователя.
 - Свойство `DisplayName` (необязательное) — это отображаемое имя для участника потока.
 - Свойство `ShareHistoryTime` (необязательное) — это время, в течение которого участнику предоставляется доступ к журналу чата. Чтобы предоставить общий доступ к журналу с начала потока чата, задайте для него значение DateTime.MinValue. Чтобы не использовать журнал до момента добавления участника, задайте для него текущее время. Чтобы предоставить общий доступ к частичному журналу, задайте для него точку во времени между созданием потока и текущим временем.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Удаление пользователя из потока чата

Подобно добавлению пользователя в поток, можно удалить его из потока чата. Для этого необходимо отслеживание удостоверения (CommunicationUser) добавленных участников.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>Выполнение кода

Запустите приложение из каталога приложения с помощью команды `dotnet run`.

```console
dotnet run
```
