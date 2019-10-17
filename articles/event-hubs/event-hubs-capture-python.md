---
title: Чтение собранных данных из приложения Python в службе "Центры событий Azure" | Документация Майкрософт
description: Скрипты, использующие пакет SDK для Azure Python для демонстрации функции записи концентраторов событий.
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
ms.topic: article
ms.custom: seodec18
ms.date: 10/10/2019
ms.author: shvija
ms.openlocfilehash: 354964e1b66b55dcccd9b5674f011f8c5a38a1c5
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428950"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Пошаговое руководство. Использование функции "Сбор" в Центрах событий с Python

Сбор — это функция Центров событий Azure. Вы можете использовать захват для автоматической доставки потоковых данных в концентраторе событий в выбранную учетную запись хранилища BLOB-объектов Azure. Эта возможность упрощает выполнение пакетной обработки данных потоковой передачи в реальном времени. В этой статье мы расскажем, как использовать функцию "Сбор" в Центрах событий с Python. Дополнительные сведения о записи концентраторов событий см. в статье [Захват событий с помощью концентраторов событий Azure][Overview of Event Hubs Capture].

В этом пошаговом руководстве для демонстрации функции записи используется [пакет SDK для Azure Python](https://azure.microsoft.com/develop/python/) . Программа *sender.py* отправляет смоделированные данные телеметрии окружающей среды в концентраторы событий в формате JSON. Концентратор событий использует функцию записи для записи этих данных в хранилище BLOB-объектов в пакетах. Приложение *capturereader.py* считывает эти большие двоичные объекты, создает файл добавления для каждого устройства и записывает данные в *CSV* -файлы на каждом устройстве.

В этом пошаговом руководстве описаны следующие операции: 

> [!div class="checklist"]
> * Создайте учетную запись хранилища BLOB-объектов Azure и контейнер в портал Azure.
> * Включите запись концентраторов событий и направьте ее в учетную запись хранения.
> * Отправка данных в концентратор событий с помощью скрипта Python.
> * Чтение и обработка файлов из записи концентраторов событий с помощью другого скрипта Python.

## <a name="prerequisites"></a>Технические условия

- Python 3,4 или более поздней версии с установленным и обновленным `pip`.
  
- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.
  
- Активное пространство имен концентраторов событий и концентратор событий, созданное с помощью инструкций в разделе [Краткое руководство по созданию концентратора событий с использованием портал Azure](event-hubs-create.md). Запишите имя пространства имен и концентратора событий, которое будет использоваться далее в этом пошаговом руководстве. 
  
  > [!NOTE]
  > Если у вас уже есть контейнер хранилища для использования, можно включить запись и выбрать контейнер хранилища при создании концентратора событий. 
  > 
  
- Имя ключа общего доступа концентратора событий и значение первичного ключа. Найдите или создайте эти значения в разделе **политики общего доступа** на странице концентраторов событий. Имя ключа доступа по умолчанию — **RootManageSharedAccessKey**. Скопируйте имя ключа доступа и значение первичного ключа для дальнейшего использования в этом пошаговом руководстве. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Создание учетной записи хранилища BLOB-объектов Azure и контейнера

Создайте учетную запись хранения и контейнер, которые следует использовать для записи. 

1. Войдите на [портале Azure][Azure portal].
2. На панели навигации слева выберите **учетные записи хранения**, а затем на экране **учетные записи хранения** выберите **добавить**.
3. На экране создания учетной записи хранения выберите подписку и группу ресурсов и присвойте учетной записи хранения имя. Можно оставить другие значения по умолчанию. Выберите **Проверка + создать**, просмотрите параметры, а затем щелкните **создать**. 
   
   ![Создание учетной записи хранения][1]
   
4. После завершения развертывания выберите **Переход к ресурсу**, а затем **на экране** учетная запись хранения выберите **контейнеры**.
5. На экране **контейнеры** выберите **+ контейнер**. 
6. На экране **новый контейнер** присвойте контейнеру имя, а затем нажмите кнопку **ОК**. Запишите имя контейнера, которое будет использоваться далее в этом пошаговом руководстве. 
7. В левой области навигации экрана **контейнеры** выберите **ключи доступа**. Скопируйте **имя учетной записи хранения**и значение **ключа** в поле **Key1**, чтобы использовать его позже в этом пошаговом руководстве.
 
## <a name="enable-event-hubs-capture"></a>Включить запись концентраторов событий

1. В портал Azure перейдите к концентратору событий, выбрав его пространство имен концентраторов событий из **всех ресурсов**, выбрав **концентраторы событий** в левой области навигации, а затем выбрав концентратор событий. 
2. На экране **обзора** концентратора событий выберите **Захват событий**.
3. На экране **Capture (запись** ) выберите **вкл**. Затем в разделе **контейнер хранилища Azure**выберите **выбрать контейнер**. 
4. На экране **контейнеры** выберите контейнер хранилища, который необходимо использовать, а затем нажмите **кнопку Выбрать**. 
5. На экране **Capture (запись** ) выберите **Save Changes (сохранить изменения**). 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Создание скрипта Python для отправки событий в концентратор событий
Этот скрипт отправляет 200 событий в ваш концентратор событий. События — это простые считывания среды в формате JSON.

1. Откройте предпочитаемый редактор Python, например [Visual Studio Code][Visual Studio Code].
2. Создайте новый файл с именем *sender.py*. 
3. Вставьте следующий код в *sender.py*. Замените собственные значения для концентраторов событий \<namespace >, \<AccessKeyName >, \<primary значение ключа > и \<eventhub >.
   
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

## <a name="create-a-python-script-to-read-capture-files"></a>Создание скрипта Python для чтения файлов записи

Этот сценарий считывает записанные файлы и создает файл для каждого устройства, чтобы записывать данные только для этого устройства.

1. В редакторе Python создайте новый файл с именем *capturereader.py*. 
2. Вставьте следующий код в *capturereader.py*. Замените сохраненные значения для учетной записи \<storageaccount >, > ключа доступа \<storage и \<storagecontainer >.
   
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

## <a name="run-the-python-scripts"></a>Выполнение скриптов Python

1. Откройте командную строку с Python в пути и выполните следующие команды, чтобы установить пакеты необходимых компонентов Python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Если у вас более ранняя версия `azure-storage` или `azure`, может потребоваться использовать параметр `--upgrade`.
   
   Также может потребоваться выполнить следующую команду. Выполнение этой команды не является обязательным в большинстве систем. 
   
   ```cmd
   pip install cryptography
   ```
   
2. В каталоге, в котором вы сохранили *sender.py* и *capturereader.py*, выполните следующую команду:
   
   ```cmd
   start python sender.py
   ```
   
   Команда запускает новый процесс Python для запуска отправителя.
   
3. После завершения записи выполните следующую команду:
   
   ```cmd
   python capturereader.py
   ```

   Обработчик записи загружает все непустые большие двоичные объекты из контейнера учетной записи хранения и записывает результаты в виде *CSV* -файлов в локальный каталог. 

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о концентраторах событий см. в следующих статьях: 

* [Общие сведения о записи концентраторов событий][Overview of Event Hubs Capture]
* [Примеры приложений, использующих Центры событий](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Общие сведения о Центрах событий][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
