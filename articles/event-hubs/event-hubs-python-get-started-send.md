---
title: Отправка событий в Центры событий Azure с помощью Python | Документация Майкрософт
description: Статья содержит пошаговое руководство по созданию приложения Python, которое отправляет события в концентраторы событий Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 11/16/2018
ms.author: shvija
ms.openlocfilehash: 2168fc89134615ffb4e0e718cc0cc27b8c1a7839
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262777"
---
# <a name="send-events-to-event-hubs-using-python"></a>Отправка событий в Центры событий с помощью Python

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом руководстве представлены инструкции по отправке событий в концентратор событий из приложения, написанного на Python. 

> [!NOTE]
> Вы можете скачать это краткое руководство в качестве примера с сайта [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), заменить строки `EventHubConnectionString` и `EventHubName` значениями для своего концентратора событий и выполнить этот пример. Или следуйте инструкциям из этого руководства, чтобы создать собственное решение.

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- Python 3.4 или более поздней версии.


## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Создание пространства имен Центров событий и концентратора событий
Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа Центров событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните инструкции из [этой статьи](event-hubs-create.md).

Получите значение ключа доступа для концентратора событий, следуя [Получение строки подключения на портале](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Используйте ключ доступа в коде, который вы напишете далее в рамках этого руководства. Имя ключа по умолчанию: **RootManageSharedAccessKey**.

Теперь приступите к следующим действиям в этом руководстве:

## <a name="install-python-package"></a>Установка пакета Python

Чтобы установить пакет Python для Центров событий, откройте окно командной строки с Python в пути, а затем выполните следующую команду. 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>Создание сценария Python для отправки событий

Создайте приложение Python, отправляющее события в концентратор событий:

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code][Visual Studio Code].
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

## <a name="run-application-to-send-events"></a>Запуск приложения для отправки событий

Чтобы выполнить сценарий, откройте окно командной строки с Python в пути, а затем выполните следующую команду.

```bash
start python send.py
```

Поздравляем! Теперь вы можете отправлять сообщения в концентратор событий.
 
## <a name="next-steps"></a>Дальнейшие действия
В рамках работы с этим кратким руководством вы отправляли сообщения в концентратор событий с помощью Python. Чтобы узнать, как получать события из концентратора событий с помощью Python, см. статью [Получение событий из концентраторов событий с помощью Python](event-hubs-python-get-started-receive.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
