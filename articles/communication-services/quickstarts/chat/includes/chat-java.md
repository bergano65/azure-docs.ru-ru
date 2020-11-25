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
ms.openlocfilehash: edf48bc75817b3510264d852eb9cc717ed022f33
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915315"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- [комплект SDK для Java (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) версии 8 или более поздней версии.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Развернутый ресурс Служб коммуникации и строка подключения. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- [Маркер доступа пользователя](../../access-tokens.md). Обязательно задайте для области значение chat и запишите строку маркера, а также строку userId.


## <a name="setting-up"></a>Настройка

### <a name="create-a-new-java-application"></a>Создание нового приложения Java

Откройте терминал или командное окно и перейдите в каталог, в котором необходимо создать приложение Java. Выполните приведенную ниже команду, чтобы создать проект Java из шаблона maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Вы заметите, что цель generate создала каталог с тем же именем, что и у artifactId. В этом каталоге `src/main/java directory` содержит исходный код проекта, каталог `src/test/java` содержит источник теста, а файл pom.xml является объектной моделью проекта или [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Обновите файл POM приложения, чтобы использовать Java 8 или более поздней версии:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-client-library"></a>Добавление ссылок на пакет для клиентской библиотеки чата

В файле POM добавьте ссылку на пакет `azure-communication-chat` с помощью API-интерфейсов для чатов:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>
```

Для проверки подлинности клиент должен добавить ссылку на пакет `azure-communication-common`:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>

```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки чата Служб коммуникации для Java.

| Имя                                  | Описание                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Этот класс требуется для реализации всех функций чата. Его можно создать с помощью сведений о подписке и использовать для создания, получения и удаления потоков. |
| ChatAsyncClient | Этот класс требуется для реализации всех асинхронных функций чата. Его можно создать с помощью сведений о подписке и использовать для создания, получения и удаления потоков. |
| ChatThreadClient | Этот класс требуется для реализации всех функций потоков чата. Вы получаете экземпляр с помощью ChatClient и используете его для отправки, получения, обновления и удаления сообщений, добавления, удаления и получения пользователей, а также для отправки уведомлений о вводе и уведомления о прочтении. |
| ChatThreadAsyncClient | Этот класс требуется для реализации всех асинхронных функций потоков чата. Вы получаете экземпляр с помощью ChatAsyncClient и используете его для отправки/получения/обновления/удаления сообщений, добавления/удаления/получения пользователей и отправки уведомлений о вводе и уведомления о прочтении. |

## <a name="create-a-chat-client"></a>Создание клиента чата
Чтобы создать клиент чата, вы будете использовать конечную точку Служб коммуникации и маркер доступа, который был создан на шаге предварительных требований. Маркеры доступа пользователей позволяют создавать клиентские приложения, которые напрямую проходят проверку подлинности в Службах коммуникации Azure. После создания этих маркеров на сервере передайте их обратно клиентскому устройству. Для передачи маркера клиенту чата необходимо использовать класс CommunicationUserCredential из общей клиентской библиотеки. 

При добавлении операторов импорта обязательно добавляйте только импорты из пространств имен com.azure.communication.chat и com.azure.communication.chat.models, а не из com.azure.communication.chat.implementation. В файле App.java, созданном с помощью Maven, можно использовать следующий код:

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>Запуск потока чата

Для создания потока чата используйте метод `createChatThread`.
`createChatThreadOptions` используется для описания запроса потока.

- Для указания раздела в этом чате используйте `topic`. Раздел можно обновить после создания потока чата с помощью функции `UpdateThread`.
- Используйте `members`, чтобы получить список участников потока, добавляемых в поток. `ChatThreadMember` принимает пользователя, которого вы создали при изучении краткого руководства о [маркере доступа пользователя](../../access-tokens.md).

Ответ `chatThreadClient`, который используется для выполнения операций с созданным потоком чата для добавления участников в поток, отправки, удаления сообщения и т. д. Он содержит свойство `chatThreadId`, которое является уникальным идентификатором потока чата. Свойство доступно для общедоступного метода .getChatThreadId().

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>Отправка сообщения в поток чата

Используйте метод `sendMessage` для отправки сообщения в созданный вами поток, идентифицируемый с помощью chatThreadId.
`sendChatMessageOptions` используется для описания запроса сообщения чата.

- Используйте `content`, чтобы указать содержимое сообщения в чате.
- Используйте `priority`, чтобы указать уровень приоритета сообщения чата, например "нормальный" или "высокий". Это свойство можно использовать для отображения в приложении индикатора пользовательского интерфейса, чтобы привлечь внимание к сообщению или выполнить пользовательскую бизнес-логику.
- Используйте `senderDisplayName`, чтобы указать отображаемое имя отправителя.

`sendChatMessageResult` ответа содержит `id`, который является уникальным идентификатором сообщения.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>Получение клиента потока чата

Метод `getChatThreadClient` возвращает клиент потока для имеющегося потока. Его можно использовать для выполнения операций в созданном потоке: добавления участников, отправки сообщения и т. д. `chatThreadId` — уникальный идентификатор имеющегося потока чата.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Получение сообщений из потока чата

Вы можете получать сообщения чата, выполняя опрос метода `listMessages` на клиенте потока чата через определенные интервалы.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` возвращает последнюю версию сообщения, включая все изменения или удаления, произошедшие с сообщением, с помощью .editMessage() и .deleteMessage(). Для удаленных сообщений `chatMessage.getDeletedOn()` возвращает значение даты и времени, указывающее, когда это сообщение было удалено. Для измененных сообщений `chatMessage.getEditedOn()` возвращает значение даты и времени, указывающее, когда это сообщение было изменено. Доступ к исходному времени создания сообщения можно получить с помощью `chatMessage.getCreatedOn()`, который также можно использовать для упорядочения сообщений.

`listMessages` возвращает различные типы сообщений, которые могут быть идентифицированы с помощью `chatMessage.getType()`. Это следующие типы:

- `Text`: обычное сообщение чата, отправляемое участником потока.

- `ThreadActivity/TopicUpdate`: системное сообщение, указывающее на изменение темы.

- `ThreadActivity/AddMember`: системное сообщение о том, что один или несколько участников были добавлены в цепочку чата.

- `ThreadActivity/DeleteMember`: системное сообщение о том, что участник был удален из цепочки чата.

Дополнительные сведения см. в разделе о [типах сообщений](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Добавление пользователя в качестве участника в поток чата

После создания потока чата можно добавлять и удалять пользователей. Добавляя пользователей, вы предоставляете им доступ для отправки сообщений в поток чата, а также добавления или удаления других участников. Необходимо начать с получения нового маркера доступа и удостоверения для этого пользователя. Перед вызовом метода addMembers убедитесь, что вы получили новый маркер доступа и удостоверение для этого пользователя. Пользователю потребуется маркер доступа для инициализации своего клиента чата.

Используйте метод `addMembers`, чтобы добавить участников потока в определяемый threadId поток.

- Используйте `members`, чтобы получить список участников, добавляемых в поток чата.
- `user` (обязательно) — это CommunicationUser, созданный вами с помощью CommunicationIdentityClient при изучении краткого руководства о [маркере доступа пользователя](../../access-tokens.md).
- Свойство `display_name` (необязательное) — это отображаемое имя для участника потока.
- Свойство `share_history_time` (необязательное) — это время, в течение которого участнику предоставляется доступ к журналу чата. Чтобы предоставить общий доступ к журналу с момента запуска потока чата, установите для этого свойства любую дату, равную или меньше времени создания потока. Чтобы не использовать журнал до момента добавления участника, задайте для него текущую дату. Чтобы предоставить общий доступ к части журнала, задайте для него требуемую дату.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>Удаление пользователя из потока чата

Подобно добавлению пользователя в поток, можно удалить его из потока чата. Для этого необходимо отслеживание удостоверений пользователей добавленных участников.

Используйте `removeMember`, где `user` — это созданный вами CommunicationUser.

```Java
chatThreadClient.removeMember(user);
```

## <a name="run-the-code"></a>Выполнение кода

Перейдите в каталог, содержащий файл *pom.xml*, и скомпилируйте проект с помощью следующей команды `mvn`.

```console
mvn compile
```

Затем выполните сборку пакета.

```console
mvn package
```

Выполните следующую команду `mvn` для запуска приложения.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```