---
title: Отправка и получение событий через Центры событий Azure с помощью Python (старая версия)
description: В этом пошаговом руководстве показано, как создать и выполнить скрипты Python, которые отправляют или получают события через Центры событий Azure с помощью старого пакета azure/event-hub версии 1.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 654ccd6352dc0b671cc3becdafd2f1e1102dd39e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902933"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Краткое руководство. Отправка и получение событий через Центры событий с использованием Python (azure-eventhub, версия 1)

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию от распределенных программных и аппаратных систем. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Дополнительные сведения о Центрах событий см. в статьях [Центры событий Azure — платформа потоковой передачи больших данных и служба приема событий](event-hubs-about.md) и [Features and terminology in Azure Event Hubs](event-hubs-features.md) (Функции и терминология в Центрах событий Azure).

В этом кратком руководстве показано, как создать приложения Python, которые отправляют или получают события через концентратор событий. 

> [!WARNING]
> Для этого краткого руководства используется версия 1 пакета SDK Центров событий Azure для Python. Рекомендуем [перенести](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) код в [версию 5 пакета SDK для Python](get-started-python-send-v2.md).

 
 

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством необходимо следующее:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- Активное пространство имен Центров событий и концентратор событий, созданные по инструкциям из статьи [Краткое руководство. Создание концентратора событий с помощью портала Azure](event-hubs-create.md). Запишите имена пространства имен и концентратора событий, которые пригодятся далее в этом пошаговом руководстве. 
- Имя ключа общего доступа и значение первичного ключа для пространства имен Центров событий. Получите имя и значение ключа доступа, следуя инструкциям в разделе [Get connection string from the portal](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) (Получение строки подключения на портале). По умолчанию для ключа доступа используется имя **RootManageSharedAccessKey**. Скопируйте имя ключа и значение первичного ключа, которые потребуются далее в этом пошаговом руководстве. 
- Python 3.4 или более поздней версии, для которого установлено и обновлено `pip`.
- Пакет Python для Центров событий. Чтобы установить этот пакет, выполните следующую команду в окне командной строки, где путь к Python сохранен в переменной окружения path. 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```


## <a name="send-events"></a>Отправка событий

Чтобы создать приложение Python, которое отправляет события в концентратор событий, выполните следующее.

> [!NOTE]
> Вы можете пропустить работу с кратким руководством и сразу скачать [примеры приложений](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) с сайта GitHub. Замените строки `EventHubConnectionString` и `EventHubName` значениями для реального концентратора событий.

1. Откройте редактор Python, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте новый файл с именем *send.py*. Этот сценарий отправляет 100 событий в ваш концентратор событий.
3. Вставьте следующий код в файл *send.py*, заменив актуальными значениями следующие параметры Центров событий: \<namespace> (пространство имен), \<eventhub> (концентратор событий), \<AccessKeyName> (имя ключа доступа) и \<primary key value> (значение первичного ключа). 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
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
               message = "Message {}".format(i)
               sender.send(EventData(message))
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
   
4. Сохраните файл. 

Чтобы запустить скрипт, перейдите в каталог с сохраненным файлом *send.py* и выполните следующую команду.

```cmd
start python send.py
```

Поздравляем! Теперь вы можете отправлять сообщения в концентратор событий.

## <a name="receive-events"></a>Получение событий

Чтобы создать приложение Python, которое получает события из концентратора событий, выполните следующее.

1. В редакторе Python создайте файл с именем *recv.py*.
2. Вставьте следующий код в *recv.py*, заменив актуальными значениями следующие параметры Центров событий: \<namespace> (пространство имен), \<eventhub> (концентратор событий), \<AccessKeyName> (имя ключа доступа) и \<primary key value> (значение первичного ключа). 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
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
   
4. Сохраните файл.

Чтобы запустить скрипт, перейдите в ту папку, где сохранен файл *recv.py*, и выполните следующую команду.

```cmd
start python recv.py
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о Центрах событий см. в следующих статьях:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Функции и терминология в Центрах событий Azure](event-hubs-features.md)
- [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)

