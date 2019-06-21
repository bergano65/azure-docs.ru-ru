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
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 638d7bfb0e396874415c1055c4b707a65caffa4e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269298"
---
# <a name="access-data-from-your-datastores"></a>Доступ к данным из вашего хранилища данных

 В службе машинного обучения Azure хранилища данных вычисления механизмы независимо от расположения для доступа к хранилищу без внесения изменений в исходный код. Написать код обучения для пути как параметр или передавать хранилище данных непосредственно в оценщика, рабочие процессы машинного обучения Azure убедитесь, хранилище данных источников доступны и станут доступны в качестве контекста вычисления.

В этом практическом руководстве показаны примеры следующие задачи:
* [Выберите хранилище данных](#access)
* [Получение данных](#get)
* [Отправка и загрузка данных для хранилища данных](#up-and-down)
* [Хранилище данных доступа во время обучения](#train)

## <a name="prerequisites"></a>Технические условия

Для использования хранилищ данных необходима [рабочая область](concept-workspace.md).

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

 Каждая рабочая область имеет зарегистрировано, хранилище данных по умолчанию, который можно использовать прямо сейчас.

Чтобы получить хранилище данных рабочей области по умолчанию:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Регистрация собственного хранилища данных с рабочей областью

Если у вас уже есть существующая служба хранилища Azure, ее можно зарегистрировать как хранилище данных для рабочей области. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Мы рекомендуем большого двоичного объекта хранилища и больших двоичных объектов хранилища данных. Хранилище класса standard и premium доступны для больших двоичных объектов. Несмотря на то что дороже, мы советуем использовать хранилище уровня "премиум" из-за более высокую скорость пропускной способности, которые позволяют повысить скорость обучения выполняется, particlularly при обучении с большим набором данных. См. в разделе [калькулятором цен Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) для учетной записи хранения сведения о стоимости.

>[!NOTE]
> Служба машинного обучения Azure поддерживает другие типы хранилища данных, который можно использовать для конкретных сценариев. Например если вам нужно обучить с помощью данных, хранящихся в базе данных, можно использовать AzureSQLDatabaseDatastore или AzurePostgreSqlDatastore. См. в разделе [эта таблица](#matrix) для типы доступных хранилищ данных.

#### <a name="register-your-datastore"></a>Регистрация в хранилище данных
Метод регистрации находятся на [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) , что позволяет форме register_azure_ *.

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

+ Для **хранилище данных общей папки Azure файловой**, использовать [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Пример: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
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

### <a name="upload"></a>Отправьте

 Каталог или отдельные файлы передайте в хранилище данных с помощью пакета SDK для Python.

Чтобы отправить каталог в хранилище данных `ds`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

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

После внесения в хранилище данных на целевой объект вычислений обучения, вы сможете открыть его во время учебных запусков (например, данные обучения или проверки), просто передав путь к нему, как параметр в скрипте обучения.

В следующей таблице перечислены [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) методов, которые сообщают целевого объекта вычислений, как использовать хранилище данных во время выполнения.

способ|Метод|Описание|
----|-----|--------
Подключение| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Используйте для подключения к хранилищу данных в целевой объект вычислений.
Download (Скачать)|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Использовать для загрузки содержимого из хранилища данных в расположении, заданном параметром `path_on_compute`. <br> Для контекста обучающих запусках данная загрузка происходит до запуска.
Отправьте|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Передайте файл из местоположения, указанного по `path_on_compute` для хранилища данных. <br> Для контекста обучающих запусках эта отправка после запуска.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Использовать хранилище данных, чтобы ссылаться на определенные папки или файла в хранилище данных и сделать его доступным в целевой объект вычислений, [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) функции.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Любой `ds` или `ds.path` объект разрешается в имя переменной среды формата `"$AZUREML_DATAREFERENCE_XXXX"` , значение которого представляет путь подключения и загрузки на целевой объект вычислений. Путь к хранилищу данных на целевой объект вычислений не может быть таким же, как путь выполнения для сценария обучения.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Матрица обучения вычислительные ресурсы и хранилище данных

В следующем матрице будут отображены функции повсеместного доступа доступных данных для обучения различные целевые объекты вычисления и сценариев хранилища данных. Дополнительные сведения о [обучения целевых объектов вычислений для машинного обучения Azure](how-to-set-up-training-targets.md#compute-targets-for-training).

|Среда выполнения приложений|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Н/Д         |Н/Д                                                                         |
| Вычислительная среда Машинного обучения Azure; |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;конвейеров](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;конвейеров](concept-ml-pipelines.md)|Н/Д         |Н/Д                                                                         |
| Виртуальные машины               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| Передача данных                  |[ML&nbsp;конвейеров](concept-ml-pipelines.md)                                               |Н/Д                                           |[ML&nbsp;конвейеров](concept-ml-pipelines.md)            |[ML&nbsp;конвейеров](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;конвейеров](concept-ml-pipelines.md)                                              |Н/Д                                           |[ML&nbsp;конвейеров](concept-ml-pipelines.md)             |Н/Д                                                                         |
| Пакетная служба Azure                    |[ML&nbsp;конвейеров](concept-ml-pipelines.md)                                               |Н/Д                                           |Н/Д         |Н/Д                                                                         |
| Azure DataLake-Analytics       |Н/Д                                           |Н/Д                                           |[ML&nbsp;конвейеров](concept-ml-pipelines.md)             |Н/Д                                                                         |

> [!NOTE]
> Возможны сценарии, в которых высокий уровень цикличности, процессы для больших объемов данных выполняются быстрее с помощью `as_download()` вместо `as_mount()`; это может быть проверен на основе экспериментов.

### <a name="examples"></a>Примеры 

В следующих примерах кода относятся к [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс для доступа к в хранилище данных во время обучения.

Этот код создает оценщик с помощью сценария обучения `train.py`, из указанного исходного каталога, используя параметры, определенные в `script_params`, все в обучающем указанного целевого объекта вычислений.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Также можно передать в список хранилищ данных в конструктор оценщика `inputs` параметр для подключения или копирования из вашего хранилища данных. Данный пример кода:
* Загружает все содержимое в хранилище данных `ds1` для целевого объекта вычислений перед сценарий обучения `train.py` выполняется
* Папка скачиваний `'./foo'` в хранилище данных `ds2` для целевого объекта вычислений перед `train.py` выполняется
* Передает файл `'./bar.pkl'` из целевого объекта вычислений до хранилища данных `ds3` после выполнения скрипта

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Дальнейшие действия

* [Обучение модели](how-to-train-ml-models.md)

* [Развертывание модели](how-to-deploy-and-where.md)
