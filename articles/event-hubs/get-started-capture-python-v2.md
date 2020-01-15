---
title: Чтение сохраненных данных концентраторов событий Azure из приложения Python | Документация Майкрософт
description: В этой статье показано, как написать код Python для записи данных, отправляемых в концентратор событий, и чтения записанных данных событий из службы хранилища Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: d7a0c757f7314e45d5b4d13273df984739912b27
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942416"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Сбор данных концентраторов событий в службе хранилища Azure и их чтение с помощью Python 
Можно использовать настройку концентратора событий, чтобы данные, отправляемые в концентратор событий, задавались в службе хранилища Azure или Azure Data Lake Storage. В этой статье показано, как использовать написание кода Python для отправки событий в концентратор событий и считывания записанных данных из хранилища BLOB-объектов Azure. Дополнительные сведения об этой функции см. в статье [Обзор функций записи концентраторов событий](event-hubs-capture-overview.md).

В приведенном примере для демонстрации функции записи используется [пакет SDK Azure для Python](https://azure.microsoft.com/develop/python/). Программа sender.py отправляет имитацию телеметрии окружающей среды Центрам событий в формате JSON. В настройках концентратора событий предусмотрено использование функции записи для пакетной записи этих данных в хранилище BLOB-объектов. Приложение capturereader.py считывает эти большие двоичные объекты и создает для каждого устройства файл для дозаписи. Затем приложение записывает данные в CSV-файлы.

> [!IMPORTANT]
> В этом кратком руководстве используется версия 5 пакета SDK для концентраторов событий Azure для Python. Краткий старт, использующий старую версию 1 пакета SDK для Python, см. в [этой статье](event-hubs-capture-python.md). Если вы используете пакет SDK версии 1, рекомендуется перенести код в последнюю версию. Дополнительные сведения см. в разделе [руководств по миграции](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

В этом кратком руководстве вы: 

> [!div class="checklist"]
> * Создайте учетную запись хранилища BLOB-объектов Azure и контейнер на портале Azure.
> * Создайте пространство имен концентраторов событий с помощью портал Azure.
> * Создайте концентратор событий с включенной функцией записи и подключите его к вашей учетной записи хранения.
> * Отправьте данные в концентратор событий с помощью скрипта Python.
> * Прочитайте и обработайте файлы, полученные от функции "Сбор" службы "Центры событий", с помощью другого скрипта Python.

## <a name="prerequisites"></a>Технические условия

- Python 2,7 и 3,5 или более поздней версии с `pip` установлен и обновлен.
- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
- [Создайте пространство имен концентраторов событий и концентратор событий в пространстве имен](event-hubs-create.md). Запишите имя пространства имен концентраторов событий, имя концентратора событий и первичный ключ доступа для пространства имен. Получите ключ доступа, следуя инструкциям из статьи: [Получение строки подключения](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Имя ключа по умолчанию: **RootManageSharedAccessKey**. Строка подключения для учебника не требуется. Вам нужен только первичный ключ. 
- Выполните следующие действия, чтобы создать **учетную запись хранения Azure** и **контейнер больших двоичных объектов**.
    1. [Создайте учетную запись хранения Azure](/storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [Создайте контейнер больших двоичных объектов в хранилище](/storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [Получение строки подключения к учетной записи хранения](/storage/common/storage-configure-connection-string?#view-and-copy-a-connection-string)

        Запишите **строку подключения** и **имя контейнера**. Они будут использоваться позже в коде. 
- Включите функцию **захвата** для концентратора событий, следуя инструкциям из раздела [Включение записи концентраторов событий с помощью портал Azure](event-hubs-capture-enable-through-portal.md). Выберите учетную запись хранения и контейнер больших двоичных объектов, созданный на предыдущем шаге. Эту функцию также можно включить при создании концентратора событий. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Создание сценария Python для отправки событий в концентратор событий
В этом разделе вы создадите скрипт Python, который отправляет события 200 (10 устройств * 20 событий) в концентратор событий. Эти события представляют собой пример чтения окружающей среды, отправляемой в формате JSON. 

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code][Visual Studio Code].
2. Создайте сценарий с именем **sender.py**. 
3. Вставьте следующий код в sender.py. Дополнительные сведения см. в комментариях к коду. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Замените следующие значения в скриптах:
    1. Замените `EVENT HUBS NAMESPACE CONNECTION STRING` строкой подключения для пространства имен концентраторов событий.
    2. Замените `EVENT HUB NAME` именем концентратора событий. 
5. Запустите скрипт, чтобы отправить события в концентратор событий. 
6. В портал Azure можно проверить, что концентратор событий получил сообщения. Переключитесь в представление **сообщений** в разделе **метрики** . Обновите страницу, чтобы обновить диаграмму. На отображение полученных сообщений может уйти несколько секунд. 

    [![убедиться, что концентратор событий получил сообщения](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Создание скрипта Python для чтения файлов записи
В этом примере захваченные данные хранятся в хранилище BLOB-объектов Azure. Сценарий в этом разделе считывает файлы данных записи из хранилища Azure и создает CSV-файлы, чтобы легко открыть и просмотреть содержимое. Вы увидите 10 файлов в текущем рабочем каталоге приложения. Эти файлы будут содержать считывание окружающей среды для 10 устройств. 

1. В редакторе Python создайте скрипт с именем **capturereader.py**. Он считывает собранные файлы и создает для каждого устройства отдельный файл для записи данных только по этому устройству.
2. Вставьте следующий код в capturereader.py. Дополнительные сведения см. в комментариях к коду. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Замените `<AZURE STORAGE CONNECTION STRING>` строкой подключения для учетной записи хранения Azure. Имя контейнера, созданного в этом руководстве: **Capture**. Если вы использовали другое имя для контейнера, замените `capture` именем контейнера в учетной записи хранения. 

## <a name="run-the-scripts"></a>Выполнение сценариев
1. Откройте окно командной строки с Python в соответствующем расположении, а затем выполните следующие команды для установки пакетов необходимых компонентов Python.
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Укажите папку, в которой были сохранены скрипты sender.py и capturereader.py, и выполните следующую команду.
   
   ```
   python sender.py
   ```
   
   Эта команда запускает новый процесс Python для выполнения скрипта sender.
3. Подождите несколько минут до запуска записи. Теперь введите следующую команду в исходном окне командной строки.
   
   ```
   python capturereader.py
   ```

   Этот обработчик записи загружает все большие двоичные объекты из учетной записи хранения или контейнера в локальный каталог. Он обрабатывает все непустые файлы и записывает результаты в CSV-файлы в локальном каталоге.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с примерами Python на сайте GitHub [здесь](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
