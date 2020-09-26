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
ms.date: 07/22/2020
ms.custom: how-to, contperfq1, devx-track-python
ms.openlocfilehash: 639e4cde82fd8496c90f8ebf263dd42661a75748
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296813"
---
# <a name="connect-to-azure-storage-services"></a>Подключение к службам хранилища Azure

Из этой статьи вы узнаете, как **подключиться к службам хранилища Azure с помощью машинное обучение Azure хранилищ данных**. Хранилища данных безопасно подключаются к службе хранилища Azure, не заключая учетные данные проверки подлинности и целостность исходного источника. Они хранят сведения о подключении, такие как идентификатор подписки и авторизация маркера, в [Key Vault](https://azure.microsoft.com/services/key-vault/) , связанных с рабочей областью, чтобы обеспечить безопасный доступ к хранилищу без необходимости жесткого кодирования в скриптах. Для создания и регистрации хранилищ данных можно использовать [пакет SDK для машинное обучение Azure Python](#python) или [машинное обучение Azure Studio](how-to-connect-data-ui.md) .

Если вы предпочитаете создавать хранилища данных и управлять ими с помощью расширения Машинное обучение Azure VS Code; Дополнительные сведения см. в [VS Code руководства по управлению ресурсами](how-to-manage-resources-vscode.md#datastores) .

Хранилища данных можно создавать на основе [этих решений службы хранилища Azure](#matrix). **Для неподдерживаемых решений для хранения**данных и для сохранения затрат на исходящие данные во время экспериментов в машинном [коде перенесите данные](#move) в поддерживаемое решение службы хранилища Azure.  

Сведения о работе с хранилищами данных в общем рабочем процессе доступа к данным в Машинном обучение Azure см. в статье о [безопасном доступе к данным](concept-data.md#data-workflow).

## <a name="prerequisites"></a>Предварительные требования

Что вам понадобится:
- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).

- Учетная запись хранения Azure с [поддерживаемым типом хранилища](#matrix).

- [Пакет SDK для Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) или доступ к [Студии машинного обучения Azure](https://ml.azure.com/).

- Рабочая область машинного обучения Azure.
  
  [Создайте рабочую область Машинного обучения Azure](how-to-manage-workspace.md) или используйте существующую с помощью пакета SDK для Python. 

    Импортируйте классы `Workspace` и `Datastore` и загрузите сведения о подписке из файла `config.json` с помощью функции `from_config()`. Эта функция выполняет поиск файла JSON по умолчанию в текущем каталоге, но можно также указать параметр path, указывающий на файл, с помощью `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    При создании рабочей области контейнер больших двоичных объектов Azure и файловый ресурс Azure автоматически регистрируются в качестве хранилищ данных в рабочей области. Они называются `workspaceblobstore` и `workspacefilestore`, соответственно. `workspaceblobstore`Используется для хранения артефактов рабочей области и журналов экспериментов машинного обучения. Он также задается в качестве **хранилища данных по умолчанию** и не может быть удален из рабочей области. `workspacefilestore`Используется для хранения записных книжек и скриптов R, авторизация которых осуществляется с помощью [вычислительного экземпляра](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files).
    
    > [!NOTE]
    > Конструктор Машинное обучение Azure создаст хранилище данных с именем **azureml_globaldatasets** автоматически при открытии образца на домашней странице конструктора. В этом хранилище данных содержатся только примеры наборов данных. **Не** используйте это хранилище данных для доступа к конфиденциальным данным.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Поддерживаемые типы служб хранилища данных

В настоящее время хранилища данных поддерживают хранение сведений о подключении к службам хранилищ, указанных в следующей таблице.

| Тип&nbsp;хранилища | Тип&nbsp;проверки подлинности | [Студия&nbsp;машинного&nbsp;обучения Azure](https://ml.azure.com/) | [Пакет SDK для&nbsp;машинного&nbsp;обучения Azure для&nbsp;Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) |  [CLI&nbsp;машинного&nbsp;обучения Azure](reference-azure-machine-learning-cli.md) | [Rest API&nbsp;машинного&nbsp;обучения&nbsp;Azure](https://docs.microsoft.com/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[Хранилище&nbsp;BLOB-объектов&nbsp;Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Ключ учетной записи <br> Маркер SAS | ✓ | ✓ | ✓ |✓ |✓
[Общая&nbsp;папка&nbsp;Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Ключ учетной записи <br> Маркер SAS | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage 1-го&nbsp;поколения](https://docs.microsoft.com/azure/data-lake-store/)| Субъект-служба| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage 2-го&nbsp;поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Субъект-служба| ✓ | ✓ | ✓ |✓|
[База данных&nbsp;SQL&nbsp;Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Проверка подлинности SQL <br>Субъект-служба| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Проверка подлинности SQL| ✓ | ✓ | ✓ |✓|
[База данных&nbsp;Azure&nbsp;для&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | Проверка подлинности SQL|  | ✓* | ✓* |✓*|
[Файловая&nbsp;система&nbsp;Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| без аутентификации; | | ✓** | ✓ ** |✓** |

\* MySQL поддерживается только для конвейера [дататрансферстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py&preserve-view=true)<br />
\*\*Модуль обработки блоков обработки информации поддерживается только для [датабрикксстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py&preserve-view=true) конвейера


### <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Рекомендуется создать хранилище данных для [контейнера BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Для BLOB-объектов доступны хранилища класса Standard и Premium. Хотя хранилище класса Premium является более дорогим, за счет его высокой пропускной способности можно повысить скорость выполнения обучения, особенно при обучении на основе большого набора данных. О стоимости учетных записей хранения можно узнать на странице [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) создано на основе хранилища BLOB-объектов Azure и предназначено для анализа больших данных в организации. Основная часть Data Lake Storage 2-го поколения — это добавление [иерархического пространства имен](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) в хранилище BLOB-объектов. Иерархическое пространство имен позволяет упорядочивать объекты и файлы в иерархии каталогов для эффективного доступа к данным.

## <a name="storage-access-and-permissions"></a>Доступ к хранилищу и разрешения

Чтобы обеспечить безопасное подключение к службе хранилища Azure, Машинное обучение Azure требуется разрешение на доступ к соответствующему контейнеру хранилища данных. Этот доступ зависит от учетных данных проверки подлинности, используемых для регистрации хранилища данных. 

### <a name="virtual-network"></a>Виртуальная сеть 

Если учетная запись хранения данных находится в **виртуальной сети**, необходимо выполнить дополнительные действия по настройке, чтобы обеспечить машинное обучение Azure доступ к данным. При создании и регистрации хранилища данных см. статью [использование машинное обучение Azure Studio в виртуальной сети Azure](how-to-enable-studio-virtual-network.md) .  

### <a name="access-validation"></a>Проверка доступа

В ходе **процесса создания и регистрации хранилища данных**машинное обучение Azure автоматически проверяет, существует ли базовая служба хранилища, и предоставленный пользователем участник (имя пользователя, субъект-служба или маркер SAS) имеет доступ к указанному хранилищу.

**После создания хранилища данных**эта проверка выполняется только для методов, которым требуется доступ к базовому контейнеру хранилища, а **не** при извлечении объектов хранилища данных. Например, проверка выполняется, если требуется скачать файлы из хранилища данных. Но если вы просто хотите изменить хранилище данных по умолчанию, проверки не будет.

Для проверки подлинности доступа к базовой службе хранилища можно указать ключ учетной записи, маркеры общего доступа (SAS) или субъект-службу в соответствующем `register_azure_*()` методе создаваемого типа хранилища данных. В [матрице типа хранилища](#matrix) перечислены поддерживаемые типы проверки подлинности, соответствующие каждому типу хранилища данных.

Сведения о ключе учетной записи, маркере SAS и субъекте-службе можно найти на [портал Azure](https://portal.azure.com).

* Если для проверки подлинности вы планируете использовать ключ учетной записи или маркер SAS, выберите элемент **Учетные записи хранения** на панели слева и выберите учетную запись хранения, которую требуется зарегистрировать. 
  * На странице **Обзор** приводятся такие сведения, как имя учетной записи, контейнер и имя общей папки. 
      1. Чтобы использовать ключи учетной записи, перейдите к элементу **Ключи доступа** на панели **Параметры**. 
      1. Чтобы использовать маркеры SAS, перейдите к элементу **Подписанные URL-адреса** на панели **Параметры**.

* Если вы планируете использовать субъект-службу для проверки подлинности, перейдите к **Регистрация приложений** и выберите приложение, которое вы хотите использовать. 
    * Соответствующая страница **обзора** будет содержать требуемые сведения, такие как идентификатор клиента и идентификатор клиента.

> [!IMPORTANT]
> По соображениям безопасности может потребоваться изменить ключи доступа для учетной записи хранения Azure (ключ учетной записи или маркер SAS). При этом не забудьте синхронизировать новые учетные данные с рабочей областью и подключенными к ней хранилищами данных. Узнайте, как [синхронизировать обновленные учетные данные](how-to-change-storage-access-key.md). 

### <a name="permissions"></a>Разрешения

Для контейнера больших двоичных объектов Azure и Azure Data Lake хранилища Gen 2 Убедитесь, что учетные данные для проверки подлинности имеют доступ на **Чтение данных BLOB-объекта хранилища** . См. Дополнительные сведения о [модуле чтения BLOB-объектов хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). По умолчанию маркер SAS учетной записи не имеет разрешений. Для доступа на чтение данных учетные данные проверки подлинности должны иметь минимум из списка и разрешений на чтение для контейнеров и объектов. Для доступа на запись данных также требуются разрешения на запись и добавление.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Создание и регистрация хранилищ данных

При регистрации решения для хранилища Azure в качестве хранилища данных вы автоматически создаете и регистрируете хранилище данных в определенной рабочей области. Дополнительные сведения о сценариях виртуальной сети и о том, где найти необходимые учетные данные для проверки подлинности, см. в разделе [разрешения & доступа к хранилищу](#storage-access-and-permissions) 

В этом разделе приведены примеры создания и регистрации хранилища данных с помощью пакета SDK для Python для следующих типов хранилищ. Параметры, приведенные в этих примерах, являются **обязательными** для создания и регистрации хранилища данных.

* [Контейнер BLOB-объектов Azure](#azure-blob-container)
* [Файловый ресурс Azure](#azure-file-share)
* [Azure Data Lake Storage 2-го поколения](#azure-data-lake-storage-generation-2)

 Сведения о создании хранилищ данных для других поддерживаемых служб хранилища см. в [справочной документации по соответствующим `register_azure_*` методам](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py&preserve-view=true#&preserve-view=truemethods).

Если вы предпочитаете работу с кодом, см. статью [Подключение к данным с помощью машинное обучение Azure Studio](how-to-connect-data-ui.md).

> [!NOTE]
> Имя хранилища данных должно состоять только из строчных букв, цифр и знаков подчеркивания. 

### <a name="azure-blob-container"></a>Контейнер BLOB-объектов Azure

Чтобы зарегистрировать контейнер BLOB-объектов в качестве хранилища данных, используйте [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Следующий код создает и регистрирует хранилище данных `blob_datastore_name` в рабочей области `ws`. Это хранилище данных обращается к контейнеру BLOB-объектов `my-container-name` в учетной записи хранения `my-account-name` с помощью предоставленного ключа доступа учетной записи. Дополнительные сведения о сценариях виртуальной сети и о том, где найти необходимые учетные данные для проверки подлинности, см. в разделе [разрешения & доступа к хранилищу](#storage-access-and-permissions) 

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

### <a name="azure-file-share"></a>Общая папка Azure

Чтобы зарегистрировать общую папку Azure в качестве хранилища данных, используйте [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Следующий код создает и регистрирует хранилище данных `file_datastore_name` в рабочей области `ws`. Это хранилище данных обращается к общей папке `my-fileshare-name` в учетной записи хранения `my-account-name` с помощью предоставленного ключа доступа учетной записи. Дополнительные сведения о сценариях виртуальной сети и о том, где найти необходимые учетные данные для проверки подлинности, см. в разделе [разрешения & доступа к хранилищу](#storage-access-and-permissions) 

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage 2-го поколения

Для хранилища данных Azure Data Lake Storage 2-го поколения (ADLS 2-го поколения) используйте [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) для регистрации базы данных учетных данных, подключенной к хранилищу Azure DataLake 2-го поколения, с [разрешениями субъекта-службы](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  

Чтобы использовать субъект-службу, необходимо [зарегистрировать приложение](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) и предоставить субъекту-службе доступ к **модулю чтения данных BLOB-объекта хранилища** . Узнайте больше о [контроле доступа в ADLS 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Следующий код создает и регистрирует хранилище данных `adlsgen2_datastore_name` в рабочей области `ws`. Это хранилище данных обращается к файловой системе `test` в учетной записи хранения `account_name` с помощью предоставленных учетных данных субъекта-службы. Дополнительные сведения о сценариях виртуальной сети и о том, где найти необходимые учетные данные для проверки подлинности, см. в разделе [разрешения & доступа к хранилищу](#storage-access-and-permissions) 

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

<a name="arm"></a>

## <a name="create-datastores-using-azure-resource-manager"></a>Создание хранилищ данных с помощью Azure Resource Manager

Существует ряд шаблонов [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) , которые можно использовать для создания хранилищ данных.

Сведения об использовании этих шаблонов см. в разделе [Использование шаблона Azure Resource Manager для создания рабочей области для машинное обучение Azure](how-to-create-workspace-template.md).

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Используйте данные в хранилищах данных

После создания хранилища данных [создайте машинное обучение Azure набор данных](how-to-create-register-datasets.md) для взаимодействия с данными. Наборы данных упаковывают ваши данные в отложенно вычисляемый объект для задач машинного обучения, например для обучения. Они также предоставляют возможность [скачивания или подключения](how-to-train-with-datasets.md#mount-vs-download) файлов любого формата из служб хранилища Azure, таких как, хранилище BLOB-объектов Azure и ADLS Gen 2. Их также можно использовать для загрузки табличных данных в таблицу данных Pandas или Spark.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Получение хранилищ данных из рабочей области

Чтобы получить конкретное хранилище данных, зарегистрированное в текущей рабочей области, используйте статический метод [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-workspace--datastore-name-) в классе `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Чтобы получить список хранилищ данных, зарегистрированных в определенной рабочей области, можно использовать свойство [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedatastores) в объекте рабочей области:

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
