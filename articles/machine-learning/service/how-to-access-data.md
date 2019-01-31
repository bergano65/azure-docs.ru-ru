---
title: Доступ к данным в хранилищам данных для обучения
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать хранилища данных для доступа к данным во время обучения с помощью Службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: a4960f3e29011948ec30fbc24222d8a6dccf6b8a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252121"
---
# <a name="access-data-during-training-from-your-datastores"></a>Доступ к данным в хранилище данных во время обучения
Чтобы получить доступ к данным или взаимодействию с ними в рабочих процессах машинного обучения Azure, используйте хранилище данных.

В службах машинного обучения Azure хранилище данных — это абстракция над [службой хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction). Хранилище данных может ссылаться на контейнер [больших двоичных объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) или [общую папку Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) в качестве базового хранилища. 

## <a name="create-a-datastore"></a>Создание хранилища данных
Для использования хранилищ данных необходима [рабочая область](concept-azure-machine-learning-architecture.md#workspace). Необходимо начать с [создания новой рабочей области](quickstart-create-workspace-with-python.md) или получить существующую.

```Python
import azureml.core
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="use-the-default-datastore"></a>Использование хранилища данных по умолчанию
Нет необходимости в создании или конфигурации учетной записи хранения.  Каждая рабочая область имеет хранилище данных по умолчанию, которое можно сразу же использовать.

Чтобы получить хранилище данных рабочей области по умолчанию:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-a-datastore"></a>Регистрация хранилища данных
Если у вас уже есть существующая служба хранилища Azure, ее можно зарегистрировать как хранилище данных для рабочей области. Контейнер BLOB-объектов Azure или общую папку Azure можно зарегистрировать как хранилище данных. Все методы регистрации находятся в классе `Datastore` и имеют форму `register_azure_*`.

#### <a name="azure-blob-container-datastore"></a>Хранилище данных "Контейнер BLOB-объектов Azure"
Чтобы зарегистрировать контейнер больших двоичных объектов Azure в качестве хранилища данных:

```Python
ds = Datastore.register_azure_blob_container(workspace=ws, 
                                             datastore_name='your datastore name', 
                                             container_name='your azure blob container name',
                                             account_name='your storage account name', 
                                             account_key='your storage account key',
                                             create_if_not_exists=True)
```

#### <a name="azure-file-share-datastore"></a>Хранилище данных "Общая папка Azure"
Чтобы зарегистрировать общую папку Azure в качестве хранилища данных:

```Python
ds = Datastore.register_azure_file_share(workspace=ws, 
                                         datastore_name='your datastore name', 
                                         container_name='your file share name',
                                         account_name='your storage account name', 
                                         account_key='your storage account key',
                                         create_if_not_exists=True)
```

### <a name="get-an-existing-datastore"></a>Получение существующего хранилища данных
Чтобы запросить зарегистрированное хранилище данных по имени:
```Python
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Также можно получить все хранилища данных для рабочей области:
```Python
datastores = ws.datastores()
for name, ds in datastores.items(),
    print(name, ds.datastore_type)
```

Если вы хотите задать одно из зарегистрированных хранилищ данных в качестве хранилища данных по умолчанию для рабочей области, сделайте это следующим образом.
```Python
ws.set_default_datastore('your datastore name')
```

## <a name="upload-and-download-data"></a>Отправка и загрузка данных
### <a name="upload"></a>Передать
Каталог или отдельные файлы передайте в хранилище данных с помощью пакета SDK для Python.

Чтобы отправить каталог в хранилище данных `ds`:
```Python
ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` указывает расположение в общей папке (или контейнере больших двоичных объектов) для отправки. По умолчанию используется значение `None`: в этом случае данные отправляются в корневой каталог. `overwrite=True` выполняет перезапись всех существующих данных в `target_path`.

Кроме того, можно передать список отдельных файлов в хранилище данных с помощью метода хранилища данных `upload_files()`.

### <a name="download"></a>Download (Скачать)
Аналогичным образом можно загрузить данные из хранилища данных в локальную файловую систему.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` — это расположение локального каталога для загрузки данных. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` имеет значение `None`, будет загружено все содержимое общей папки (или контейнера больших двоичных объектов).

## <a name="access-datastores-for-training"></a>Доступ к хранилищам данных для обучения
Для получения доступа к хранилищу данных во время учебного запуска (например, для обучения или проверки данных) на целевом объекте удаленных вычислений используется пакет SDK для Python. 

Существует два способа сделать хранилище данных доступным для объекта удаленных вычислений:
* **Подключение**  
`ds.as_mount()`: при выборе этого режима подключения хранилище данных будет подключено к объекту удаленных вычислений. 
* **Загрузка или отправка**  
    * `ds.as_download(path_on_compute='your path on compute')` загружает данные из вашего хранилища данных в объект удаленных вычислений в расположении, заданном параметром `path_on_compute`.
    * `ds.as_upload(path_on_compute='yourfilename'` передает данные в хранилище данных.  Предположим, что сценарий обучения создает файл `foo.pkl` в текущем рабочем каталоге на объекте удаленных вычислений. После создания файла передайте его в хранилище данных с помощью `ds.as_upload(path_on_compute='./foo.pkl')`. Файл будет передан в корневой каталог хранилища данных.
    
Чтобы сослаться на определенную папку или файл в хранилище данных, можно использовать функцию хранилища данных **`path`**. Например, чтобы загрузить содержимое каталога `./bar` из хранилища данных для целевого объекта вычислений, используйте `ds.path('./bar').as_download()`.

Любой объект `ds` или `ds.path` разрешается в имя переменной среды формата `"$AZUREML_DATAREFERENCE_XXXX"`, значение которой представляет путь подключения или загрузки в объекте удаленных вычислений. Путь к хранилищу данных в объекте удаленных вычислений не может совпадать с путем выполнения для скрипта.

Чтобы получить доступ к хранилищу данных во время обучения, передайте его в скрипт обучения как аргумент командной строки с помощью `script_params`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```
`as_mount()` — режим по умолчанию для хранилища данных, поэтому можно просто передать `ds` в аргумент `'--data_dir'` напрямую.

Передаете список хранилищ данных в параметр `inputs` конструктора средства оценки, чтобы выполнить подключение или копирование в хранилище данных или из него.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```
Приведенный выше код выполняет следующие действия:
* загружает все содержимое в хранилище данных `ds1` в объект удаленных вычислений перед запуском скрипта обучения `train.py`;
* загружает папку `'./foo'` в хранилище данных `ds2` в объект удаленных вычислений перед запуском `train.py`;
* отправляет файл `'./bar.pkl'` из объекта удаленных вычислений в хранилище данных `d3` после выполнения скрипта.

## <a name="next-steps"></a>Дополнительная информация
* [Обучение модели](how-to-train-ml-models.md)
