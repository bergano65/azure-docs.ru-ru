---
title: Подключение к службам хранилища Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать хранилища данных для безопасного подключения к службам хранилища Azure во время обучения с помощью Службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 904738d73aaa0580773a085c70cd74f4240fc4b7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773939"
---
# <a name="connect-to-azure-storage-services"></a>Подключение к службам хранилища Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье приводятся сведения о подключении к службам хранилища Azure с помощью хранилищ данных Машинного обучения Azure. В хранилищах данных содержатся данные о подключении, такие как идентификатор подписки и авторизация с помощью маркеров в [Key Vault](https://azure.microsoft.com/services/key-vault/), связанных с рабочей областью. С их помощью вы можете безопасно получать доступ к хранилищу без необходимости их жесткого кодирования в сценариях. Сведения о работе с хранилищами данных в общем рабочем процессе доступа к данным в Машинном обучение Azure см. в статье о [безопасном доступе к данным](concept-data.md#data-workflow).

Хранилища данных можно создавать на основе [этих решений службы хранилища Azure](#matrix). При использовании неподдерживаемых решений хранилищ и для сокращения затрат на исходящие данные во время экспериментов машинного обучения рекомендуется [переместить данные](#move) в поддерживаемые решения для хранилища Azure. 

## <a name="prerequisites"></a>Предварительные требования

Что вам понадобится:
- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).

- Учетная запись хранения Azure с [контейнером BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) или [общей папкой Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Пакет SDK для Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) или доступ к [Студии машинного обучения Azure](https://ml.azure.com/).

- Рабочая область машинного обучения Azure.
  
  [Создайте рабочую область Машинного обучения Azure](how-to-manage-workspace.md) или используйте существующую с помощью пакета SDK для Python. Импортируйте классы `Workspace` и `Datastore` и загрузите сведения о подписке из файла `config.json` с помощью функции `from_config()`. Эта функция выполняет поиск файла JSON по умолчанию в текущем каталоге, но можно также указать параметр path, указывающий на файл, с помощью `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Поддерживаемые типы служб хранилища данных

В настоящее время хранилища данных поддерживают хранение сведений о подключении к службам хранилищ, указанных в следующей таблице.

| Тип&nbsp;хранилища | Тип&nbsp;проверки подлинности | [Студия&nbsp;машинного&nbsp;обучения Azure](https://ml.azure.com/) | [Пакет SDK для&nbsp;машинного&nbsp;обучения Azure для&nbsp;Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [CLI&nbsp;машинного&nbsp;обучения Azure](reference-azure-machine-learning-cli.md) | [Rest API&nbsp;машинного&nbsp;обучения&nbsp;Azure](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Хранилище&nbsp;BLOB-объектов&nbsp;Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Ключ учетной записи <br> Маркер SAS | ✓ | ✓ | ✓ |✓
[Общая&nbsp;папка&nbsp;Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Ключ учетной записи <br> Маркер SAS | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage 1-го&nbsp;поколения](https://docs.microsoft.com/azure/data-lake-store/)| Субъект-служба| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage 2-го&nbsp;поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Субъект-служба| ✓ | ✓ | ✓ |✓
[База данных&nbsp;SQL&nbsp;Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Проверка подлинности SQL <br>Субъект-служба| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Проверка подлинности SQL| ✓ | ✓ | ✓ |✓
[База данных&nbsp;Azure&nbsp;для&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | Проверка подлинности SQL|  | ✓* | ✓* |✓*
[Файловая&nbsp;система&nbsp;Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| без аутентификации; | | ✓** | ✓ ** |✓** 

* MySQL поддерживается только для класса [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) конвейера. <br>
** Databricks поддерживаются только для класса [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) конвейера.

### <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Рекомендуется создать хранилище данных для [контейнера BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Для BLOB-объектов доступны хранилища класса Standard и Premium. Хотя хранилище класса Premium является более дорогим, за счет его высокой пропускной способности можно повысить скорость выполнения обучения, особенно при обучении на основе большого набора данных. О стоимости учетных записей хранения можно узнать на странице [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) создано на основе хранилища BLOB-объектов Azure и предназначено для анализа больших данных в организации. Основная часть Data Lake Storage 2-го поколения — это добавление [иерархического пространства имен](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) в хранилище BLOB-объектов. Иерархическое пространство имен позволяет упорядочивать объекты и файлы в иерархии каталогов для эффективного доступа к данным.

При создании рабочей области в ней автоматически регистрируются контейнер BLOB-объектов Azure и общая папка Azure. Они называются `workspaceblobstore` и `workspacefilestore`, соответственно. `workspaceblobstore` используется для хранения артефактов рабочей области и журналов экспериментов машинного обучения. `workspacefilestore` используется для хранения записных книжек и скриптов R, авторизованных с помощью [вычислительной операции](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). Контейнер `workspaceblobstore` задан в качестве хранилища данных по умолчанию.

> [!IMPORTANT]
> Конструктор машинного обучения Azure (предварительная версия) автоматически создаст хранилище данных с именем **azureml_globaldatasets** при открытии примера на домашней странице конструктора. В этом хранилище данных содержатся только примеры наборов данных. **Не** используйте это хранилище данных для доступа к конфиденциальным данным.
> ![Автоматически созданное хранилище для примеров наборов данных конструктора](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Создание и регистрация хранилищ данных

При регистрации решения для хранилища Azure в качестве хранилища данных вы автоматически создаете и регистрируете хранилище данных в определенной рабочей области. Для создания и регистрации хранилищ данных в рабочей области можно использовать [пакет SDK для Python](#python-sdk) или [Студию машинного обучения Azure](#azure-machine-learning-studio).

>[!IMPORTANT]
> В ходе создания и регистрации начального хранилища данных Машинное обучение Azure проверяет, существует ли базовая служба хранилища и имеет ли предоставленный пользователем субъект (имя пользователя, субъект-служба или маркер SAS) доступ к этому хранилищу. Однако для хранилищ данных Azure Data Lake Storage 1-го и 2-го поколений эта проверка выполняется позже, когда вызываются методы доступа к данным, такие как [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) или [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-). 
<br><br>
После создания хранилища данных эта проверка осуществляется только для методов, которым требуется доступ к базовому контейнеру хранилища, а **не** при каждом извлечении объектов хранилища данных. Например, проверка выполняется, если требуется скачать файлы из хранилища данных. Но если вы просто хотите изменить хранилище данных по умолчанию, проверки не будет.

### <a name="python-sdk"></a>Пакет SDK для Python

Все методы регистрации содержатся в классе [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) и имеют форму `register_azure_*`.
> [!IMPORTANT]
> Если ваша учетная запись хранения находится в виртуальной сети, поддерживается создание хранилищ данных только **с помощью пакета SDK**.

Сведения, необходимые для заполнения метода `register_azure_*()`, можно найти на [портале Azure](https://portal.azure.com).

* Если для проверки подлинности вы планируете использовать ключ учетной записи или маркер SAS, выберите элемент **Учетные записи хранения** на панели слева и выберите учетную запись хранения, которую требуется зарегистрировать. 
  * На странице **Обзор** приводятся такие сведения, как имя учетной записи, контейнер и имя общей папки. 
      1. Чтобы использовать ключи учетной записи, перейдите к элементу **Ключи доступа** на панели **Параметры**. 
      1. Чтобы использовать маркеры SAS, перейдите к элементу **Подписанные URL-адреса** на панели **Параметры**.

* Если проверка подлинности будет выполняться с помощью субъекта-службы, перейдите в раздел **Регистрация приложений** и выберите нужное приложение. 
    * На соответствующей странице **Обзор** вы найдете такие требуемые сведения, такие как идентификаторы клиентов (tenant ID и client iD).

В следующих примерах показано, как зарегистрировать контейнер BLOB-объектов Azure, общую папку Azure и Azure Data Lake Storage 2-го поколения в качестве хранилища данных. Параметры, приведенные в этих примерах, являются **обязательными** для создания и регистрации хранилища данных. 

Сведения о создании хранилищ данных для других служб хранилища и просмотре дополнительных параметров для этих методов см. в [справочной документации по соответствующим методам `register_azure_*`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Контейнер BLOB-объектов

Чтобы зарегистрировать контейнер BLOB-объектов в качестве хранилища данных, используйте [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Следующий код создает и регистрирует хранилище данных `blob_datastore_name` в рабочей области `ws`. Это хранилище данных обращается к контейнеру BLOB-объектов `my-container-name` в учетной записи хранения `my-account-name` с помощью предоставленного ключа доступа учетной записи.

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
Если контейнер BLOB-объектов находится в виртуальной сети, включите параметр `skip_validation=True` в метод [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-). 

#### <a name="file-share"></a>Общая папка

Чтобы зарегистрировать общую папку Azure в качестве хранилища данных, используйте [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Следующий код создает и регистрирует хранилище данных `file_datastore_name` в рабочей области `ws`. Это хранилище данных обращается к общей папке `my-fileshare-name` в учетной записи хранения `my-account-name` с помощью предоставленного ключа доступа учетной записи.

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
Если общая папка находится в виртуальной сети, включите параметр `skip_validation=True` в метод [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage 2-го поколения

Для хранилища данных Azure Data Lake Storage 2-го поколения (ADLS 2-го поколения) используйте [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) для регистрации базы данных учетных данных, подключенной к хранилищу Azure DataLake 2-го поколения, с [разрешениями субъекта-службы](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Чтобы использовать субъект-службу, необходимо [зарегистрировать приложение](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) и предоставить субъекту-службе доступ с правами на *чтение данных BLOB-объектов хранилища*. Узнайте больше о [контроле доступа в ADLS 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Чтобы использовать субъект-службу, необходимо [зарегистрировать приложение](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) и предоставить субъекту-службе соответствующие права доступа к данным. Узнайте больше о [контроле доступа в ADLS 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Следующий код создает и регистрирует хранилище данных `adlsgen2_datastore_name` в рабочей области `ws`. Это хранилище данных обращается к файловой системе `test` в учетной записи хранения `account_name` с помощью предоставленных учетных данных субъекта-службы.

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

Создайте хранилище данных, выполнив всего несколько шагов в Студии машинного обучения Azure.

> [!IMPORTANT]
> Если ваша учетная запись хранения находится в виртуальной сети, поддерживается создание хранилищ данных только [с помощью пакета SDK](#python-sdk). 

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com/).
1. Выберите элемент **Хранилища данных** на панели слева в разделе **Управление**.
1. Выберите **+ Новое хранилище данных**.
1. Заполните форму для нового хранилища данных. Форма самостоятельно интеллектуально обновится в соответствии с выбранным типом хранилища Azure и типа проверки подлинности.
  
Сведения, необходимые для заполнения формы, можно найти на [портале Azure](https://portal.azure.com). Выберите элемент **Учетные записи хранения** на панели слева и выберите учетную запись хранения, которую требуется зарегистрировать. На странице **Обзор** приводятся такие сведения, как имя учетной записи, контейнер и имя общей папки. 

* Для использования элементов проверки подлинности, таких как ключ учетной записи или маркер SAS, перейдите к элементу **Ключи доступа** на панели **Параметры**. 

* Для использования элементов субъекта-службы, таких как идентификаторы клиентов (tenant ID и client ID), перейдите в раздел **Регистрация приложений** и выберите нужное приложение. Эти элементы будут находиться на соответствующей странице **Обзор**. 

В следующем примере показано, как выглядит форма при создании хранилища BLOB-объектов Azure. 
    
![Форма для нового хранилища данных.](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Получение хранилищ данных из рабочей области

Чтобы получить конкретное хранилище данных, зарегистрированное в текущей рабочей области, используйте статический метод [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) в классе `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Чтобы получить список хранилищ данных, зарегистрированных в определенной рабочей области, можно использовать свойство [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) в объекте рабочей области:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Чтобы получить хранилище данных рабочей области по умолчанию, используйте следующую строку:

```Python
datastore = ws.get_default_datastore()
```
С помощью следующего кода можно изменить хранилище данных по умолчанию. Эта возможность поддерживается только в пакете SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Отправка и загрузка данных

Методы [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) и [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-), описанные в следующих примерах, относятся к классам [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) и [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) и работают одинаково для каждого из них.

> [!NOTE]
> В настоящее время отправка в хранилища данных AzureDataLakeGen2 не поддерживается.

### <a name="upload"></a>Передать

Отправьте каталог или отдельные файлы в хранилище данных с помощью пакета SDK для Python.

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Параметр `target_path` указывает расположение в общей папке (или контейнере BLOB-объектов) для отправки. По умолчанию используется значение `None`, поэтому данные отправляются в корневую папку. Если задано значение `overwrite=True`, любые существующие данные в параметре `target_path` перезаписываются.

Кроме того, в хранилище данных можно отправить список отдельных файлов с помощью метода `upload_files()`.

### <a name="download"></a>Скачивание

Скачайте данные из хранилища данных в локальную файловую систему:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Параметр `target_path` — это расположение локального каталога для скачивания данных. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` имеет значение `None`, будет скачано все содержимое общей папки (или контейнера BLOB-объектов).

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Доступ к данным во время обучения

Для взаимодействия с данными в хранилищах данных или для упаковки данных в объект, который можно использовать для задач машинного обучения, таких как обучение, [создайте набор данных Машинного обучения Azure](how-to-create-register-datasets.md). Наборы данных предоставляют функции, которые загружают табличные данные в таблицу данных Pandas или Spark. Кроме того, наборы данных позволяют скачивать или подключать файлы любого формата из хранилища BLOB-объектов Azure, Файлов Azure, Azure Data Lake Storage 1-го поколения, Azure Data Lake Storage 2-го поколения, базы данных SQL Azure и базы данных Azure для PostgreSQL. [Узнайте больше об обучении с наборами данных](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Доступ к исходному коду во время обучения

Хранилище BLOB-объектов Azure имеет более высокую пропускную способность, чем общая папка Azure, и может масштабироваться до большого числа параллельно запущенных заданий. По этой причине рекомендуется настроить запуски для использования хранилища BLOB-объектов для передачи файлов исходного кода.

В следующем примере кода в конфигурации запуска указывается хранилище данных BLOB-объектов, которое будет использоваться для передачи исходного кода.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Доступ к данным во время оценки

Машинное обучение Azure предоставляет несколько способов использования моделей для оценки. Некоторые из этих методов не предоставляют доступ к хранилищам данных. В следующей таблице приводятся методы, позволяющие получить доступ к хранилищам данных во время оценки.

| Метод | Доступ к хранилищу данных | Описание |
| ----- | :-----: | ----- |
| [Прогнозирование в пакетном режиме](how-to-use-parallel-run-step.md) | ✔ | Асинхронное создание прогнозов на основе больших объемов данных. |
| [Веб-служба](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей в качестве веб-служб. |
| [Модуль Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей на устройствах IoT Edge. |

В случаях, когда пакет SDK не предоставляет доступ к хранилищам данных, можно создать пользовательский код для доступа к данным с помощью соответствующего пакета SDK Azure Например, [пакет SDK для хранилища Azure для Python](https://github.com/Azure/azure-storage-python) является клиентской библиотекой, подходящей для доступа к данным, хранящимся в BLOB-объектах или файлах.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Перемещение данных в поддерживаемые решения хранилища Azure

Машинное обучение Azure поддерживает доступ к данным из хранилища BLOB-объектов Azure, Файлов Azure, Azure Data Lake Storage 1-го поколения, Azure Data Lake Storage 2-го поколения, базы данных SQL Azure и базы данных Azure для PostgreSQL. Если вы используете неподдерживаемое хранилище, рекомендуется переместить данные в поддерживаемые решения хранилища Azure с помощью [фабрики данных Azure и следующих действий](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Перемещение данных в поддерживаемое хранилище поможет сократить расходы на исходящие данные во время экспериментов машинного обучения. 

Фабрика данных Azure обеспечивает эффективную и стабильную передачу данных с помощью более чем 80 предварительно подготовленных соединителей без дополнительных затрат. К этим соединителям относятся службы данных Azure, локальные источники данных, Amazon S3, Redshift и Google BigQuery.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание набора данных Машинного обучения Azure](how-to-create-register-datasets.md)
* [Обучение модели](how-to-train-ml-models.md)
* [Развертывание модели](how-to-deploy-and-where.md)
