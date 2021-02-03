---
title: Краткое руководство о том, как присоединиться к собранию Teams
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 780ef2bbb7851d8bef5fc52a51421a7938043ecb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932306"
---
## <a name="join-the-meeting-chat"></a>Присоединение к чату собрания 

Включив взаимодействие с Teams, пользователь Служб коммуникации может присоединиться в качестве гостевого пользователя к вызову в Teams с помощью клиентской библиотеки для вызовов. При присоединении к вызову пользователь будет также добавлен в чат собрания как его участник, где сможет обмениваться сообщениями с другими участниками вызова. Пользователь не будет видеть сообщения в чате, отправленные до его присоединения к вызову. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Получение данных о беседе в чате для пользователя Служб коммуникации

Сначала создайте экземпляр `ChatThreadClient` для беседы в чате собрания. Проанализируйте ссылку на собрание или используйте API Graph с идентификатором собрания, чтобы получить идентификатор беседы. 

- Ссылка на собрание в Teams выглядит следующим образом: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. Идентификатор беседы будет указан вместо строки `meeting_chat_thread_id` в этой ссылке. 
- Если у вас есть идентификатор собрания, для получения идентификатора беседы можно использовать [API Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). В ответе [GET API](/graph/api/onlinemeeting-get?tabs=http%22+%5c&view=graph-rest-beta) будет указан объект `chatInfo`, содержащий `threadID`. 

Получив идентификатор беседы в чате, вы можете получить данные о клиенте беседы в чате с помощью клиентской библиотеки чата JavaScript: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
`chatThreadClient` можно использовать для перечисления участников беседы в чате, получения журнала чата и отправки сообщений.  

## <a name="send-and-receive-messages"></a>Отправка и получение сообщений  

Для отправки сообщения в чат собрания используйте `SendMessage`. Возможность подписываться на такие события, как `chatMessageReceived`, для получения входящих сообщений не поддерживается, потому что для этого сценария еще не включена отправка сигналов в реальном времени. Чтобы получить последние сообщения, можно опросить API `ListMessages`. Для сценария взаимодействия API `ListMessages` теперь поддерживает возврат трех новых типов сообщений:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

Дополнительные сведения о типах сообщений см. [здесь](../../../concepts/chat/concepts.md). 

**Примечание.** В настоящее время для сценариев взаимодействия с Teams поддерживается только отправка и получение сообщений. Другие функции, такие как индикаторы ввода и возможность пользователей Служб коммуникации добавлять и удалять других пользователей в собрании в Teams, пока не поддерживаются.  

