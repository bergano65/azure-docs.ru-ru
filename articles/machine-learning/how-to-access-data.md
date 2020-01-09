---
title: Доступ к данным в службах хранилища Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать хранилища данных для безопасного подключения к службам хранилища Azure во время обучения с помощью Машинное обучение Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540949"
---
# <a name="access-data-in-azure-storage-services"></a>Доступ к данным в службах хранилища Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как легко получить доступ к данным в службах хранилища Azure с помощью Машинное обучение Azure хранилищ данных. Хранилища данных используются для хранения сведений о подключении, таких как идентификатор подписки и авторизация маркеров. При использовании хранилищ данных доступ к хранилищу можно получить, не имея в скриптах сведения о соединении с жестким кодом. 

Хранилища данных можно создавать из [этих решений службы хранилища Azure](#matrix). Для неподдерживаемых решений для хранения данных и для сохранения затрат на исходящие данные во время экспериментов машинного обучения рекомендуется [переместить данные](#move) в поддерживаемые решения службы хранилища Azure. 

## <a name="prerequisites"></a>Технические условия
Что вам понадобится:
- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Попробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree).

- Учетная запись хранения Azure с [контейнером больших двоичных объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) или [общей папкой Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Машинное обучение Azure пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или доступ к [машинное обучение Azure Studio](https://ml.azure.com/).

- Рабочая область машинного обучения Azure.
  
  [Создайте машинное обучение Azure рабочую область](how-to-manage-workspace.md) или используйте существующую с помощью пакета SDK для Python:

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Создание и регистрация хранилищ данных

При регистрации решения службы хранилища Azure в качестве хранилища данных автоматически создается хранилище данных в определенной рабочей области. Хранилища данных можно создавать и регистрировать в рабочей области с помощью пакета SDK для Python или Машинное обучение Azure Studio.

### <a name="python-sdk"></a>Пакет Python SDK

Все методы Register относятся к классу [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) и имеют форму `register_azure_*`.

Сведения, необходимые для заполнения метода `register()`, можно найти с помощью [портал Azure](https://portal.azure.com).

1. Выберите **учетные записи хранения** на левой панели и выберите учетную запись хранения, которую требуется зарегистрировать. 
2. Сведения, такие как имя учетной записи, контейнер и имя общей папки, можно найти на странице **Обзор** . Чтобы получить сведения о проверке подлинности, например ключ учетной записи или маркер SAS, перейдите в **раздел ключи доступа** на панели **Параметры** . 

> [!IMPORTANT]
> Если ваша учетная запись хранения находится в виртуальной сети, поддерживается только создание хранилища данных BLOB-объектов Azure. Чтобы предоставить рабочей области доступ к вашей учетной записи хранения, задайте для параметра `grant_workspace_access` значение `True`.

В следующих примерах показано, как зарегистрировать контейнер больших двоичных объектов Azure, файловый ресурс Azure и данные SQL Azure в качестве хранилища данных.

#### <a name="blob-container"></a>Контейнер BLOB-объектов

Чтобы зарегистрировать контейнер больших двоичных объектов Azure в качестве хранилища данных, используйте [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Следующий код создает и регистрирует хранилище данных `blob_datastore_name` в рабочей области `ws`. Это хранилище данных обращается к контейнеру больших двоичных объектов `my-container-name` в учетной записи хранения `my-account-name`, используя указанный ключ учетной записи.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Файловый ресурс

Чтобы зарегистрировать общую папку Azure в качестве хранилища данных, используйте [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Следующий код создает и регистрирует хранилище данных `file_datastore_name` в рабочей области `ws`. Это хранилище данных обращается к `my-fileshare-name` общей папке в учетной записи хранения `my-account-name`, используя указанный ключ учетной записи.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>SQL-данные

Для хранилища данных SQL Azure используйте [register_azure_sql_database ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) , чтобы зарегистрировать хранилище учетных записей, подключенное к базе данных SQL Azure, с разрешениями проверки подлинности SQL или субъекта-службы. 

Для регистрации с помощью проверки подлинности SQL:

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

Для регистрации через субъект-службу выполните следующие действия.

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Мы рекомендуем использовать контейнер больших двоичных объектов Azure. Хранилище уровня "Стандартный" и "Премиум" доступны для больших двоичных объектов. Хотя хранилище класса Premium является более дорогим, его быстрые скорости пропускной способности могут повысить скорость выполнения обучения, особенно при обучении большого набора данных. Сведения о стоимости учетных записей хранения см. в [калькуляторе цен Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

### <a name="azure-machine-learning-studio"></a>Студия машинного обучения Azure. 

Создайте хранилище данных, выполнив несколько шагов в Машинное обучение Azure Studio:

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com/).
1. Выберите **хранилища данных** на левой панели в разделе **Управление**.
1. Выберите **+ создать хранилище данных**.
1. Заполните форму для нового хранилища данных. Форма интеллектуального обновления обновляется на основе выбора типа службы хранилища Azure и типа проверки подлинности.
  
Сведения, необходимые для заполнения формы, можно найти на [портал Azure](https://portal.azure.com). Выберите **учетные записи хранения** на левой панели и выберите учетную запись хранения, которую требуется зарегистрировать. На странице **Обзор** содержатся такие сведения, как имя учетной записи, контейнер и имя общего файлового ресурса. Для элементов проверки подлинности, таких как ключ учетной записи или маркер SAS, перейдите к **разделу ключи учетной записи** на панели **Параметры** .

В следующем примере показано, как выглядит форма при создании хранилища BLOB-объектов Azure. 
    
![Форма для нового хранилища данных](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Получение хранилищ данных из рабочей области

Чтобы получить конкретное хранилище данных, зарегистрированное в текущей рабочей области, используйте статический метод [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) класса `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Чтобы получить список хранилищ данных, зарегистрированных в определенной рабочей области, можно использовать свойство [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) объекта рабочей области.

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

При создании рабочей области контейнер больших двоичных объектов Azure и файловый ресурс Azure автоматически регистрируются в рабочей области. Они называются `workspaceblobstore` и `workspacefilestore`соответственно. Они хранят сведения о подключении для контейнера больших двоичных объектов и общей папки, подготовленные в учетной записи хранения, подключенной к рабочей области. Контейнер `workspaceblobstore` задается в качестве хранилища данных по умолчанию.

Чтобы получить хранилище данных по умолчанию для рабочей области, используйте следующую строку:

```Python
datastore = ws.get_default_datastore()
```

Чтобы определить другое хранилище данных по умолчанию для текущей рабочей области, используйте метод [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) для объекта рабочей области:

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Отправка и загрузка данных

Методы [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) и [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) , описанные в следующих примерах, относятся к классам [азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) и [азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) , а также работают одинаково.

### <a name="upload"></a>Отправка

Отправьте либо каталог, либо отдельные файлы в хранилище данных с помощью пакета SDK для Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Параметр `target_path` указывает расположение в общей папке (или контейнере больших двоичных объектов) для передачи. По умолчанию используется `None`, поэтому данные передаются в корневую папку. Если `overwrite=True`, любые существующие данные в `target_path` перезаписываются.

Можно также передать список отдельных файлов в хранилище данных с помощью метода `upload_files()`.

### <a name="download"></a>Загрузить

Скачайте данные из хранилища данных в локальную файловую систему:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Параметр `target_path` — это расположение локального каталога, в который нужно загрузить данные. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` `None`, будут скачаны все содержимое общей папки (или контейнера больших двоичных объектов).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Доступ к данным во время обучения

> [!IMPORTANT]
> Теперь мы советуем использовать [машинное обучение Azure наборы](how-to-create-register-datasets.md) данных для доступа к данным в процессе обучения. Наборы данных предоставляют функции, которые загружают табличные данные в таблицу данных Pandas или Spark. Наборы данных также предоставляют возможность скачивания или подключения файлов любого формата из хранилища BLOB-объектов Azure, файлов Azure, Azure Data Lake Storage 1-го поколения, Azure Data Lake Storage 2-го поколения, базы данных SQL Azure и Azure для PostgreSQL. [Узнайте больше о том, как обучить наборы данных](how-to-train-with-datasets.md).

В следующей таблице перечислены методы, которые сообщают целевому объекту вычислений, как использовать хранилища данных во время выполнения: 

Проезд|Метод|Description|
----|-----|--------
Подключение| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Используйте для подключения хранилища данных к целевому объекту вычислений. При подключении хранилища данных все файлы хранилища данных становятся доступными для целевого объекта вычислений.
Загрузить|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Используйте для загрузки содержимого хранилища данных в расположение, указанное `path_on_compute`. <br><br> Этот процесс загрузки выполняется перед запуском.
Отправка|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Используйте для передачи файла из расположения, указанного параметром `path_on_compute`, в хранилище данных. <br><br> Эта отправка происходит после запуска.

Чтобы создать ссылку на определенную папку или файл в хранилище данных и сделать его доступным на целевом объекте вычислений, используйте метод [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) хранилища данных:

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Любой указанный `datastore` или `datastore.path` объект разрешается в имя переменной среды в формате `"$AZUREML_DATAREFERENCE_XXXX"`. Значение этого имени представляет путь подключения или загрузки в целевом объекте вычислений. Путь к хранилищу данных в целевом объекте вычислений может отличаться от пути выполнения для обучающего скрипта.

### <a name="examples"></a>Примеры 

Для доступа к данным во время обучения рекомендуется использовать класс [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) . 

`script_params` переменная — это словарь, содержащий параметры для `entry_script`. Используйте его для передачи хранилища данных и описания того, как данные становятся доступными в целевом объекте вычислений. Ознакомьтесь с [комплексным руководством](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

Можно также передать список хранилищ данных в параметр `inputs` конструктора `Estimator`, чтобы подключать или копировать данные в хранилище. Этот пример кода:
* Скачивает все содержимое в `datastore1` целевому объекту вычислений перед выполнением скрипта обучения `train.py`.
* Загружает `'./foo'` папку в `datastore2` целевой объект вычислений перед запуском `train.py`.
* Передает файл `'./bar.pkl'` из целевого объекта вычислений в `datastore3` после выполнения скрипта.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Если вы предпочитаете использовать объект `RunConfig` для обучения, необходимо настроить объект [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) . 

В следующем коде показано, как работать с объектом `DataReference` в конвейере оценки. Полный пример см. в [этой записной книжке](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb).

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Матрица вычислений и хранилища данных

В настоящее время хранилища данных поддерживают хранение сведений о подключении к службам хранилища, указанным в следующей матрице. Эта матрица отображает доступные функции доступа к данным для различных целевых объектов вычислений и сценариев хранилища данных. Дополнительные [сведения о целевых показателях вычислений для машинное обучение Azure](how-to-set-up-training-targets.md#compute-targets-for-training).

|Среда выполнения приложений|[азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[азуредаталакедатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [азурепостгресклдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [азуресклдатабаседатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Местного уровня|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Н/Д         |Н/Д                                                                         |
| Вычислительная среда Машинного обучения Azure |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [машинное обучениеные конвейеры](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [машинное обучениеные конвейеры](concept-ml-pipelines.md)|Н/Д         |Н/Д                                                                         |
| Виртуальные машины               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| Azure HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| Передача данных                  |[Конвейеры Машинное обучение](concept-ml-pipelines.md)                                               |Н/Д                                           |[Конвейеры Машинное обучение](concept-ml-pipelines.md)            |[Конвейеры Машинное обучение](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Конвейеры Машинное обучение](concept-ml-pipelines.md)                                              |Н/Д                                           |[Конвейеры Машинное обучение](concept-ml-pipelines.md)             |Н/Д                                                                         |
| Пакетная служба Azure                    |[Конвейеры Машинное обучение](concept-ml-pipelines.md)                                               |Н/Д                                           |Н/Д         |Н/Д                                                                         |
| Аналитика озера данных Azure       |Н/Д                                           |Н/Д                                           |[Конвейеры Машинное обучение](concept-ml-pipelines.md)             |Н/Д                                                                         |

> [!NOTE]
> Возможны ситуации, в которых очень итеративные процессы обработки больших данных выполняются быстрее с использованием `as_download()` вместо `as_mount()`. Это эксперимент можно проверить.

### <a name="accessing-source-code-during-training"></a>Доступ к исходному коду во время обучения

В хранилище BLOB-объектов Azure более высокая скорость пропускной способности, чем общая папка Azure, и масштабирование до большого числа заданий, запускаемых параллельно. По этой причине рекомендуется настроить запуски для использования хранилища BLOB-объектов для передачи файлов исходного кода.

В следующем примере кода указывается в конфигурации запуска, которую хранилище данных BLOB-объектов будет использовать для передачи исходного кода:

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Доступ к данным во время оценки

Машинное обучение Azure предоставляет несколько способов использования моделей для оценки. Некоторые из этих методов не предоставляют доступ к хранилищам данных. Используйте следующую таблицу, чтобы понять, какие методы позволяют получать доступ к хранилищам данных во время оценки.

| Метод | Доступ к хранилищу данных | Description |
| ----- | :-----: | ----- |
| [Прогнозирование пакетной службы](how-to-run-batch-predictions.md) | ✔ | Асинхронное создание прогнозов на больших объемах данных. |
| [Веб-служба](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей в качестве веб-службы. |
| [Модуль Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей на IoT Edge устройствах. |

В ситуациях, когда пакет SDK не предоставляет доступ к хранилищам данных, вы можете создать пользовательский код с помощью соответствующего пакета SDK Azure для доступа к данным. Например, [пакет SDK для службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python) — это клиентская библиотека, которую можно использовать для доступа к данным, хранящимся в больших двоичных объектах или файлах.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Перемещение данных в поддерживаемые решения службы хранилища Azure

Машинное обучение Azure поддерживает доступ к данным из хранилища BLOB-объектов Azure, файлов Azure, Azure Data Lake Storage 1-го поколения, Azure Data Lake Storage 2-го поколения, базы данных SQL Azure и базы данных Azure для PostgreSQL. Если вы используете неподдерживаемое хранилище, мы рекомендуем перенести данные в поддерживаемые решения службы хранилища Azure с помощью фабрики данных Azure. Перемещение данных в поддерживаемое хранилище поможет сохранить затраты на исходящие данные во время экспериментов машинного обучения. 

Фабрика данных Azure обеспечивает эффективную и устойчивую пересылку данных с более чем 80 предварительно подготовленных соединителей без дополнительных затрат. К этим соединителям относятся службы данных Azure, локальные источники данных, Amazon S3 и Redshift и Google BigQuery. Следуйте пошаговым [инструкциям, чтобы переместить данные с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Дальнейшие действия

* [Обучение модели](how-to-train-ml-models.md)
* [Развертывание модели](how-to-deploy-and-where.md)
