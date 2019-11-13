---
title: Краткое руководство. Чтение собранных данных из приложения Python (Центры событий Azure)
description: Краткое руководство. Скрипты, в которых используется пакет SDK Azure Python для демонстрации функции "Сбор" службы "Центры событий".
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: ade4aa79b2de005bfecd7a5882f06cb491ea4e6d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717840"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python"></a>Краткое руководство. Пошаговое руководство. Использование функции "Сбор" в службе "Центры событий" с Python

Сбор — это функция Центров событий Azure. Она позволяет автоматически передавать потоковые данные из концентратора событий в выбранную учетную запись хранилища BLOB-объектов Azure. Эта возможность упрощает пакетную обработку данных потоковой передачи в режиме реального времени. В этой статье мы расскажем, как использовать функцию "Сбор" в Центрах событий с Python. Дополнительные сведения о функции "Сбор" в Центрах событий см. в [этой статье][Overview of Event Hubs Capture].

В этом пошаговом руководстве для демонстрации функции "Сбор" используется [пакет SDK Azure для Python](https://azure.microsoft.com/develop/python/). Программа *sender.py* отправляет имитацию телеметрии среды в Центры событий в формате JSON. Концентратор событий использует функцию "Сбор" для записи этих данных в хранилище BLOB-объектов отдельными пакетами. Приложение *capturereader.py* считывает эти большие двоичные объекты, создает файл для добавления данных по каждому устройству и записывает данные в *CSV-файлы*.

В этом пошаговом руководстве описаны следующие операции: 

> [!div class="checklist"]
> * Создайте учетную запись хранилища BLOB-объектов Azure и контейнер на портале Azure.
> * Включите функцию "Сбор" в Центрах событий и укажите целевую учетную запись хранения.
> * Отправьте данные в концентратор событий с помощью скрипта Python.
> * Прочитайте и обработайте файлы, полученные от функции "Сбор" службы "Центры событий", с помощью другого скрипта Python.

## <a name="prerequisites"></a>Предварительные требования

- Python 3.4 или более поздней версии, для которого установлено и обновлено `pip`.
  
- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
  
- Активное пространство имен Центров событий и концентратор событий, созданные по инструкциям из статьи [Краткое руководство. Создание концентратора событий с помощью портала Azure](event-hubs-create.md). Запишите имена пространства имен и концентратора событий, которое пригодятся далее в этом пошаговом руководстве. 
  
  > [!NOTE]
  > Если у вас уже есть подходящий контейнер хранилища, включите функцию "Сбор" и выберите этот контейнер хранилища при создании Центра событий. 
  > 
  
- Имя ключа общего доступа Центра событий и значение первичного ключа. Найдите или создайте эти значения в разделе **политик общего доступа** на странице Центров событий. По умолчанию для ключа доступа используется имя **RootManageSharedAccessKey**. Скопируйте имя ключа доступа и значение первичного ключа, которые потребуются далее в этом пошаговом руководстве. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Создание учетной записи хранилища BLOB-объектов Azure и контейнера

Создайте учетную запись хранения и контейнера для сбора данных. 

1. Войдите на [портале Azure][Azure portal].
2. На панели навигации слева выберите **Учетные записи хранения**, а затем на экране **Учетные записи хранения** нажмите **Добавить**.
3. На экране создания учетной записи хранения выберите подписку и группу ресурсов, затем присвойте имя учетной записи хранения. Для остальных параметров сохраните значения по умолчанию. Нажмите **Просмотр и создание**, проверьте параметры и нажмите **Создать**. 
   
   ![Создание учетной записи хранения][1]
   
4. После завершения развертывания выберите **Перейти к ресурсу** и на экране **обзорных сведений** для учетной записи хранения выберите **Контейнеры**.
5. На экране **Контейнеры** выберите **+ Контейнер**. 
6. На экране **Новый контейнер** присвойте контейнеру имя и нажмите **ОК**. Запишите имя контейнера — оно потребуется вам позже в этом пошаговом руководстве. 
7. В области навигации слева на экране **Контейнеры** выберите **Ключи доступа**. Скопируйте значения **Имя учетной записи хранения** и **Ключ** в разделе **key1**, чтобы использовать их позже в этом пошаговом руководстве.
 
## <a name="enable-event-hubs-capture"></a>Включение функции "Сбор" в службе "Центры событий"

1. На портале Azure перейдите к нужному концентратору событий, выбрав соответствующее пространство имен из списка **Все ресурсы**, затем выберите **Концентраторы событий** в области навигации слева и, наконец, сам концентратор событий. 
2. На экране **общих сведений** для концентратора событий выберите **Сбор событий**.
3. На экране **Сбор** выберите **Включено**. Затем в разделе **Контейнер хранилища Azure** нажмите **Выбрать контейнер**. 
4. На экране **Контейнеры** выберите контейнер хранилища, который вы намерены использовать, а затем нажмите **Выбрать**. 
5. На экране **Сбор** нажмите **Сохранить изменения**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Создание скрипта Python для отправки событий в Центр событий
Этот скрипт отправляет 200 событий в ваш концентратор событий. События здесь содержат простые данные о среде в формате JSON.

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code][Visual Studio Code].
2. Создайте новый файл с именем *sender.py*. 
3. Скопируйте приведенный ниже код в файл *sender.py*. Подставьте реальные значения следующих параметров Центра событий: \<namespace> (пространство имен), \<AccessKeyName> (имя ключа доступа), \<primary key value> (значение первичного ключа) и \<eventhub> (концентратор событий).
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Сохраните файл.

## <a name="create-a-python-script-to-read-capture-files"></a>Создание скрипта Python для чтения файлов, полученных функцией "Сбор"

Этот скрипт считывает собранные файлы и создает для каждого устройства отдельный файл, в который сохраняет данные по этому устройству.

1. В редакторе Python создайте новый файл с именем *capturereader.py*. 
2. Скопируйте приведенный ниже код и вставьте его в файл *capturereader.py*. Подставьте сохраненные ранее значения: \<storageaccount> (учетная запись хранения), \<storage account access key> (ключ доступа к учетной записи хранения) и \<storagecontainer> (контейнер хранения).
   
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
           if not parsed_json['id'] in dict:
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
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Запуск скриптов Python

1. Откройте окно командной строки, где путь к Python сохранен в переменной окружения path, а затем выполните следующие команды для установки пакетов необходимых компонентов Python.
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Если вы используете более раннюю версию `azure-storage` или `azure`, вам может потребоваться параметр `--upgrade`.
   
   Возможно, придется выполнить также следующую команду. На большинстве систем это не требуется. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Перейдите в ту папку, где были сохранены скрипты *sender.py* и *capturereader.py*, и выполните следующую команду.
   
   ```cmd
   start python sender.py
   ```
   
   Эта команда запускает новый процесс Python для выполнения скрипта sender.
   
3. Когда сбор данных завершится, выполните следующую команду:
   
   ```cmd
   python capturereader.py
   ```

   Обработчик собранных данных скачивает все непустые BLOB-объекты из контейнера учетной записи хранения и сохраняет полученные результаты в *CSV-файлах* в локальном каталоге. 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Центрах событий см. здесь: 

* [Общие сведения о функции "Сбор" в Центрах событий Azure][Overview of Event Hubs Capture]
* [Примеры приложений, использующих Центры событий](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Общие сведения о Центрах событий][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
