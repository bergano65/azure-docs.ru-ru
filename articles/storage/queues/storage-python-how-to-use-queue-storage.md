---
title: How to use Azure Queue storage v2.1 from Python - Azure Storage
description: Learn how to use the Azure Queue service v2.1 from Python to create and delete queues, and insert, get, and delete messages.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 09/17/2019
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: b5382a6a1ea381d57a026e9d42190152e38f7696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209525"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>How to use Azure Queue storage v2.1 from Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

This article demonstrates common scenarios using the Azure Queue storage service. The scenarios covered include inserting, peeking, getting, and deleting queue messages, and creating and deleting queues.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Краткое описание

The samples in this article are written in Python and use the [Пакет SDK для службы хранилища Microsoft Azure для Python]. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия](#next-steps).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Download and install Azure Storage SDK for Python

The [Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) requires Python version 2.7, 3.3, or later.
 
### <a name="install-via-pypi"></a>Установка с помощью PyPI

Для установки с помощью индекса пакетов Python (PyPI) введите:

```bash
pip install azure-storage-blob==2.1.0
```

> [!NOTE]
> Если вы обновляете пакет SDK службы хранилища Azure для Python версии 0.36 или более ранней, удалите старый пакет SDK с помощью `pip uninstall azure-storage`, прежде чем устанавливать последнюю версию пакета.

Другие методы установки см. в статье [Пакет SDK службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Просмотр примера приложения

To view and run a sample application that shows how to use Python with Azure Queues, see [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Чтобы запустить пример приложения, убедитесь, что установили оба пакета `azure-storage-queue` и `azure-storage-common`.

## <a name="create-a-queue"></a>Создание очереди

Объект [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) позволяет работать с очередями. The following code creates a `QueueService` object. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

```python
from azure.storage.queue import QueueService
```

Следующий код создает объект `QueueService`, используя имя и ключ учетной записи хранения. Replace *myaccount* and *mykey* with your account name and key.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Вставка сообщения в очередь

To insert a message into a queue, use the [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) method to create a new message and add it to the queue.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure queue messages are stored as text. If you want to store binary data, setup Base64 encoding and decoding functions on the queue service object before putting a message in the queue.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Просмотр следующего сообщения

You can peek at the message in the front of a queue without removing it from the queue by calling the [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) method. By default, `peek_messages` peeks at a single message.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Вывод сообщений из очереди

Ваш код удаляет сообщение из очереди в два этапа. When you call [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), you get the next message in a queue by default. Сообщение, возвращаемое методом `get_messages`, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Your code calls `delete_message` right after the message has been processed.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. The following code example uses the `get_messages` method to get 16 messages in one call. Затем он обрабатывает каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. The code below uses the [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) method to update a message. Время ожидания видимости равно 0. Это означает, что сообщение появится немедленно, и содержимое обновится.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. The [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) method asks the queue service to return metadata about the queue, and the `approximate_message_count`. В результате число указывается лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Удаление очереди

To delete a queue and all the messages contained in it, call the [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) method.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Дальнейшие действия

Now that you've learned the basics of queue storage, follow these links to learn more.

* [Azure Queues Python API reference](/python/api/azure-storage-queue)
* [Центр по разработке для Python](https://azure.microsoft.com/develop/python/)
* [API-интерфейс REST служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Пакет SDK для службы хранилища Microsoft Azure для Python]: https://github.com/Azure/azure-storage-python
