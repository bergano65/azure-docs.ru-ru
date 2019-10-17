---
title: Отправка и получение событий с помощью Python — концентраторы событий Azure
description: В этом пошаговом руководстве показано, как создать и выполнить скрипты Python, которые отправляют события или получают события из концентраторов событий Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/11/2019
ms.author: shvija
ms.openlocfilehash: 330a7f5dc325c707b5be7ce9f9b3242a1d4c9547
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428898"
---
# <a name="send-and-receive-events-with-event-hubs-using-python"></a>Отправка и получение событий с помощью концентраторов событий с использованием Python

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Концентраторы событий могут обрабатывать и хранить события, данные или телеметрию из распределенного программного обеспечения и устройств. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в режиме реального времени, а также с помощью адаптеров пакетной обработки или хранения. Дополнительные сведения о концентраторах событий см. в статьях [концентраторы событий Azure](event-hubs-about.md) и [компоненты и терминология в концентраторах событий Azure](event-hubs-features.md).

В этом кратком руководстве показано, как создавать приложения Python, которые отправляют события и получают события из концентратора событий. 

> [!NOTE]
> Вместо работы с кратким руководством вы можете скачать и запустить [примеры приложений](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) с сайта GitHub. Замените строки `EventHubConnectionString` и `EventHubName` на значения концентратора событий. 

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством необходимо следующее:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- Активное пространство имен концентраторов событий и концентратор событий, созданное с помощью инструкций в разделе [Краткое руководство по созданию концентратора событий с использованием портал Azure](event-hubs-create.md). Запишите имена пространства имен и концентраторов событий, которые должны использоваться далее в этом пошаговом руководстве. 
- Имя общего ключа доступа и значение первичного ключа для пространства имен концентраторов событий. Получите имя и значение ключа доступа, следуя инструкциям в разделе [Получение строки подключения](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Имя ключа доступа по умолчанию — **RootManageSharedAccessKey**. Скопируйте имя ключа и значение первичного ключа для дальнейшего использования в этом пошаговом руководстве. 
- Python 3,4 или более поздней версии с установленным и обновленным `pip`.
- Пакет Python для концентраторов событий. Чтобы установить пакет, выполните следующую команду в командной строке, которая содержит Python в пути: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Код в этом кратком руководстве использует текущую стабильную версию 1.3.1 пакета SDK для концентраторов событий. Пример кода, в котором используется предварительная версия пакета SDK, см. в разделе [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Отправка событий

Чтобы создать приложение Python, которое отправляет события в концентратор событий:

1. Откройте предпочтительный редактор Python, например [Visual Studio Code](https://code.visualstudio.com/)
2. Создайте новый файл с именем *Send.py*. Этот сценарий отправляет 100 событий в ваш концентратор событий.
3. Вставьте следующий код в *Send.py*, заменив концентраторы событий \<namespace >, \<eventhub >, \<AccessKeyName > и \<primary значение ключа > значениями. 
   
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

Чтобы запустить скрипт из каталога, в котором вы сохранили *Send.py*, выполните следующую команду:

```cmd
start python send.py
```

Поздравляем! Теперь вы можете отправлять сообщения в концентратор событий.

## <a name="receive-events"></a>Получение событий

Чтобы создать приложение Python, которое получает события из концентратора событий:

1. В редакторе Python создайте файл с именем *recv.py*.
2. Вставьте следующий код в *recv.py*, заменив концентраторы событий \<namespace >, \<eventhub >, \<AccessKeyName > и \<primary значение ключа > значениями. 
   
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

Чтобы запустить скрипт из каталога, в котором вы сохранили *recv.py*, выполните следующую команду:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о концентраторах событий см. в следующих статьях:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Функции и терминология в Центрах событий Azure](event-hubs-features.md)
- [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

