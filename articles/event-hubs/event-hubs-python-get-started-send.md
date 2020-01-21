---
title: Краткое руководство. Отправка и получение событий с помощью Python (Центры событий Azure)
description: Краткое руководство. В этом пошаговом руководстве показано, как создать и выполнить скрипты Python, которые отправляют или получают события через Центры событий Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/08/2020
ms.author: shvija
ms.openlocfilehash: c4fa9e6038f4007246552610f537825f9def92a8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75939952"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python"></a>Краткое руководство. Отправка и получение событий через Центры событий Azure с использованием Python

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию от распределенных программных и аппаратных систем. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Дополнительные сведения о Центрах событий см. в статьях [Центры событий Azure — платформа потоковой передачи больших данных и служба приема событий](event-hubs-about.md) и [Features and terminology in Azure Event Hubs](event-hubs-features.md) (Функции и терминология в Центрах событий Azure).

В этом кратком руководстве показано, как создать приложения Python, которые отправляют или получают события через концентратор событий. 

> [!IMPORTANT]
> В рамках этого краткого руководства используется версия 1 пакета SDK Центров событий Azure для Python. Если вы не знакомы с Центрами событий Azure, используйте пакет SDK для Python версии 5. Краткое руководство по использованию пакета SDK для Python версии 5 см. [здесь](get-started-python-send-v2.md). Чтобы перенести существующий код с версии 1 на версию 5, ознакомьтесь с [этим руководством по миграции](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


> [!NOTE]
> Вы можете пропустить работу с кратким руководством и сразу скачать [примеры приложений](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) с сайта GitHub. Замените строки `EventHubConnectionString` и `EventHubName` значениями для реального концентратора событий. 

## <a name="prerequisites"></a>предварительные требования

Для работы с данным руководством необходимо следующее:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- Активное пространство имен Центров событий и концентратор событий, созданные по инструкциям из статьи [Краткое руководство. Создание концентратора событий с помощью портала Azure](event-hubs-create.md). Запишите имена пространства имен и концентратора событий, которые пригодятся далее в этом пошаговом руководстве. 
- Имя ключа общего доступа и значение первичного ключа для пространства имен Центров событий. Получите имя и значение ключа доступа, следуя инструкциям в разделе [Get connection string from the portal](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) (Получение строки подключения на портале). По умолчанию для ключа доступа используется имя **RootManageSharedAccessKey**. Скопируйте имя ключа и значение первичного ключа, которые потребуются далее в этом пошаговом руководстве. 
- Python 3.4 или более поздней версии, для которого установлено и обновлено `pip`.
- Пакет Python для Центров событий. Чтобы установить этот пакет, выполните следующую команду в окне командной строки, где путь к Python сохранен в переменной окружения path. 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Код в этом кратком руководстве использует текущую стабильную версию 1.3.1 пакета SDK для Центров событий. Пример кода для предварительной версии пакета SDK см. на странице [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Отправка событий

Чтобы создать приложение Python, которое отправляет события в концентратор событий, выполните следующее.

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

