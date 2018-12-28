---
title: Использование Python для отправки и получения сообщений в Центрах событий Azure | Документация Майкрософт
description: Узнайте, как отправлять и получать события, а также регистрировать события, передающиеся через Центры событий, с помощью Python.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 4a0e2cd7e0c768512e1aafc042fe55338fdc206e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084980"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Как использовать Центры событий Azure из приложения Python
Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Дополнительные сведения см. в статье [Центры событий Azure представляют собой платформу потоковой передачи и службу приема событий.](event-hubs-what-is-event-hubs.md) 

В этой статье содержатся ссылки на статьи, в которых показано, как выполнить следующие задачи из приложения, написанного на **Python**:

- [отправка событий в концентратор событий](#send-events-to-event-hubs);
- [получение событий из концентратора событий](#receive-events-from-event-hubs);
- [чтение собранных данных о событиях из хранилища Azure](#capture-event-hubs-data). 

## <a name="prerequisites"></a>Предварительные требования
- Создайте концентратор событий, используя одно из кратких руководств в зависимости от используемого средства: [портал Azure](event-hubs-create.md), [Azure CLI](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [шаблон Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 или более поздней версии, установленный на компьютере.

## <a name="install-python-package"></a>Установка пакета Python

Чтобы установить пакет Python для Центров событий, откройте окно командной строки с Python в пути, а затем выполните следующую команду. 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Отправка событий в службу "Центры событий"
В следующем коде показано, как отправлять события в концентратор событий из приложения Python: 

1. Создайте переменные для хранения URL-адреса концентратора событий, имени ключа и значения ключа. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Создайте клиент Центров событий, добавьте отправителя, запустите клиент, отправьте событие с помощью отправителя, а затем, когда вы закончите, остановите клиент. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Полное руководство по отправке событий в концентратор событий из приложения, написанного на Python, см. в [этой статье](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Получение событий из Центров событий
В следующем коде показано, как получать события из концентратора событий в приложении Python: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Полное руководство по получению событий из концентратора событий в приложении, написанном на Python, см. в [этой статье](event-hubs-python-get-started-receive.md).

## <a name="read-capture-event-data-from-azure-storage"></a>Чтение собранных данных о событиях из хранилища Azure
В следующем коде показано, как из приложения Python читать собранные данные о событиях, хранящиеся в **хранилище BLOB-объектов**: Чтобы включить возможность **сбора** для концентратора событий, следуйте указаниям из этой статьи: [Включение сбора событий потоковой передачи из Центров событий Azure](event-hubs-capture-enable-through-portal.md). Перед тестированием кода отправьте некоторые события в концентратор событий. 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')

```

Полное руководство по чтению собранных данных Центров событий в хранилище BLOB-объектов из приложения, написанного на Python, см. в [этой статье](event-hubs-capture-python.md).

## <a name="github-samples"></a>Примеры GitHub
Вы можете просмотреть больше примеров Python в репозитории Git [azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/).

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со статьями в разделе "Основные понятия" статьи [об общих сведениях о возможностях Центров событий](event-hubs-features.md).