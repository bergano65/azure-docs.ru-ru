---
title: Использование очередей служебной шины Azure с Python | Документация Майкрософт
description: Узнайте, как использовать очереди служебной шины в Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 622b1f6f6a852251c07c5576ed10cd76adbf5231
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795021"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Как использовать очереди служебной шины с Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

В этом руководстве вы узнаете, как создавать приложения Python, чтобы отправлять и получать сообщения из очереди служебной шины. 

## <a name="prerequisites"></a>Технические условия
1. Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать ваши [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Выполните шаги [с помощью портала Azure создать очередь служебной шины](service-bus-quickstart-portal.md) статьи.
    1. Чтение быстрого **Обзор** служебной шины **очереди**. 
    2. Чтобы создать служебную шину **пространства имен**. 
    3. Получить **строку подключения**. 

        > [!NOTE]
        > Вы создадите **очереди** в пространстве имен служебной шины с помощью Python в этом руководстве. 
1. Установить Python или [пакет служебной шины Azure Python][Python Azure Service Bus package], см. в разделе [руководство по установке Python](../python-how-to-install.md). Полную документацию пакета SDK Python для служебной шины см. в разделе [здесь](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Создание очереди
**ServiceBusClient** объект позволяет работать с очередями. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к служебной шине программным способом.

```python
from azure.servicebus import ServiceBusClient
```

Следующий код создает **ServiceBusClient** объекта. Замените `mynamespace`, `sharedaccesskeyname` и `sharedaccesskey` своим пространством имен, именем и значением ключа подписанного URL-адреса (SAS).

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Значения для имени и значение ключа SAS можно найти в данных подключения к [порталу Azure][Azure portal] или в области **Свойства** Visual Studio при выборе пространства имен служебной шины в обозревателе сервера (как показано в предыдущем разделе).

```python
sb_client.create_queue("taskqueue")
```

Метод `create_queue` также поддерживает дополнительные параметры, позволяющие переопределить настройки очереди по умолчанию, такие как срок жизни сообщения или максимальный размер очереди. В следующем примере показано, как установить максимальный размер очереди 5 ГБ и срок жизни в 1 минуту:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

sb_client.create_queue("taskqueue", queue_options)
```

Дополнительные сведения см. в разделе [документации Python шины службы Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
Чтобы отправить сообщение в очередь служебной шины, приложение вызывает `send` метод `ServiceBusClient` объекта.

В следующем примере показано, как отправить тестовое сообщение в очередь с именем `taskqueue` с помощью метода `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(Message("Message"))
```

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину][Service Bus quotas].

Дополнительные сведения см. в разделе [документации Python шины службы Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
Сообщения извлекаются из очереди с помощью `get_receiver` метод `ServiceBusService` объекта:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

## Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Дополнительные сведения см. в разделе [документации Python шины службы Azure](/python/api/overview/azure/servicebus?view=azure-python).


Прочитанные сообщения будут удаляться из очереди, если для параметра `peek_lock` задано значение **False**. Вы можете прочитать (извлечь) и заблокировать сообщение, не удаляя его из очереди, присвоив параметру `peek_lock` значение **True**.

Поведение по умолчанию — чтение и удаление сообщения как часть операции получения — является простейшей моделью, оптимальной для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

Если параметр `peek_lock` имеет значение **true**, получение становится операцией из двух этапов, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. После завершения обработки сообщения (или его надежного сохранения для будущей обработки) приложение завершает второй этап процесса получения, вызывая метод **delete** объекта **Message**. Метод **delete** помечает сообщение как использованное и удаляет его из очереди.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Как обрабатывать сбои приложения и нечитаемые сообщения
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод **unlock** объекта **Message**. После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина разблокирует сообщение автоматически и сделает его доступным для приема.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **delete**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто это называется **по крайней мере одну обработку**, то есть каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="next-steps"></a>Дальнейшие действия
Вы ознакомились с основными сведениями об очередях служебной шины. Дополнительные сведения см. в статье:

* [Очереди, разделы и подписки служебной шины][Queues, topics, and subscriptions].

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

