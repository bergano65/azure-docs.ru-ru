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
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523824"
---
## <a name="prerequisites"></a>Предварительные требования
Перед началом работы нужно сделать следующее:

- Создайте учетную запись Azure с активной подпиской. Дополнительные сведения см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- установите [Python](https://www.python.org/downloads/);
- Создайте ресурс Служб коммуникации Azure. Дополнительные сведения см. в статье [Краткое руководство по созданию ресурсов Служб коммуникации и управлению ими](../../create-communication-resource.md). Для работы с этим кратким руководством необходимо записать **конечную точку**.
- [Маркер доступа пользователя](../../access-tokens.md). Обязательно задайте для области значение chat и запишите строку маркера, а также строку userId.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-python-application"></a>Создание приложения Python

Откройте терминал или командное окно, создайте каталог для своего приложения и перейдите к нему.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Используйте текстовый редактор, чтобы создать файл с именем **start-chat.py** в корневом каталоге проекта и добавить структуру для программы, включая базовую обработку исключений. В следующих разделах показано, как добавить в этот файл весь исходный код для примера из этого краткого руководства.

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Установка клиентской библиотеки

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки чата Служб коммуникации для Python.

| Имя                                  | Описание                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Этот класс требуется для реализации всех функций чата. Его можно создать с помощью сведений о подписке и использовать для создания, получения и удаления потоков. |
| ChatThreadClient | Этот класс требуется для реализации всех функций потоков чата. Вы получаете экземпляр с помощью ChatClient и используете его для отправки, получения, обновления и удаления сообщений, добавления, удаления и получения пользователей, а также для отправки уведомлений о вводе и уведомления о прочтении. |

## <a name="create-a-chat-client"></a>Создание клиента чата

Чтобы создать клиент чата, вы будете использовать конечную точку службы коммуникации и `Access Token`, который был создан в рамках предварительных требований. Дополнительные сведения о маркерах доступа пользователей см. [здесь](../../access-tokens.md).

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Запуск потока чата

Для создания потока чата используйте метод `create_chat_thread`.

- Для указания раздела в этом чате используйте `topic`. Раздел можно обновить после создания потока чата с помощью функции `update_thread`.
- Используйте `members`, чтобы получить список `ChatThreadMember`, добавляемых в поток чата. `ChatThreadMember` принимает объект `CommunicationUser` типа `user`, который был получен после [создания пользователя](../../access-tokens.md#create-an-identity).

Ответ `chat_thread_client`, который используется для выполнения операций с созданным потоком чата для добавления участников в поток, отправки, удаления сообщения и т. д. Он содержит свойство `thread_id`, которое является уникальным идентификатором потока чата.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Получение клиента потока чата
Метод get_chat_thread_client возвращает клиент потока для потока, который уже существует. Его можно использовать для выполнения операций в созданном потоке: добавления участников, отправки сообщения и т. д. thread_id — уникальный идентификатор имеющегося потока чата.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Отправка сообщения в поток чата

Используйте метод `send_message` для отправки сообщения чата в созданный вами поток, идентифицируемый с помощью threadId.

- Используйте `content`, чтобы указать содержимое сообщения в чате.
- Используйте `priority`, чтобы указать уровень приоритета сообщения чата, например "нормальный" или "высокий". Это свойство можно использовать для отображения в приложении индикатора пользовательского интерфейса, чтобы привлечь внимание к сообщению или выполнить пользовательскую бизнес-логику.
- Используйте `senderDisplayName`, чтобы указать отображаемое имя отправителя.

`SendChatMessageResult` ответа содержит идентификатор, который является уникальным идентификатором сообщения.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Получение сообщений из потока чата

Кроме того, можно получать сообщения чата, выполняя опрос метода `list_messages` через определенные интервалы.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` возвращает последнюю версию сообщения, включая все изменения или удаления, произошедшие с сообщением, с помощью `update_message` и `delete_message`. Для удаленных сообщений `ChatMessage.deleted_on` возвращает значение даты и времени, указывающее, когда это сообщение было удалено. Для измененных сообщений `ChatMessage.edited_on` возвращает значение даты и времени, указывающее, когда это сообщение было изменено. Доступ к исходному времени создания сообщения можно получить с помощью `ChatMessage.created_on`, который можно использовать для упорядочения сообщений.

`list_messages` возвращает различные типы сообщений, которые могут быть идентифицированы с помощью `ChatMessage.type`. Это следующие типы:

- `Text`: обычное сообщение чата, отправляемое участником потока.

- `ThreadActivity/TopicUpdate`: системное сообщение, указывающее на изменение темы.

- `ThreadActivity/AddMember`: системное сообщение о том, что один или несколько участников были добавлены в цепочку чата.

- `ThreadActivity/DeleteMember`: системное сообщение о том, что участник был удален из цепочки чата.

Дополнительные сведения см. в разделе о [типах сообщений](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Добавление пользователя в качестве участника в поток чата

После создания потока чата можно добавлять и удалять пользователей. Добавляя пользователей, вы предоставляете им доступ для возможности отправки сообщений в поток, а также добавления или удаления других участников. Перед вызовом метода `add_members` убедитесь, что вы получили новый маркер доступа и удостоверение для этого пользователя. Пользователю потребуется маркер доступа для инициализации своего клиента чата.

Используйте метод `add_members`, чтобы добавить участников потока в определяемый threadId поток.

- Используйте `members`, чтобы получить список участников, добавляемых в поток чата.
- Свойство `user` (обязательное) — это значение `CommunicationUser`, полученное `CommunicationIdentityClient` при [создании пользователя](../../access-tokens.md#create-an-identity).
- Свойство `display_name` (необязательное) — это отображаемое имя для участника потока.
- Свойство `share_history_time` (необязательное) — это время, в течение которого участнику предоставляется доступ к журналу чата. Чтобы предоставить общий доступ к журналу с момента запуска потока чата, установите для этого свойства любую дату, равную или меньше времени создания потока. Чтобы не использовать журнал до момента добавления участника, задайте для него текущую дату. Чтобы предоставить общий доступ к части журнала, задайте для него промежуточную дату.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Удаление пользователя из потока чата

Подобно добавлению участника, можно удалить элементы из потока чата. Для удаления необходимо отслеживать идентификаторы добавленных участников.

Используйте метод `remove_member`, чтобы удалить участников потока из определяемого threadId потока.
- Свойство `user` — это пользователь CommunicationUser, удаляемый из потока.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>Выполнение кода

Запустите приложение из каталога приложения с помощью команды `python`.

```console
python start-chat.py
```
