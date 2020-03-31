---
title: Как использовать хранилище azure Очередь v2.1 от Python - Azure Storage
description: Узнайте, как использовать службу Azure Очередь v2.1 от Python для создания и удаления очередей, а также ввода, получения и удаления сообщений.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: ca0831fd7554058d21e315b67d6965579af1d38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060920"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Как использовать хранилище azure Очередь v2.1 от Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

В этой статье показаны распространенные сценарии с использованием службы хранения Azure Queue. Сценарии включают вставки, заглядывание, получение и удаляние сообщений очереди, а также создание и удаляние очередей.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Обзор

Образцы в этой статье написаны в Python и используют [SDK хранилища Microsoft Azure для Python.] Дополнительные сведения об очередях см. в разделе [Дальнейшие действия](#next-steps).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Скачать и установить SDK хранения Azure для Python

[SDK-хранилище Azure для Python](https://github.com/azure/azure-storage-python) требует версии Python 2.7, 3.3 или позже.
 
### <a name="install-via-pypi"></a>Установка с помощью PyPI

Для установки с помощью индекса пакетов Python (PyPI) введите:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Если вы обновляете пакет SDK службы хранилища Azure для Python версии 0.36 или более ранней, удалите старый пакет SDK с помощью `pip uninstall azure-storage`, прежде чем устанавливать последнюю версию пакета.

Другие методы установки см. в статье [Пакет SDK службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Просмотр примера приложения

Для просмотра и запуска примера приложения, которое показывает, [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started)как использовать Python с очередью Azure, см. 

Чтобы запустить пример приложения, убедитесь, что установили оба пакета: `azure-storage-queue` и `azure-storage-common`.

## <a name="create-a-queue"></a>Создание очереди

Объект [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) позволяет работать с очередями. Следующий код `QueueService` создает объект. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

```python
from azure.storage.queue import QueueService
```

Следующий код создает объект `QueueService`, используя имя и ключ учетной записи хранения. Замените *myaccount* и *mykey* с вашим именем учетной записи и ключом.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Вставка сообщения в очередь

Чтобы вставить сообщение в очередь, используйте метод [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) для создания нового сообщения и добавления его в очередь.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Сообщения очереди Azure хранятся в виде текста. Если вы хотите хранить двоичные данные, настройка функций кодирования и декодирования Base64 на объекте обслуживания очереди, прежде чем поместить сообщение в очередь.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Просмотр следующего сообщения

Вы можете заглянуть в сообщение перед очередью, не удаляя его из очереди, позвонив [в peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) метод. По умолчанию, `peek_messages` заглядывает в одно сообщение.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Вывод сообщений из очереди

Ваш код удаляет сообщение из очереди в два этапа. Когда вы звоните [get_messages,](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)вы получаете следующее сообщение в очереди по умолчанию. Сообщение, возвращаемое методом `get_messages`, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы закончить удаление сообщения из очереди, необходимо также позвонить [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Ваш код `delete_message` вызывает сявт сразу после обработки сообщения.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. Следующий пример кода `get_messages` использует метод для получения 16 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Приведенный ниже код использует метод [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) для обновления сообщения. Время ожидания видимости равно 0. Это означает, что сообщение появится немедленно, и содержимое обновится.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) просит службу очереди вернуть метаданные `approximate_message_count`о очереди и В результате число указывается лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Удаление очереди

Чтобы удалить очередь и все содержащиеся в ней сообщения, позвоните в [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) метод.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы изучили основы хранения очередей, следуйте этим ссылкам, чтобы узнать больше.

* [Ссылка на API API Очередей Azure](/python/api/azure-storage-queue)
* [Центр по разработке для Python](https://azure.microsoft.com/develop/python/)
* [REST API служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[пакет SDK для службы хранилища Microsoft Azure для Python]: https://github.com/Azure/azure-storage-python
