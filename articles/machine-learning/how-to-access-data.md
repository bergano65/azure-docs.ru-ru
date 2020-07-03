---
title: Подключение к службам хранилища Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать хранилища данных для безопасного подключения к службам хранилища Azure во время обучения с помощью Машинное обучение Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: f5a7605a1fa68c3a600c77ded762722990d7a514
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231209"
---
# <a name="connect-to-azure-storage-services"></a>Подключение к службам хранилища Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как подключиться к службам хранилища Azure с помощью Машинное обучение Azure хранилищ данных. Хранилища данных хранят сведения о подключении, такие как идентификатор подписки и авторизация маркеров в [Key Vault](https://azure.microsoft.com/services/key-vault/) , связанных с рабочей областью, чтобы обеспечить безопасный доступ к хранилищу без необходимости жесткого кодирования в скриптах. Сведения о расположении хранилищ данных в общем рабочем процессе доступа к данным Машинное обучение Azure см. в статье [безопасный доступ](concept-data.md#data-workflow) к данным.

Хранилища данных можно создавать из [этих решений службы хранилища Azure](#matrix). Для неподдерживаемых решений для хранения данных и для сохранения затрат на исходящие данные во время экспериментов машинного обучения рекомендуется [переместить данные](#move) в поддерживаемые решения службы хранилища Azure. 

## <a name="prerequisites"></a>Предварительные требования

Что вам понадобится:
- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree).

- Учетная запись хранения Azure с [контейнером больших двоичных объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) или [общей папкой Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Машинное обучение Azure пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или доступ к [машинное обучение Azure Studio](https://ml.azure.com/).

- Рабочая область машинного обучения Azure.
  
  [Создайте машинное обучение Azure рабочую область](how-to-manage-workspace.md) или используйте существующую, используя пакет SDK для Python. Импортируйте `Workspace` класс `Datastore` и и загрузите сведения о подписке из файла `config.json` с помощью функции. `from_config()` По умолчанию в текущем каталоге выполняется поиск JSON-файла, но можно также указать параметр path, указывающий на файл с помощью `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Поддерживаемые типы служб хранилища данных

В настоящее время хранилища данных поддерживают хранение сведений о подключении к службам хранилища, указанным в следующей матрице.

| Тип&nbsp;хранилища | Тип&nbsp;проверки подлинности | [Студии&nbsp;машинного&nbsp;обучения Azure](https://ml.azure.com/) | [Пакет&nbsp;SDK&nbsp;Python&nbsp; для машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [CLI&nbsp;машинного&nbsp;обучения Azure](reference-azure-machine-learning-cli.md) | [API&nbsp;-&nbsp;интерфейс&nbsp; для службы "машинное обучение Azure"](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Хранилище&nbsp;BLOB&nbsp;-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Ключ учетной записи <br> Маркер SAS | ✓ | ✓ | ✓ |✓
[&nbsp;Файловый&nbsp;ресурс Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Ключ учетной записи <br> Маркер SAS | ✓ | ✓ | ✓ |✓
[Служба&nbsp;хранилища&nbsp;Data Lake Azure&nbsp;(GEN 1)](https://docs.microsoft.com/azure/data-lake-store/)| Субъект-служба| ✓ | ✓ | ✓ |✓
[Служба&nbsp;хранилища&nbsp;Data Lake Azure&nbsp;(Gen 2)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Субъект-служба| ✓ | ✓ | ✓ |✓
[База&nbsp;данных&nbsp;SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Проверка подлинности SQL <br>Субъект-служба| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Проверка подлинности SQL| ✓ | ✓ | ✓ |✓
[База&nbsp;данных&nbsp;Azure&nbsp;для MySQL](https://docs.microsoft.com/azure/mysql/overview) | Проверка подлинности SQL|  | ✓* | ✓* |✓*
[&nbsp;Файловая&nbsp;система кирпичей](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| без аутентификации; | | ✓** | ✓ ** |✓** 

* MySQL поддерживается только для конвейера [дататрансферстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
* * Кирпичи обработки информации поддерживаются только для [датабрикксстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) конвейера

### <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Рекомендуется создать хранилище данных для [контейнера больших двоичных объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Хранилище уровня "Стандартный" и "Премиум" доступны для больших двоичных объектов. Хотя хранилище класса Premium является более дорогим, его быстрые скорости пропускной способности могут повысить скорость выполнения обучения, особенно при обучении большого набора данных. Сведения о стоимости учетных записей хранения см. в [калькуляторе цен Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) построены на основе хранилища BLOB-объектов Azure и предназначены для анализа больших данных предприятия. Основная часть Data Lake Storage 2-го поколения — это добавление [иерархического пространства имен](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) в хранилище BLOB-объектов. Иерархическое пространство имен позволяет упорядочивать объекты и файлы в иерархии каталогов для эффективного доступа к данным.

При создании рабочей области контейнер больших двоичных объектов Azure и файловый ресурс Azure автоматически регистрируются в рабочей области. Они называются `workspaceblobstore` и `workspacefilestore`соответственно. `workspaceblobstore`используется для хранения артефактов рабочей области и журналов экспериментов машинного обучения. `workspacefilestore`используется для хранения записных книжек и скриптов R, авторизация которых осуществляется с помощью [вычислительного экземпляра](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). `workspaceblobstore` Контейнер задается как хранилище данных по умолчанию.

> [!IMPORTANT]
> Машинное обучение Azure конструктор (Предварительная версия) создаст хранилище данных с именем **azureml_globaldatasets** автоматически при открытии образца на домашней странице конструктора. В хранилище данных содержатся только образцы наборов данных. **Не** используйте это хранилище данных для доступа к конфиденциальным данным.
> ![Автоматическое создание хранилища данных для образцов DataSet в конструкторе](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Создание и регистрация хранилищ данных

При регистрации решения службы хранилища Azure в качестве хранилища данных вы автоматически создаете и регистрируете хранилище данных в определенной рабочей области. Хранилища данных можно создавать и регистрировать в рабочей области с помощью [пакета SDK для Python](#python-sdk) или [машинное обучение Azure Studio](#azure-machine-learning-studio).

>[!IMPORTANT]
> В ходе создания и регистрации начального хранилища данных Машинное обучение Azure проверяет, существует ли базовая служба хранилища, и что пользователь, который предоставил участник (имя пользователя, субъект-служба или маркер SAS), имеет доступ к этому хранилищу. Однако для Azure Data Lake Storage 1 и 2 хранилищ данных эта проверка происходит позже, когда вызываются методы доступа к данным, такие [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) как [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) или. 
<br><br>
После создания хранилища данных эта проверка выполняется только для методов, которым требуется доступ к базовому контейнеру хранилища, а **не** при извлечении объектов хранилища данных. Например, проверка выполняется, если требуется загрузить файлы из хранилища данных. но если вы просто хотите изменить хранилище данных по умолчанию, то проверка не будет выполняться.

### <a name="python-sdk"></a>Пакет SDK для Python

Все методы Register относятся к [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) классу и имеют форму. `register_azure_*`
> [!IMPORTANT]
> Если ваша учетная запись хранения находится в виртуальной сети, поддерживается только создание хранилищ данных **с помощью пакета SDK** .

Сведения, необходимые для заполнения `register_azure_*()` метода, можно найти в [портал Azure](https://portal.azure.com).

* Если вы планируете использовать ключ учетной записи или маркер SAS для проверки подлинности, выберите **учетные записи хранения** на левой панели и выберите учетную запись хранения, которую требуется зарегистрировать. 
  * На странице **Обзор** содержатся такие сведения, как имя учетной записи, контейнер и имя общего файлового ресурса. 
      1. Для ключей учетной записи перейдите в **раздел ключи доступа** на панели **Параметры** . 
      1. Для маркеров SAS перейдите на страницу " **подписанные** URL" на панели " **Параметры** ".

* Если вы планируете использовать субъект-службу для проверки подлинности, перейдите к **Регистрация приложений** и выберите приложение, которое вы хотите использовать. 
    * Соответствующая страница **обзора** будет содержать требуемые сведения, такие как идентификатор клиента и идентификатор клиента.

В следующих примерах показано, как зарегистрировать контейнер больших двоичных объектов Azure, файловый ресурс Azure и Azure Data Lake Storage поколение 2 в качестве хранилища данных. Параметры, приведенные в этих примерах, являются **обязательными параметрами** для создания и регистрации хранилища данных. 

Чтобы создать хранилища данных для других служб хранилища и просмотреть дополнительные параметры для этих методов, см. [справочную документацию по `register_azure_*` соответствующим методам](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Контейнер BLOB-объектов

Чтобы зарегистрировать контейнер больших двоичных объектов Azure в качестве хранилища данных [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-), используйте.

Следующий код создает и регистрирует `blob_datastore_name` хранилище данных в `ws` рабочей области. Это хранилище `my-account-name` данных обращается к `my-container-name` контейнеру больших двоичных объектов в учетной записи хранения, используя предоставленный ключ доступа к учетной записи.

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
Если контейнер больших двоичных объектов находится в виртуальной сети, включите `skip_validation=True` параметр в [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) метод. 

#### <a name="file-share"></a>Общая папка

Чтобы зарегистрировать общую папку Azure в качестве хранилища данных, используйте [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Следующий код создает и регистрирует `file_datastore_name` хранилище данных в `ws` рабочей области. Это хранилище `my-account-name` данных получает доступ к `my-fileshare-name` общей папке в учетной записи хранения, используя предоставленный ключ доступа к учетной записи.

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
Если общая папка находится в виртуальной сети, включите параметр `skip_validation=True` в [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) метод. 

#### <a name="azure-data-lake-storage-generation-2"></a>Поколение Azure Data Lake Storage 2

Для хранилища данных Azure Data Lake Storage Generation 2 (ADLS Gen 2) используйте [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) , чтобы зарегистрировать хранилище учетные данные, подключенное к хранилищу Azure Data Lake 2 с [разрешениями субъекта-службы](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Чтобы использовать субъект-службу, необходимо [зарегистрировать приложение](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) и предоставить субъекту-службе доступ к *владельцу данных BLOB-объекта хранилища* . Узнайте больше об [управлении доступом, настроенном для ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Чтобы использовать субъект-службу, необходимо [зарегистрировать приложение](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) и предоставить субъекту-службе правильный доступ к данным. Узнайте больше об [управлении доступом, настроенном для ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Следующий код создает и регистрирует `adlsgen2_datastore_name` хранилище данных в `ws` рабочей области. Это хранилище данных обращается к файловой системе `test` в учетной записи `account_name` хранения, используя предоставленные учетные данные субъекта-службы.

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

Создайте хранилище данных, выполнив несколько шагов в Машинное обучение Azure Studio:

> [!IMPORTANT]
> Если ваша учетная запись хранения находится в виртуальной сети, поддерживается только создание хранилищ данных [с помощью пакета SDK](#python-sdk) . 

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com/).
1. Выберите **хранилища данных** на левой панели в разделе **Управление**.
1. Выберите **+ Новое хранилище данных**.
1. Заполните форму для нового хранилища данных. Форма интеллектуального обновления обновляется на основе выбора типа службы хранилища Azure и типа проверки подлинности.
  
Сведения, необходимые для заполнения формы, можно найти на [портал Azure](https://portal.azure.com). Выберите **учетные записи хранения** на левой панели и выберите учетную запись хранения, которую требуется зарегистрировать. На странице **Обзор** содержатся такие сведения, как имя учетной записи, контейнер и имя общего файлового ресурса. 

* Для элементов проверки подлинности, таких как ключ учетной записи или маркер SAS, перейдите к **разделу ключи доступа** на панели **Параметры** . 

* В качестве элементов субъекта-службы, таких как, идентификатор клиента и клиентский идентификатор, перейдите к **Регистрация приложений** и выберите приложение, которое требуется использовать. На соответствующей странице **обзора** будут содержаться эти элементы. 

В следующем примере показано, как выглядит форма при создании хранилища BLOB-объектов Azure. 
    
![Форма для нового хранилища данных](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Получение хранилищ данных из рабочей области

Чтобы получить конкретное хранилище данных, зарегистрированное в текущей рабочей области, [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) используйте статический метод для `Datastore` класса:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Чтобы получить список хранилищ данных, зарегистрированных в заданной рабочей области, можно использовать [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) свойство для объекта рабочей области:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Чтобы получить хранилище данных по умолчанию для рабочей области, используйте следующую строку:

```Python
datastore = ws.get_default_datastore()
```
Можно также изменить хранилище данных по умолчанию, используя следующий код. Эта возможность поддерживается только через пакет SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Отправка и загрузка данных

Методы [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) и [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) , описанные в следующих примерах, относятся к классам [азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) и [азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) , а также работают одинаково.

> [!NOTE]
> В настоящее время отправка в хранилища данных AzureDataLakeGen2 не поддерживается.

### <a name="upload"></a>Передать

Отправьте либо каталог, либо отдельные файлы в хранилище данных с помощью пакета SDK для Python:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` Параметр указывает расположение в общей папке (или контейнере больших двоичных объектов) для передачи. По умолчанию используется `None`значение, поэтому данные передаются в корневую папку. Если `overwrite=True`значение равно, все существующие `target_path` данные в перезаписываются.

Можно также передать список отдельных файлов в хранилище данных с помощью `upload_files()` метода.

### <a name="download"></a>Скачать

Скачайте данные из хранилища данных в локальную файловую систему:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` Параметр — это расположение локального каталога, в который загружаются данные. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` имеет `None`значение, будет скачано все содержимое общей папки (или контейнера больших двоичных объектов).

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Доступ к данным во время обучения

Для взаимодействия с данными в хранилищах данных или упаковки данных в объект, который можно использовать для задач машинного обучения, например для обучения, [Создайте набор данных машинное обучение Azure](how-to-create-register-datasets.md). Наборы данных предоставляют функции, которые загружают табличные данные в таблицу данных Pandas или Spark. Наборы данных также предоставляют возможность скачивания или подключения файлов любого формата из хранилища BLOB-объектов Azure, файлов Azure, Azure Data Lake Storage 1-го поколения, Azure Data Lake Storage 2-го поколения, базы данных SQL Azure и Azure для PostgreSQL. [Узнайте больше о том, как обучить наборы данных](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Доступ к исходному коду во время обучения

В хранилище BLOB-объектов Azure более высокая скорость пропускной способности, чем общая папка Azure, и масштабирование до большого числа заданий, запускаемых параллельно. По этой причине рекомендуется настроить запуски для использования хранилища BLOB-объектов для передачи файлов исходного кода.

В следующем примере кода указывается в конфигурации запуска, которую хранилище данных BLOB-объектов будет использовать для передачи исходного кода.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Доступ к данным во время оценки

Машинное обучение Azure предоставляет несколько способов использования моделей для оценки. Некоторые из этих методов не предоставляют доступ к хранилищам данных. Используйте следующую таблицу, чтобы понять, какие методы позволяют получать доступ к хранилищам данных во время оценки.

| Метод | Доступ к хранилищу данных | Описание |
| ----- | :-----: | ----- |
| [Прогнозирование в пакетном режиме](how-to-use-parallel-run-step.md) | ✔ | Асинхронное создание прогнозов на больших объемах данных. |
| [Веб-служба](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей в качестве веб-службы. |
| [Модуль Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей на IoT Edge устройствах. |

В ситуациях, когда пакет SDK не предоставляет доступ к хранилищам данных, вы можете создать пользовательский код с помощью соответствующего пакета SDK Azure для доступа к данным. Например, [пакет SDK для службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python) — это клиентская библиотека, которую можно использовать для доступа к данным, хранящимся в больших двоичных объектах или файлах.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Перемещение данных в поддерживаемые решения службы хранилища Azure

Машинное обучение Azure поддерживает доступ к данным из хранилища BLOB-объектов Azure, файлов Azure, Azure Data Lake Storage 1-го поколения, Azure Data Lake Storage 2-го поколения, базы данных SQL Azure и базы данных Azure для PostgreSQL. Если вы используете неподдерживаемое хранилище, мы рекомендуем перенести данные в поддерживаемые решения службы хранилища Azure с помощью [фабрики данных Azure и следующих шагов](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Перемещение данных в поддерживаемое хранилище поможет сохранить затраты на исходящие данные во время экспериментов машинного обучения. 

Фабрика данных Azure обеспечивает эффективную и устойчивую пересылку данных с более чем 80 предварительно подготовленных соединителей без дополнительных затрат. К этим соединителям относятся службы данных Azure, локальные источники данных, Amazon S3 и Redshift и Google BigQuery.

## <a name="next-steps"></a>Следующие шаги

* [Создание набора данных машинного обучения Azure](how-to-create-register-datasets.md)
* [Обучение модели](how-to-train-ml-models.md)
* [Развертывание модели](how-to-deploy-and-where.md)
