---
title: Доступ к данным в хранилищах данных или больших двоичных объектах для обучения
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать хранилища данных BLOB-объектов для доступа к данным во время обучения с помощью Службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: c171e35c6542febffc666ad5abfab50e093bb698
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359285"
---
# <a name="access-data-from-your-datastores"></a>Доступ к данным из вашего хранилища данных

Хранилища данных позволяют взаимодействовать и получить доступ к данным, используется ли ваш код локально, в вычислительном кластере или на виртуальной машине. В этой статье Узнайте, что рабочие процессы машинного обучения Azure, которые обеспечивают вашего хранилища данных доступны и становятся доступными в качестве контекста вычисления.

В этом практическом руководстве показаны примеры для выполнения следующих задач:
* [Выберите хранилище данных](#access)
* [Получение данных](#get)
* [Отправка и загрузка данных для хранилища данных](#up-and-down)
* [Хранилище данных доступа во время обучения](#train)

## <a name="prerequisites"></a>Технические условия

Для использования хранилища данных, необходим [рабочей области](concept-azure-machine-learning-architecture.md#workspace) первого. 

Необходимо начать с [создания новой рабочей области](setup-create-workspace.md#sdk) или получить существующую.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Выберите хранилище данных

Вы можете использовать хранилище данных по умолчанию или добавьте свои собственные.

### <a name="use-the-default-datastore-in-your-workspace"></a>Используйте хранилище данных по умолчанию в рабочей области

Нет необходимости создать или настроить учетную запись хранения, так как каждая рабочая область содержит хранилище данных по умолчанию. Можно использовать хранилище данных прямо сейчас, зарегистрированный в рабочей области. 

Чтобы получить хранилище данных рабочей области по умолчанию:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Регистрация собственного хранилища данных с рабочей областью
Если у вас уже есть существующая служба хранилища Azure, ее можно зарегистрировать как хранилище данных для рабочей области.   Метод регистрации находятся на [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) , что позволяет форме register_azure_ *. 

Вам необходимо зарегистрировать контейнер BLOB-объектов Azure или файловый ресурс Azure как хранилище данных в следующих примерах.

+ Для **хранилище данных контейнера BLOB-объектов Azure**, используйте [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Для **хранилище данных общей папки Azure файловой**, использовать [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Например:  
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>& Найти определение хранилища данных

Чтобы получить указанное хранилище данных, зарегистрированные в текущей рабочей области, используйте [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Чтобы получить список всех хранилищ данных в заданной рабочей области, используйте следующий код:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Чтобы определить хранилище данных по умолчанию для текущей рабочей области, используйте [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Отправка и загрузка данных
[ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) И [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) зависят от методов, описанных в следующих примерах и работают аналогично для [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) и [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) классы.

### <a name="upload"></a>Передать

 Каталог или отдельные файлы передайте в хранилище данных с помощью пакета SDK для Python.

Чтобы отправить каталог в хранилище данных `ds`:

```Python
import azureml.data
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Хранилища данных доступа во время обучения

После опубликования в хранилище данных на удаленных вычислений, доступен во время учебных запусков (например, данные обучения или проверки), просто передав путь к нему, как параметр в скрипте обучения.

В следующей таблице перечислены распространенные [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) неподдерживаемые методы предоставления хранилища данных на удаленных вычислений.

# #

способ|Метод|ОПИСАНИЕ
----|-----|--------
Подключение| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Используйте для подключения хранилища данных на удаленных вычислений. Режим по умолчанию для хранилища данных.
Download (Скачать)|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Использовать для загрузки данных из расположения, указанного `path_on_compute` на хранилище данных для удаленных вычислений.
Передать|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Использовать для отправки данных в корне хранилища данных в расположении, заданном параметром `path_on_compute`.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Чтобы сослаться на определенную папку или файл в хранилище данных, можно использовать функцию хранилища данных [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-).

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> Любой объект `ds` или `ds.path` разрешается в имя переменной среды формата `"$AZUREML_DATAREFERENCE_XXXX"`, значение которой представляет путь подключения или загрузки в объекте удаленных вычислений. Путь к хранилищу данных на удаленных вычислений не может быть таким же, как путь выполнения для сценария обучения.

### <a name="examples"></a>Примеры 

Ниже показаны примеры, относящиеся к [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс для доступа к в хранилище данных во время обучения.


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

Так как `as_mount()` режим используется по умолчанию для хранилища данных, можно также напрямую передать `ds` для `'--data_dir'` аргумент.

Или передать конструктору механизм оценки в список хранилищ данных `inputs` параметр для подключения или копирования из вашего хранилища данных. Данный пример кода:
* Загружает все содержимое в хранилище данных `ds1` для удаленных вычислений, прежде чем сценарий обучения `train.py` выполняется
* Папка скачиваний `'./foo'` в хранилище данных `ds2` для удаленных вычислений перед `train.py` выполняется
* Передает файл `'./bar.pkl'` из удаленных вычислений до хранилища данных `ds3` после выполнения скрипта

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>Дальнейшие действия

* [Обучение модели](how-to-train-ml-models.md)

* [Развертывание модели](how-to-deploy-and-where.md)
