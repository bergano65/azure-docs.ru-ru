---
title: Подключение к службам хранения данных Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать хранилища данных для безопасного подключения к службам хранения данных Azure во время обучения с помощью Машинного обучения Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ca892b5f360f523ee2b5ff875dfb0707136a5ab5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383439"
---
# <a name="connect-to-azure-storage-services"></a>Подключение к службам хранения данных Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье узнайте, как подключиться к службам хранения данных Azure через магазины данных Azure Machine Learning. Магазины данных хранят информацию о подключении, например идентификатор подписки и авторизацию маркеров в [вашем ключевом](https://azure.microsoft.com/services/key-vault/) хранилище, связанную с рабочим пространством, так что вы можете получить безопасный доступ к хранилищу без жесткого кодирования их в своих скриптов.

Вы можете создавать хранилища данных из [этих решений для хранения данных Azure.](#matrix) Для неподдерживаемых решений для хранения данных и для экономии затрат на выемку данных во время экспериментов машинного обучения мы рекомендуем [перемещать данные](#move) в поддерживаемые решения для хранения данных Azure. 

## <a name="prerequisites"></a>Предварительные требования

Что вам понадобится:
- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию машинного обучения Azure.](https://aka.ms/AMLFree)

- Учетная запись хранения Azure с [контейнером Azure blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) или [совместной копией файлов Azure.](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)

- [SDK Для Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или доступ к [студии машинного обучения Azure.](https://ml.azure.com/)

- Рабочая область машинного обучения Azure.
  
  Либо [создайте рабочее пространство Для машин Azure,](how-to-manage-workspace.md) либо используйте существующее через Python SDK. Импортируйте `Workspace` `Datastore` и класс, и загружайте информацию о подписке из файла `config.json` с помощью функции. `from_config()` Это выглядит для файла JSON в текущем каталоге по умолчанию, но вы `from_config(path="your/file/path")`также можете указать параметр пути, чтобы указать на файл с помощью .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Поддерживаемые типы служб хранения данных

В настоящее время хранилища данных поддерживают хранение информации о подключении к службам хранения, перечисленным в следующей матрице.

| Тип&nbsp;хранения | Тип&nbsp;аутентификации | [Студия&nbsp;машинного&nbsp;обучения Azure](https://ml.azure.com/) | [Лазурное машинное&nbsp;&nbsp;обучение&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Лазурное машинное&nbsp;&nbsp;обучение CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Хранение&nbsp;Azure&nbsp;Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Ключ учетной записи <br> Токен SAS | ✓ | ✓ | ✓ |✓
[Совместное&nbsp;&nbsp;с файлом Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Ключ учетной записи <br> Токен SAS | ✓ | ✓ | ✓ |✓
[&nbsp;Лазурное&nbsp;хранилище&nbsp;озер данных Gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Субъект-служба| ✓ | ✓ | ✓ |✓
[&nbsp;Лазурное&nbsp;хранилище&nbsp;озер данных Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Субъект-служба| ✓ | ✓ | ✓ |✓
[База&nbsp;данных&nbsp;Azure S'L](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Проверка подлинности SQL <br>Субъект-служба| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreS'L](https://docs.microsoft.com/azure/postgresql/overview) | Проверка подлинности SQL| ✓ | ✓ | ✓ |✓
[База&nbsp;&nbsp;данных&nbsp;Azure для MyS'L](https://docs.microsoft.com/azure/mysql/overview) | Проверка подлинности SQL|  | ✓* | ✓* |✓*
[Файловая&nbsp;&nbsp;система Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| без аутентификации; | | ✓** | ✓ ** |✓** 

«MyS'L поддерживается только для конвейера [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
Databricks поддерживается только для конвейера [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Мы рекомендуем создать хранилище данных для [контейнера Azure Blob.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Как стандартное, так и премиум-хранилище доступно для капли. Хотя премиум-хранилище является более дорогим, его более быстрая скорость пропускной работы может повысить скорость ваших учебных запусков, особенно если вы тренируетесь против большого набора данных. Информацию о стоимости учетных записей хранилища можно узнать на ч. м. [Калькулятор цен Azure.](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)

[Azure Data Lake Storage 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) построен на базе хранилища Azure Blob и предназначен для аналитики больших данных на предприятиях. Основная часть Data Lake Storage 2-го поколения — это добавление [иерархического пространства имен](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) в хранилище BLOB-объектов. Иерархическое пространство имен позволяет упорядочивать объекты и файлы в иерархии каталогов для эффективного доступа к данным.

При создании рабочего пространства контейнер Azure blob и доля файлов Azure автоматически регистрируются в рабочем пространстве. Они названы `workspaceblobstore` `workspacefilestore`и, соответственно. `workspaceblobstore`используется для хранения артефактов рабочего пространства и журналов экспериментов машинного обучения. `workspacefilestore`используется для хранения ноутбуков и R скриптов, авторизованных через [вычисляемый экземпляр.](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files) Контейнер `workspaceblobstore` устанавливается как хранилище данных по умолчанию.

> [!IMPORTANT]
> Дизайнер машинного обучения Azure (предварительный просмотр) создаст хранилище данных под названием **azureml_globaldatasets** автоматически при открытии образца на главной странице дизайнера. Этот хранилище данных содержит только наборы выборочных данных. Пожалуйста, **не** используйте этот хранилище данных для доступа к конфиденциальным данным.
> ![Автоматически созданный магазин данных для наборов данных выборки конструкторов](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Создание и регистрация хранилив данных

При регистрации решения для хранения данных Azure в качестве хранилища данных вы автоматически создаете и регистрируете хранилище данных в определенном рабочем пространстве. Вы можете создавать и регистрировать хранилища данных в рабочее пространство с помощью студии Python SDK или Azure Machine Learning.

>[!IMPORTANT]
> В рамках первоначального процесса создания и регистрации хранилища Azure Machine Learning подтверждает, что базовая служба хранения существует и что пользователь предоставил основной (имя пользователя, основной сервис или токен SAS) имеет доступ к этому хранилищу. Однако для хранилищ данных Azure Data Lake, Gen 1 и 2, эта проверка происходит позже, когда методы доступа к данным нравятся [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) или [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) называются. 
<br><br>
После создания хранилища данных эта проверка выполняется только для методов, требующих доступа к базовому контейнеру хранения, а **не** каждый раз при извлечении объектов хранилища данных. Например, проверка происходит, если вы хотите загрузить файлы из магазина данных; но если вы просто хотите изменить свой магазин данных по умолчанию, то проверки не происходит.

### <a name="python-sdk"></a>Пакет SDK для Python

Все методы регистра [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) находятся на `register_azure_*`классе и имеют форму.

Информацию, необходимую для заполнения `register()` метода, можно найти на [портале Azure.](https://portal.azure.com)
Выберите **учетные записи хранения** на левом стеле и выберите учетную запись хранения, которую вы хотите зарегистрировать. Страница **«Обзор»** содержит такие сведения, как имя учетной записи, контейнер и имя файла. 

* Для элементов проверки подлинности, таких как ключ учетной записи или маркер SAS, перейдите к **клавишам доступа** на панели **настроек.** 

* Для основных элементов обслуживания, таких как идентификатор клиента и идентификатор клиента, перейдите на **регистрацию приложений** и выберите, какое приложение вы хотите использовать. Соответствующая страница **Обзора** будет содержать эти элементы.

> [!IMPORTANT]
> Если ваша учетная запись находится в виртуальной сети, поддерживается только создание Blob, File share, ADLS Gen 1 и ADLS Gen 2 datastores **через SDK.** Чтобы предоставить вашему рабочему пространству `grant_workspace_access` доступ `True`к учетной записи хранилища, установите параметр для .

Ниже приведены следующие примеры, как зарегистрировать контейнер Azure blob, раздел файлов Azure и хранилище данных Azure Data Lake Generation 2 в качестве хранилища данных. Для других служб хранения, пожалуйста, ознакомьтесь с [справочной документацией `register_azure_*` для применимых методов.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)

#### <a name="blob-container"></a>Контейнер BLOB-объектов

Чтобы зарегистрировать контейнер Azure blob в [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)качестве хранилища данных, используйте:

Следующий код создает и `blob_datastore_name` регистрирует хранилище `ws` данных в рабочее пространство. Этот хранилище данных `my-container-name` получает доступ `my-account-name` к контейнеру blob на учетной записи хранилища, используя предоставленный ключ доступа к учетной записи.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Общая папка

Чтобы зарегистрировать общий доступ к файлам Azure в качестве хранилища данных, используйте: [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) 

Следующий код создает и `file_datastore_name` регистрирует хранилище `ws` данных в рабочее пространство. Этот хранилище данных `my-fileshare-name` получает доступ `my-account-name` к доле файла в учетной записи хранилища, используя предоставленный ключ доступа к учетной записи.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

#### <a name="azure-data-lake-storage-generation-2"></a>Поколение 2 системы хранения данных Azure Data Lake

Для хранения данных Azure Data Lake Storage Generation 2 (ADLS Gen 2) используйте [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) для регистрации хранилища данных, подключенного к хранилищу Azure DataLake Gen 2 с [разрешениями службы.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Для того, чтобы использовать ваш основной сервис, вам необходимо [зарегистрировать приложение](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) и предоставить директору службы правильный доступ к данным. Подробнее о [элементе управления доступом, настроенном для ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Следующий код создает и `adlsgen2_datastore_name` регистрирует хранилище `ws` данных в рабочее пространство. Этот хранилище данных получает `test` доступ `account_name` к файловой системе на учетной записи хранилища, используя основные учетные данные службы.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Студия машинного обучения Azure. 

Создание нового хранилища данных в несколько шагов в студии Машинного обучения Azure:

> [!IMPORTANT]
> Если ваша учетная запись находится в виртуальной сети, поддерживается только создание хранилищ данных [через SDK.](#python-sdk) 

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com/).
1. Выберите **datastores** на левом стеле под **Управлением.**
1. Выберите **новый магазин данных**.
1. Заполните форму для нового хранилища данных. Форма разумно обновляется на основе выделения для типа хранения Azure и типа проверки подлинности.
  
Информацию, необходимую для заполнения формы, можно найти на [портале Azure.](https://portal.azure.com) Выберите **учетные записи хранения** на левом стеле и выберите учетную запись хранения, которую вы хотите зарегистрировать. Страница **«Обзор»** содержит такие сведения, как имя учетной записи, контейнер и имя файла. 

* Для элементов проверки подлинности, таких как ключ учетной записи или маркер SAS, перейдите к **клавишам доступа** на панели **настроек.** 

* Для основных элементов обслуживания, таких как идентификатор клиента и идентификатор клиента, перейдите на **регистрацию приложений** и выберите, какое приложение вы хотите использовать. Соответствующая страница **Обзора** будет содержать эти элементы. 

Следующий пример показывает, как выглядит форма при создании хранилища данных Azure blob: 
    
![Форма для нового хранилища данных](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Получайте хранилища данных из рабочего пространства

Чтобы зарегистрировать определенный хранилище данных в текущем [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) рабочем пространстве, используйте статический метод в `Datastore` классе:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Чтобы получить список хранилив данных, зарегистрированных [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) в данном рабочем пространстве, можно использовать свойство на объекте рабочей области:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Чтобы получить хранилище данных рабочего пространства по умолчанию, используйте эту строку:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Отправка и загрузка данных

[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) Методы, описанные в следующих примерах, специфичны для классов [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) и [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) и работают одинаково.

### <a name="upload"></a>Передать

Загрузите каталог или отдельные файлы в хранилище данных с помощью Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Параметр `target_path` определяет местоположение в доле файла (или контейнере с blob) для загрузки. Он по `None`умолчанию, так что данные загружаются на корень. Если, `overwrite=True`любые `target_path` существующие данные на перезаписаны.

Вы также можете загрузить список отдельных файлов `upload_files()` в хранилище данных с помощью метода.

### <a name="download"></a>Скачать

Скачать данные из хранилища данных в локальную файловую систему:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Параметр `target_path` — это расположение локального каталога для загрузки данных. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` `None`это так, все содержимое вашего файла (или контейнер акро) будет загружено.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Доступ к данным во время обучения

Чтобы взаимодействовать с данными в хранилищах данных или упаковывать данные в расходный объект для задач машинного обучения, таких как обучение, [создайте набор данных Azure Machine Learning.](how-to-create-register-datasets.md) Наборы данных предоставляют функции, которые загружают табулярные данные в панды или Spark DataFrame. Наборы данных также предоставляют возможность загрузки или установки файлов любого формата из хранилища Azure Blob, файлов Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, базы данных Azure S'L и базы данных Azure для PostgreS'L. [Подробнее о том, как тренироваться с наборами данных.](how-to-train-with-datasets.md)

### <a name="accessing-source-code-during-training"></a>Доступ к исходному коду во время обучения

Хранилище Azure Blob имеет более высокую скорость пропускной работы, чем доля файлов Azure, и будет масштабироваться до большого числа заданий, начатых параллельно. По этой причине мы рекомендуем настроить свои трассы для использования хранилища Blob для передачи файлов исходного кода.

В следующем примере кода указывается в конфигурации запуска, которая использует хранилище данных для передачи исходного кода.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Доступ к данным во время подсчета очков

Azure Machine Learning предоставляет несколько способов использования моделей для оценки. Некоторые из этих методов не обеспечивают доступ к хранилищам данных. Используйте следующую таблицу, чтобы понять, какие методы позволяют получить доступ к хранилищам данных во время подсчета очков:

| Метод | Доступ в хранилище данных | Описание |
| ----- | :-----: | ----- |
| [Прогнозирование в пакетном режиме](how-to-use-parallel-run-step.md) | ✔ | Асинхронное создание прогнозов на больших объемах данных. |
| [Веб-служба](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей в виде веб-сервиса. |
| [Модуль Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей на устройстваIoT Edge. |

В ситуациях, когда SDK не предоставляет доступ к хранилищам данных, можно создать пользовательский код, используя соответствующий SDK Azure для доступа к данным. Например, [SDK Для хранения Azure для Python](https://github.com/Azure/azure-storage-python) — это клиентская библиотека, которую можно использовать для доступа к данным, хранящимся в каплях или файлах.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Перемещение данных в поддерживаемые решения для хранения данных Azure

Azure Machine Learning поддерживает доступ к данным из хранилища Azure Blob, файлов Azure, хранилища озер Azure Data Gen1, хранилища озер Лазурных данных Gen2, базы данных Azure S-L и базы данных Azure для PostgreS'L. Если вы используете неподдерживаемое хранилище, мы рекомендуем перемещать данные в поддерживаемые решения для хранения данных Azure с помощью [Azure Data Factory и этих шагов.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool) Перемещение данных в поддерживаемое хранилище может помочь вам сэкономить затраты на вывоз данных во время экспериментов машинного обучения. 

Azure Data Factory обеспечивает эффективную и устойчивую передачу данных с более чем 80 предварительно построенными разъемами без каких-либо дополнительных затрат. Эти разъемы включают службы данных Azure, наедине источники данных, Amazon S3 и Redshift, а также Google Big'еry.

## <a name="next-steps"></a>Следующие шаги

* [Создание набора данных машинного обучения Azure](how-to-create-register-datasets.md)
* [Обучение модели](how-to-train-ml-models.md)
* [Развертывание модели](how-to-deploy-and-where.md)
