---
title: Отправлять или получать события с помощью Python - концентраторов событий Azure | Документация Майкрософт
description: Статья содержит пошаговое руководство по созданию приложения Python, которое отправляет события в концентраторы событий Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 6a8f0ddcfe6de904219059c6e761ead4c004732d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461456"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Отправлять события или получения событий из концентраторов событий с помощью Python

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

Это руководство содержит инструкции для создания приложений Python для отправки событий или получать события из концентратора событий. 

> [!NOTE]
> Вы можете скачать это краткое руководство в качестве примера с сайта [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), заменить строки `EventHubConnectionString` и `EventHubName` значениями для своего концентратора событий и выполнить этот пример. Или следуйте инструкциям из этого руководства, чтобы создать собственное решение.

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- Python 3.4 или более поздней версии.
- Используйте [портала Azure](https://portal.azure.com) создать пространство имен типа концентраторов событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните инструкции из [этой статьи](event-hubs-create.md). Затем получите значение ключа доступа для концентратора событий, следуя указаниям из статьи: [Получение строки подключения на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Используйте ключ доступа в коде, который вы напишете далее в рамках этого руководства. Имя ключа по умолчанию: **RootManageSharedAccessKey**.

## <a name="install-python-package"></a>Установка пакета Python

Чтобы установить пакет Python для Центров событий, откройте окно командной строки с Python в пути, а затем выполните следующую команду. 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>Отправка событий

### <a name="create-a-python-script-to-send-events"></a>Создание сценария Python для отправки событий

Создайте приложение Python, отправляющее события в концентратор событий:

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте сценарий с именем **send.py**. Этот сценарий отправляет 100 событий в ваш концентратор событий.
3. Вставьте следующий код в send.py, заменив значения ADDRESS, USER и KEY значениями, полученными на портале Azure в предыдущем разделе: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

try:
    if not ADDRESS:
        raise ValueError("No EventHubs URL supplied.")

    # Create Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    sender = client.add_sender(partition="0")
    client.run()
    try:
        start_time = time.time()
        for i in range(100):
            print("Sending message: {}".format(i))
            sender.send(EventData(str(i)))
    except:
        raise
    finally:
        end_time = time.time()
        client.stop()
        run_time = end_time - start_time
        logger.info("Runtime: {} seconds".format(run_time))

except KeyboardInterrupt:
    pass
```

### <a name="run-application-to-send-events"></a>Запуск приложения для отправки событий

Чтобы выполнить сценарий, откройте окно командной строки с Python в пути, а затем выполните следующую команду.

```bash
start python send.py
```

Поздравляем! Теперь вы можете отправлять сообщения в концентратор событий.

## <a name="receive-events"></a>Получение событий

### <a name="create-a-python-script-to-receive-events"></a>Создание сценария Python для получения событий

Создайте приложение Python, получающее события из концентратора событий:

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте сценарий с именем **recv.py**.
3. Вставьте следующий код в recv.py, заменив значения ADDRESS, USER и KEY значениями, полученными на портале Azure в предыдущем разделе: 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
        total += 1

    end_time = time.time()
    client.stop()
    run_time = end_time - start_time
    print("Received {} messages in {} seconds".format(total, run_time))

except KeyboardInterrupt:
    pass
finally:
    client.stop()
```

### <a name="receive-events"></a>Получение событий

Чтобы выполнить сценарий, откройте окно командной строки с Python в пути, а затем выполните следующую команду.

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Возможностями и терминологией в концентраторах событий Azure](event-hubs-features.md)
- [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)

