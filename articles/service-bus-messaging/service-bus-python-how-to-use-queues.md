---
title: Учебник. Использование очередей служебной шины Azure с Python
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
ms.custom: seo-python-october2019
ms.openlocfilehash: 3561d7e3569b31698fbbc5f8cf2518d9fe1fc398
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331130"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Как использовать очереди служебной шины с Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

В этом руководстве вы узнаете, как создавать приложения Python для отправки сообщений в очередь служебной шины и получать сообщения из нее. 

## <a name="prerequisites"></a>Технические условия
1. Подписка Azure. Для работы с этим учебником требуется учетная запись Azure. Вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) или зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Выполните действия, описанные в разделе [использование портал Azure для создания очереди служебной шины](service-bus-quickstart-portal.md) .
    1. Ознакомьтесь с кратким **обзором** **очередей**служебной шины. 
    2. Создайте **пространство имен**служебной шины. 
    3. Возвращает **строку подключения**. 

        > [!NOTE]
        > Вы создадите **очередь** в пространстве имен служебной шины с помощью Python в этом руководстве. 
1. Установка Python или [пакета служебной шины Azure для Python][Python Azure Service Bus package]см. в разделе [руководства по установке Python](/azure/python/python-sdk-azure-install). Полную документацию по пакету SDK для Python служебной шины см. [здесь](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Создание очереди
Объект **сервицебусклиент** позволяет работать с очередями. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к служебной шине программным способом.

```python
from azure.servicebus import ServiceBusClient
```

Следующий код создает объект **сервицебусклиент** . Замените `<CONNECTION STRING>` на servicebus ConnectionString.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Значения для имени и значения ключа SAS можно найти в [портал Azure][Azure portal] сведения о подключении или в области **свойств** Visual Studio при выборе пространства имен служебной шины в обозреватель сервера (как показано в предыдущем разделе).

```python
sb_client.create_queue("taskqueue")
```

Метод `create_queue` также поддерживает дополнительные параметры, позволяющие переопределить настройки очереди по умолчанию, такие как срок жизни сообщения или максимальный размер очереди. В следующем примере показано, как установить максимальный размер очереди 5 ГБ и срок жизни в 1 минуту:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Дополнительные сведения см. в [документации по Python служебной шины Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
Чтобы отправить сообщение в очередь служебной шины, приложение вызывает метод `send` для объекта `ServiceBusClient`.

В следующем примере показано, как отправить тестовое сообщение в очередь с именем `taskqueue` с помощью метода `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину][Service Bus quotas].

Дополнительные сведения см. в [документации по Python служебной шины Azure](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
Сообщения получаются из очереди с помощью метода `get_receiver` для объекта `ServiceBusService`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Дополнительные сведения см. в [документации по Python служебной шины Azure](/python/api/overview/azure/servicebus?view=azure-python).


Прочитанные сообщения будут удаляться из очереди, если для параметра `peek_lock` задано значение **False**. Вы можете прочитать (извлечь) и заблокировать сообщение, не удаляя его из очереди, присвоив параметру `peek_lock` значение **True**.

Поведение по умолчанию — чтение и удаление сообщения как часть операции получения — является простейшей моделью, оптимальной для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

Если параметр `peek_lock` имеет значение **true**, получение становится операцией из двух этапов, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. После завершения обработки сообщения (или его надежного сохранения для будущей обработки) приложение завершает второй этап процесса получения, вызывая метод **delete** объекта **Message**. Метод **delete** помечает сообщение как использованное и удаляет его из очереди.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Как обрабатывать сбои приложения и нечитаемые сообщения
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод **unlock** объекта **Message**. После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина разблокирует сообщение автоматически и сделает его доступным для приема.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **delete**, сообщение будет повторно доставлено в приложение после его перезапуска. Это часто вызывается **по крайней мере один раз при обработке**, то есть каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях одно и то же сообщение может быть доставлено снова. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

> [!NOTE]
> Вы можете управлять ресурсами служебной шины с помощью [обозревателя служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer/). Обозреватель служебной шины позволяет без труда подключаться к пространству имен служебной шины и управлять сущностями обмена сообщениями. Средство предоставляет дополнительные возможности, например функции импорта и экспорта или возможность проверять разделы, очереди, подписки, службы ретрансляции, центры уведомлений и концентраторы событий. 

## <a name="next-steps"></a>Дальнейшие действия
Вы ознакомились с основными сведениями об очередях служебной шины. Дополнительные сведения см. в статье:

* [Очереди, разделы и подписки][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

