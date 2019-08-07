---
title: Доступ к данным в службах хранилища Azure
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать хранилища данных для доступа к службам хранилища Azure во время обучения со службой Машинное обучение Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 4bc035ba061a65f6770136240d8867f82858e67e
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772730"
---
# <a name="access-data-in-azure-storage-services"></a>Доступ к данным в службах хранилища Azure

 Из этой статьи вы узнаете, как легко получить доступ к данным в службах хранилища Azure с помощью Машинное обучение Azure хранилищ данных. Хранилища данных используются для хранения сведений о подключении, таких как идентификатор подписки и авторизация маркера, для доступа к хранилищу без необходимости жесткого кодирования информации в скриптах.

В этом пошаговом окне приведены примеры следующих задач.
* [Регистрация хранилищ данных](#access)
* [Получение хранилищ данных из рабочей области](#get)
* [Отправка и скачивание данных с помощью хранилищ](#up-and-down)
* [Доступ к данным во время обучения](#train)

## <a name="prerequisites"></a>предварительные требования

Для использования хранилищ данных необходима [рабочая область](concept-workspace.md).

Необходимо начать с [создания новой рабочей области](setup-create-workspace.md#sdk) или получить существующую.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Регистрация хранилищ данных

Все методы Register относятся [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) к классу и имеют форму register_azure_ *.

В следующих примерах показано, как зарегистрировать контейнер больших двоичных объектов Azure или файловый ресурс Azure в качестве хранилища данных.

+ Для **хранилища данных контейнера больших двоичных объектов Azure**используйте[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ Для **хранилища данных файлового ресурса Azure**используйте [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Пример: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Мы рекомендуем использовать контейнер больших двоичных объектов Azure. Хранилище уровня "Стандартный" и "Премиум" доступны для больших двоичных объектов. Хотя это и более дорого, мы рекомендуем хранилище уровня "Премиум" из-за более высоких скоростей пропускной способности, которые могут повысить скорость обучения, особенно при обучении большого набора данных. Сведения о стоимости учетной записи хранения см. в [калькуляторе цен Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Получение хранилищ данных из рабочей области

Чтобы получить конкретное хранилище данных, зарегистрированное в текущей рабочей области, [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) используйте статический метод для класса хранилища данных:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Чтобы получить список хранилищ данных, зарегистрированных в заданной рабочей области, можно использовать `datastores` свойство для объекта рабочей области:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

При создании рабочей области контейнер больших двоичных объектов Azure и файловый ресурс Azure регистрируются в рабочей области с `workspaceblobstore` именем `workspacefilestore` и соответственно. Они хранят сведения о подключении контейнера больших двоичных объектов и общей папки, подготовленной в учетной записи хранения, подключенной к рабочей области. `workspaceblobstore` Задается как хранилище данных по умолчанию.

Чтобы получить хранилище данных рабочей области по умолчанию:

```Python
datastore = ws.get_default_datastore()
```

Чтобы определить другое хранилище данных по умолчанию для текущей рабочей области, [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) используйте метод для объекта рабочей области:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Отправка & скачивание данных
Методы [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) и [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) , описанные в следующих примерах, относятся к классам [азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) и [азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) и работают одинаково.

### <a name="upload"></a>Отправьте

 Каталог или отдельные файлы передайте в хранилище данных с помощью пакета SDK для Python.

Чтобы отправить каталог в хранилище данных `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` Параметр указывает расположение в общей папке (или контейнере больших двоичных объектов) для передачи. По умолчанию используется значение `None`: в этом случае данные отправляются в корневой каталог. При `overwrite=True` перезапись любых существующих `target_path` данных в.

Или отправьте список отдельных файлов в хранилище данных с помощью `upload_files()` метода.

### <a name="download"></a>Загрузить

Аналогичным образом можно загрузить данные из хранилища данных в локальную файловую систему.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` Параметр — это расположение локального каталога, в который загружаются данные. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` имеет значение `None`, будет загружено все содержимое общей папки (или контейнера больших двоичных объектов).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Доступ к данным во время обучения

Чтобы получить доступ к данным во время обучения, можно скачать или подключить данные из служб хранилища Azure к целевому объекту вычислений с помощью хранилищ данных.

В следующей таблице перечислены методы, которые сообщают целевому объекту вычислений, как использовать хранилища данных во время выполнения. 

Одностороннюю|Метод|Описание|
----|-----|--------
Подключить| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Используйте для подключения хранилища данных к целевому объекту вычислений.
Загрузить|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Используйте для загрузки содержимого хранилища данных в расположение, `path_on_compute`указанное в. <br> Этот процесс загрузки выполняется перед запуском.
Отправьте|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Используйте для передачи файла из расположения, указанного `path_on_compute` в хранилище данных. <br> Эта отправка происходит после выполнения.

Для ссылки на определенную папку или файл в хранилище данных и сделать его доступным в целевом объекте вычислений используйте метод хранилища данных [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) .

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Любой `datastore` объект `datastore.path` или разрешается в имя переменной среды формата `"$AZUREML_DATAREFERENCE_XXXX"`, значение которого представляет путь подключения или загрузки в целевом вычислении. Путь к хранилищу данных в целевом вычислении может отличаться от пути выполнения для обучающего скрипта.

### <a name="examples"></a>Примеры 

Следующие примеры кода относятся к [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) классу для доступа к данным во время обучения. 

`script_params`— Это словарь, содержащий параметры для entry_script. Его можно использовать для передачи в хранилище данных и описания того, как данные должны быть доступны в целевом объекте вычислений. Ознакомьтесь с нашим комплексным [руководством](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Вы также можете передать список хранилищ данных в параметр конструктора `inputs` оценщика, чтобы подключать или копировать данные в хранилища (или из них). Этот пример кода:
* Скачивает все содержимое в `datastore1` целевом объекте вычислений перед выполнением сценария обучения `train.py`
* Загружает папку `'./foo'` в `datastore2` целевой объект вычислений перед `train.py` выполнением
* Передает файл `'./bar.pkl'` из целевого объекта `datastore3` вычислений в после выполнения скрипта

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
### <a name="compute-and-datastore-matrix"></a>Матрица вычислений и хранилища данных

В настоящее время хранилища данных поддерживают хранение сведений о подключении к службам хранилища, указанным в следующей матрице. Эта матрица отображает доступные функциональные возможности доступа к данным для различных целевых объектов вычислений и сценариев хранилища данных. Дополнительные сведения о [целевых показателях вычислений для машинное обучение Azure](how-to-set-up-training-targets.md#compute-targets-for-training).

|Вычисления|[азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[азуредаталакедатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [Азурепостгресклдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [Азуресклдатабаседатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Локальные|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Н/Д         |Н/Д                                                                         |
| Вычислительная среда Машинного обучения Azure; |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [конвейеры ML&nbsp;](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [конвейеры ML&nbsp;](concept-ml-pipelines.md)|Н/Д         |Н/Д                                                                         |
| ВМ               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| Передача данных                  |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)                                               |Н/Д                                           |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)            |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)                                              |Н/Д                                           |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)             |Н/Д                                                                         |
| Пакетная служба Azure                    |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)                                               |Н/Д                                           |Н/Д         |Н/Д                                                                         |
| Azure Data Lake Analytics       |Н/Д                                           |Н/Д                                           |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)             |Н/Д                                                                         |

> [!NOTE]
> Возможны ситуации, в которых очень итеративные процессы обработки больших данных выполняются быстрее `as_download()` с помощью `as_mount()`вместо. это можно проверить в экспериментах.

## <a name="access-data-during-scoring"></a>Доступ к данным во время оценки

Служба Машинное обучение Azure предоставляет несколько способов использования моделей для оценки. Некоторые из этих методов не предоставляют доступ к хранилищам данных. Используйте следующую таблицу, чтобы понять, какие методы позволяют получать доступ к хранилищам данных во время оценки.

| Метод | Доступ к хранилищу данных | Описание |
| ----- | :-----: | ----- |
| [Прогнозирование пакетной службы](how-to-run-batch-predictions.md) | ✔ | Асинхронное создание прогнозов на больших объемах данных. |
| [Веб-служба](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей в качестве веб-службы. |
| [Модуль IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей для IoT Edge устройств. |

В ситуациях, когда пакет SDK не предоставляет доступ к хранилищам данных, вы можете создать пользовательский код, используя соответствующий пакет SDK Azure для доступа к данным. Например, с помощью [пакета SDK службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python) можно получить доступ к данным, хранящимся в больших двоичных объектах.


## <a name="next-steps"></a>Следующие шаги

* [Обучение модели](how-to-train-ml-models.md)

* [Развертывание модели](how-to-deploy-and-where.md)
