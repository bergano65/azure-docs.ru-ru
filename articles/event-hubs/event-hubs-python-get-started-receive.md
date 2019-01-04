---
title: Получение событий в службе "Центры событий Azure" с помощью Python | Документация Майкрософт
description: В статье описано, как создать приложение Python, которое получает события из службы "Центры событий Azure".
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: bc1cf07c5a74bc4d7182eea5281e75525fd04247
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103190"
---
# <a name="receive-events-from-event-hubs-using-python"></a>Получение событий из концентраторов событий с помощью Python

Концентраторы событий Azure — это высокомасштабируемая система управления событиями, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и другими системами. После сбора событий в концентратор событий вы можете получать и обрабатывать события с помощью внутрипроцессных обработчиков или перенаправлять их в другие аналитические системы.

Дополнительные сведения о концентраторах событий см. в статье [Что такое концентраторы событий Azure?][Event Hubs overview].

В этом руководстве представлены инструкции по получению событий из концентратора событий приложения, написанного на языке Python. Сведения об отправке событий см. в [этой статье](event-hubs-python-get-started-send.md).

Из [этих примеров GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) в руководство взят код, который можно проверить, чтобы увидеть полное рабочее приложение, включая инструкции импорта и объявления переменных. Другие примеры доступны в той же папке GitHub.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- Python 3.4 или более поздней версии.
- Существующее пространство имен Центров событий и концентратор событий. Эти сущности можно создать, следуя инструкциям в [этой статье](event-hubs-create.md). 

## <a name="install-python-package"></a>Установка пакета Python

Чтобы установить пакет Python для Центров событий, откройте окно командной строки с Python в пути, а затем выполните следующую команду. 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-receive-events"></a>Создание сценария Python для получения событий

Создайте приложение Python, получающее события из концентратора событий:

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code][Visual Studio Code].
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

## <a name="receive-events"></a>Получение событий

Чтобы выполнить сценарий, откройте окно командной строки с Python в пути, а затем выполните следующую команду.

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Дополнительная информация
В этом кратком руководстве вы создали приложение Python для получения сообщений из концентратора событий. Сведения о том, как отправлять события в концентратор событий с помощью Python, см. в статье [Отправка событий в Центры событий с помощью Python](event-hubs-python-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
