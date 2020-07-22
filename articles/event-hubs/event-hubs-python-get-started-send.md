---
title: Отправка и получение событий через Центры событий Azure с помощью Python (старая версия)
description: В этом пошаговом руководстве показано, как создать и выполнить скрипты Python, которые отправляют или получают события через Центры событий Azure с помощью старого пакета azure/event-hub версии 1.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 4ce53d2d82a00f98dbbd538bd7a61da9ba44e832
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314477"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Краткое руководство. Отправка и получение событий через Центры событий с использованием Python (azure-eventhub, версия 1)
В этом кратком руководстве показано, как отправлять события в концентратор событий и получать события из него с помощью пакета Python **azure-eventhub версии 1**. 

> [!WARNING]
> В этом кратком руководстве используется старый пакет azure-eventhub версии 1. Краткое руководство по использованию пакета azure-eventhub **последней версии 5** [для отправки и получения событий см. здесь](get-started-python-send-v2.md). Чтобы начать использовать в приложении новый пакет вместо старого, ознакомьтесь с руководством по [переходу с azure-eventhub версии 1 на версию 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Предварительные требования
Если вы впервые используете Центры событий Azure, ознакомьтесь с общими сведениями в [этой статье](event-hubs-about.md), прежде чем приступить к работе с этим руководством. 

Для работы с данным руководством необходимо следующее:

- **Подписка Microsoft Azure.** Чтобы использовать службы Azure, в том числе Центры событий Azure, потребуется действующая подписка.  Если у вас еще нет учетной записи Azure, [зарегистрируйтесь для работы с бесплатной пробной версией](https://azure.microsoft.com/free/) или [активируйте преимущества для подписчиков MSDN при создании учетной записи](https://azure.microsoft.com).
- Python 3.4 или более поздней версии, для которого установлено и обновлено `pip`.
- Пакет Python для Центров событий. Чтобы установить этот пакет, выполните следующую команду в окне командной строки, где путь к Python сохранен в переменной окружения path. 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Создайте пространство имен Центров событий и концентратор событий**. Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа Центров событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните инструкции из [этой статьи](event-hubs-create.md). Затем получите значение ключа доступа для концентратора событий, следуя инструкциям в приведенных ниже статьях. [Получение строки подключения на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Используйте ключ доступа в коде, который вы напишете далее в рамках этого краткого руководства. Имя ключа по умолчанию: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Отправка событий

Чтобы создать приложение Python, которое отправляет события в концентратор событий, выполните следующее.

> [!NOTE]
> Вы можете пропустить работу с кратким руководством и сразу скачать [примеры приложений](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) с сайта GitHub. Замените строки `EventHubConnectionString` и `EventHubName` значениями для реального концентратора событий.

1. Откройте редактор Python, например [Visual Studio Code](https://code.visualstudio.com/).
2. Создайте новый файл с именем *send.py*. Этот сценарий отправляет 100 событий в ваш концентратор событий.
3. Вставьте следующий код в *send.py*, заменив концентраторы событий \<namespace>, \<eventhub>, \<AccessKeyName> и \<primary key value> своими значениями: 
   
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
2. Вставьте следующий код в *recv.py*, заменив концентраторы событий \<namespace>, \<eventhub>, \<AccessKeyName> и \<primary key value> своими значениями: 
   
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

