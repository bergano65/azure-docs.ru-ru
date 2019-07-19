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
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319325"
---
# <a name="access-data-from-your-datastores"></a>Доступ к данным из хранилищ данных

 В службах Машинное обучение Azure хранилища данных — это независимые от расположения вычислений механизмы для доступа к хранилищу без необходимости внесения изменений в исходный код. Независимо от того, пишете ли вы код обучения для получения пути в качестве параметра или предоставляете хранилище данных непосредственно для средства оценки, Машинное обучение Azure рабочие процессы, чтобы обеспечить доступ к расположениям хранилища данных и сделать их доступными для контекста вычислений.

В этом пошаговом окне приведены примеры следующих задач.
* [Выбор хранилища данных](#access)
* [Получение данных](#get)
* [Отправка и скачивание данных в хранилищах](#up-and-down)
* [Доступ к хранилищу данных во время обучения](#train)

## <a name="prerequisites"></a>предварительные требования

Для использования хранилищ данных необходима [рабочая область](concept-workspace.md).

Необходимо начать с [создания новой рабочей области](setup-create-workspace.md#sdk) или получить существующую.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Выбор хранилища данных

Вы можете использовать хранилище данных по умолчанию или свои собственные.

### <a name="use-the-default-datastore-in-your-workspace"></a>Использование хранилища данных по умолчанию в рабочей области

 Для каждой рабочей области имеется зарегистрированное хранилище данных по умолчанию, которое можно использовать сразу.

Чтобы получить хранилище данных рабочей области по умолчанию:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Регистрация собственного хранилища данных в рабочей области

Если у вас уже есть существующая служба хранилища Azure, ее можно зарегистрировать как хранилище данных для рабочей области. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Мы рекомендуем использовать хранилище BLOB-объектов и хранилища данных BLOB-объектов. Хранилище уровня "Стандартный" и "Премиум" доступны для больших двоичных объектов. Хотя это и более дорого, мы рекомендуем хранилище уровня "Премиум" из-за более высоких скоростей пропускной способности, которые могут повысить скорость обучения, особенно при обучении большого набора данных. Сведения о стоимости учетной записи хранения см. в [калькуляторе цен Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

>[!NOTE]
> Служба Машинное обучение Azure поддерживает другие типы хранилищ данных, которые могут быть полезны в конкретных сценариях. Например, если необходимо выполнить обучение с использованием данных, хранящихся в базе данных, можно использовать Азуресклдатабаседатасторе или Азурепостгресклдатасторе. В [этой таблице](#matrix) приведены доступные типы хранилищ данных.

#### <a name="register-your-datastore"></a>Регистрация хранилища данных
Все методы Register относятся [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) к классу и имеют форму register_azure_ *.

В следующих примерах показано, как зарегистрировать контейнер больших двоичных объектов Azure или файловый ресурс Azure в качестве хранилища данных.

+ Для **хранилища данных контейнера больших двоичных объектов Azure**используйте[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Для **хранилища данных файлового ресурса Azure**используйте [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Пример: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Поиск & определение хранилищ данных

Чтобы получить указанное хранилище данных, зарегистрированное в текущей рабочей области, [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) используйте:

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

Чтобы определить другое хранилище данных по умолчанию для текущей рабочей области, [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)используйте:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Отправка & скачивание данных
Методы [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) и [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) , описанные в следующих примерах, относятся к классам [азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) и [азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) и работают одинаково.

### <a name="upload"></a>Передать

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

### <a name="download"></a>Загрузить
Аналогичным образом можно загрузить данные из хранилища данных в локальную файловую систему.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` — это расположение локального каталога для загрузки данных. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` имеет значение `None`, будет загружено все содержимое общей папки (или контейнера больших двоичных объектов).

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Доступ к хранилищам данных во время обучения

После того как хранилище данных будет доступно в целевом объекте для обучения, вы сможете получить к нему доступ во время обучения (например, данные для обучения или проверки), просто передав путь к нему в качестве параметра в обучающем скрипте.

В следующей таблице перечислены [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) методы, которые сообщают целевому объекту вычислений, как использовать хранилище данных во время выполнения.

Одностороннюю|Метод|Описание|
----|-----|--------
Подключение| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Используйте для подключения хранилища данных к целевому объекту вычислений.
Загрузить|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Используйте для загрузки содержимого хранилища данных в расположение, `path_on_compute`указанное в. <br> Для обучающего контекста этот процесс загрузки выполняется перед запуском.
Передать|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Используйте для передачи файла из расположения, указанного `path_on_compute` в хранилище данных. <br> В контексте обучающего выполнения эта передача выполняется после выполнения.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Чтобы создать ссылку на определенную папку или файл в хранилище данных и сделать его доступным в целевом объекте вычислений, используйте [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) функцию хранилища данных.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Любой `ds` объект `ds.path` или разрешается в имя переменной среды формата `"$AZUREML_DATAREFERENCE_XXXX"` , значение которого представляет путь подключения или загрузки в целевом вычислении. Путь к хранилищу данных в целевом вычислении может отличаться от пути выполнения для обучающего скрипта.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Матрица обучающих вычислений и хранилища данных

В следующей матрице показаны доступные функциональные возможности доступа к данным для различных целевых объектов вычислений для обучения и сценариев хранилища данных. Узнайте больше о [целевых показателях обучения для машинное обучение Azure](how-to-set-up-training-targets.md#compute-targets-for-training).

|Службы вычислений|[азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[азуредаталакедатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [Азурепостгресклдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [Азуресклдатабаседатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Н/Д         |Н/Д                                                                         |
| Вычислительная среда Машинного обучения Azure; |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [конвейеры ML&nbsp;](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [конвейеры ML&nbsp;](concept-ml-pipelines.md)|Н/Д         |Н/Д                                                                         |
| Виртуальные машины               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| Передача данных                  |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)                                               |Н/Д                                           |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)            |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)                                              |Н/Д                                           |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)             |Н/Д                                                                         |
| Пакетная служба Azure                    |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)                                               |Н/Д                                           |Н/Д         |Н/Д                                                                         |
| Azure Data Lake Analytics       |Н/Д                                           |Н/Д                                           |[Конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)             |Н/Д                                                                         |

> [!NOTE]
> Возможны ситуации, в которых очень итеративные процессы обработки больших данных выполняются быстрее `as_download()` с помощью `as_mount()`вместо. это можно проверить в экспериментах.

### <a name="examples"></a>Примеры 

Следующие примеры кода относятся к [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) классу для доступа к хранилищу данных во время обучения.

Этот код создает средство оценки с помощью сценария обучения, `train.py`из указанного исходного каталога с использованием параметров, определенных в `script_params`, для указанного целевого объекта для обучения.

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

Также можно передать список хранилищ данных в параметр конструктора `inputs` средства оценки для подключения или копирования в хранилища данных. Этот пример кода:
* Скачивает все содержимое хранилища данных `ds1` в целевом объекте вычислений перед выполнением сценария обучения `train.py`
* Скачивает папку `'./foo'` в хранилище данных `ds2` в целевом объекте вычислений перед `train.py` выполнением
* Передает файл `'./bar.pkl'` из целевой платформы вычислений в хранилище данных `ds3` после выполнения скрипта

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>Доступ к хранилищам данных во время оценки

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
