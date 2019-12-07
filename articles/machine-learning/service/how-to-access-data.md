---
title: Доступ к данным в службах хранилища Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать хранилища данных для доступа к службам хранилища Azure во время обучения с помощью Машинное обучение Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: d2e86c06cca26da2776459f3c20bf921a02ed89b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894717"
---
# <a name="access-data-in-azure-storage-services"></a>Доступ к данным в службах хранилища Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как легко получить доступ к данным в службах хранилища Azure с помощью Машинное обучение Azure хранилищ данных. Хранилища данных используются для хранения сведений о подключении, таких как идентификатор подписки и авторизация маркеров. С помощью хранилищ данных вы можете получить доступ к хранилищу, не имея в скриптах сведения о соединении с жестким кодом. Хранилища данных можно создавать из этих решений службы [хранилища Azure](#matrix). Для неподдерживаемых решений для хранения данных, чтобы сохранить затраты на исходящие данные во время экспериментов машинного обучения, мы рекомендуем перенести данные в поддерживаемые решения службы хранилища Azure. [Узнайте, как перемещать данные](#move). 

В этом пошаговом окне приведены примеры следующих задач.
* [Регистрация хранилищ данных](#access)
* [Получение хранилищ данных из рабочей области](#get)
* [Отправка и скачивание данных с помощью хранилищ](#up-and-down)
* [Доступ к данным во время обучения](#train)
* [Перемещение данных в Azure](#move)

## <a name="prerequisites"></a>Технические условия

- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

- Учетная запись хранения Azure с [контейнером больших двоичных объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) или [общей папкой Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Машинное обучение Azure пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или доступ к [машинное обучение Azure Studio](https://ml.azure.com/).

- Рабочая область машинного обучения Azure. 
    - Либо [создайте машинное обучение Azure рабочую область](how-to-manage-workspace.md) , либо используйте существующую, используя пакет SDK для Python.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Создание и регистрация хранилищ данных

При регистрации решения службы хранилища Azure в качестве хранилища данных автоматически создается хранилище данных в определенной рабочей области. Хранилища данных можно создавать и регистрировать в рабочей области с помощью пакета SDK для Python или Машинное обучение Azure Studio.

### <a name="using-the-python-sdk"></a>С помощью пакета SDK для Python

Все методы Register относятся к классу [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) и имеют форму register_azure_ *.

Сведения, необходимые для заполнения метода Register (), можно найти с помощью [портал Azure](https://portal.azure.com). Выберите **учетные записи хранения** на левой панели и выберите учетную запись хранения, которую нужно зарегистрировать. На странице **Обзор** представлены такие сведения, как имя учетной записи, контейнер или имя общей папки. Для получения сведений о проверке подлинности, таких как ключ учетной записи или маркер SAS, перейдите к **разделу ключи учетной записи** в области **Параметры** слева. 

В следующих примерах показано, как зарегистрировать контейнер больших двоичных объектов Azure или файловый ресурс Azure в качестве хранилища данных.

+ Для **хранилища данных контейнера больших двоичных объектов Azure**используйте [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    Следующий код создает и регистрирует хранилище данных, `my_datastore`, в рабочую область `ws`. Это хранилище данных обращается к контейнеру больших двоичных объектов Azure `my_blob_container`, в учетной записи хранения Azure `my_storage_account` с помощью предоставленного ключа учетной записи.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```
    Если ваша учетная запись хранения находится в виртуальной сети, поддерживается только создание хранилища больших двоичных объектов Azure. Задайте для параметра `grant_workspace_access` значение `True`, чтобы предоставить рабочей области доступ к вашей учетной записи хранения.

+ Для **хранилища данных файлового ресурса Azure**используйте [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

    Следующий код создает и регистрирует хранилище данных, `my_datastore`, в рабочую область `ws`. Это хранилище данных получает доступ к файловому ресурсу Azure, `my_file_share`, в учетной записи хранения Azure `my_storage_account` используя указанный ключ учетной записи.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Рекомендации по выбору хранилища

Мы рекомендуем использовать контейнер больших двоичных объектов Azure. Хранилище уровня "Стандартный" и "Премиум" доступны для больших двоичных объектов. Хотя это и более дорого, мы рекомендуем хранилище уровня "Премиум" из-за более высоких скоростей пропускной способности, которые могут повысить скорость обучения, особенно при обучении большого набора данных. Сведения о стоимости учетной записи хранения см. в [калькуляторе цен Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

### <a name="using-azure-machine-learning-studio"></a>Использование Машинное обучение Azure Studio 

Создайте новое хранилище данных, выполнив несколько шагов в Машинное обучение Azure Studio.

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com/).
1. Выберите **хранилища данных** в левой области в разделе **Управление**.
1. Выберите **+ создать хранилище данных**.
1. Заполните новую форму хранилища данных. Форма обновляется на основе типа хранилища Azure и выбора типа проверки подлинности.
  
Сведения, необходимые для заполнения формы, можно найти с помощью [портал Azure](https://portal.azure.com). Выберите **учетные записи хранения** на левой панели и выберите учетную запись хранения, которую нужно зарегистрировать. На странице **Обзор** представлены такие сведения, как имя учетной записи, контейнер или имя общей папки. Для элементов проверки подлинности, таких как ключ учетной записи или маркер SAS, перейдите к **разделу ключи учетной записи** в области **Параметры** слева.

В следующем примере показано, как будет выглядеть форма для создания хранилища BLOB-объектов Azure. 
    
 ![Создать хранилище данных](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Получение хранилищ данных из рабочей области

Чтобы получить конкретное хранилище данных, зарегистрированное в текущей рабочей области, используйте статический метод [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) в классе хранилища данных:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Чтобы получить список хранилищ данных, зарегистрированных в определенной рабочей области, можно использовать свойство [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) объекта рабочей области.

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

При создании рабочей области контейнер больших двоичных объектов Azure и файловый ресурс Azure регистрируются в рабочей области с именем `workspaceblobstore` и `workspacefilestore` соответственно. Они хранят сведения о подключении контейнера больших двоичных объектов и общей папки, подготовленной в учетной записи хранения, подключенной к рабочей области. `workspaceblobstore` задается в качестве хранилища данных по умолчанию.

Чтобы получить хранилище данных рабочей области по умолчанию:

```Python
datastore = ws.get_default_datastore()
```

Чтобы определить другое хранилище данных по умолчанию для текущей рабочей области, используйте метод [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) для объекта рабочей области:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Отправка & скачивание данных
Методы [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) и [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) , описанные в следующих примерах, относятся к классам [азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) и [азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) и работают одинаково.

### <a name="upload"></a>Отправка

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

Параметр `target_path` указывает расположение в общей папке (или контейнере больших двоичных объектов) для передачи. По умолчанию используется значение `None`: в этом случае данные отправляются в корневой каталог. В противном случае, если `overwrite=True` любые существующие данные в `target_path` перезаписываются.

Или отправьте список отдельных файлов в хранилище данных с помощью метода `upload_files()`.

### <a name="download"></a>Download (Скачать)

Аналогичным образом можно загрузить данные из хранилища данных в локальную файловую систему.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Параметр `target_path` — это расположение локального каталога, в который нужно загрузить данные. Чтобы указать путь к папке для загрузки в общей папке (или контейнере больших двоичных объектов), укажите этот путь в `prefix`. Если `prefix` имеет значение `None`, будет загружено все содержимое общей папки (или контейнера больших двоичных объектов).

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Доступ к данным во время обучения

> [!IMPORTANT]
> Использование [машинное обучение Azure наборов](how-to-create-register-datasets.md) данных — это новый рекомендуемый способ доступа к данным в процессе обучения. Наборы данных предоставляют функции, которые загружают табличные данные в Pandas или Spark, а также могут скачивать или подключать файлы любого формата из большого двоичного объекта Azure, файла Azure, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL. Узнайте больше о [том, как обучить наборы данных](how-to-train-with-datasets.md).

В следующей таблице перечислены методы, которые сообщают целевому объекту вычислений, как использовать хранилища данных во время выполнения. 

Проезд|Метод|Описание|
----|-----|--------
Подключение| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Используйте для подключения хранилища данных к целевому объекту вычислений.
Download (Скачать)|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Используйте для загрузки содержимого хранилища данных в расположение, указанное `path_on_compute`. <br><br> Этот процесс загрузки выполняется перед запуском.
Отправка|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Используйте для передачи файла из расположения, указанного параметром `path_on_compute`, в хранилище данных. <br><br> Эта отправка происходит после выполнения.

Чтобы создать ссылку на определенную папку или файл в хранилище данных и сделать его доступным в целевом объекте вычислений, используйте метод [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) хранилища данных.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Любой указанный `datastore` или `datastore.path` объект разрешается в имя переменной среды в формате `"$AZUREML_DATAREFERENCE_XXXX"`, значение которого представляет путь подключения или загрузки в целевом вычислении. Путь к хранилищу данных в целевом вычислении может отличаться от пути выполнения для обучающего скрипта.

### <a name="examples"></a>Примеры 

Следующие примеры кода относятся к классу [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) для доступа к данным во время обучения. 

`script_params` — это словарь, содержащий параметры для entry_script. Используйте его для передачи хранилища данных и описания того, как данные становятся доступными в целевом объекте вычислений. Ознакомьтесь с нашим комплексным [руководством](tutorial-train-models-with-aml.md).

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

Вы также можете передать список хранилищ данных в конструктор средства оценки `inputs` параметр для подключения или копирования в хранилища (или). Этот пример кода:
* Скачивает все содержимое в `datastore1` целевому объекту вычислений до запуска `train.py` сценария обучения
* Скачивает папку `'./foo'` в `datastore2` целевой объект вычислений перед запуском `train.py`
* Передает файл `'./bar.pkl'` из целевого объекта вычислений в `datastore3` после выполнения скрипта

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Матрица вычислений и хранилища данных

В настоящее время хранилища данных поддерживают хранение сведений о подключении к службам хранилища, указанным в следующей матрице. Эта матрица отображает доступные функциональные возможности доступа к данным для различных целевых объектов вычислений и сценариев хранилища данных. Дополнительные сведения о [целевых показателях вычислений для машинное обучение Azure](how-to-set-up-training-targets.md#compute-targets-for-training).

|Compute|[азуреблобдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[азурефиледатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[азуредаталакедатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [азурепостгресклдатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [азуресклдатабаседатасторе](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Местного уровня|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|Н/Д         |Н/Д                                                                         |
| Вычислительная среда Машинного обучения Azure; |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [конвейеры машинного обучения&nbsp;](concept-ml-pipelines.md)|Н/Д         |Н/Д                                                                         |
| Виртуальные машины               |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| HDInsight                      |[as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |Н/Д         |Н/Д                                                                         |
| Передача данных                  |[Конвейеры МАШИНного&nbsp;](concept-ml-pipelines.md)                                               |Н/Д                                           |[Конвейеры МАШИНного&nbsp;](concept-ml-pipelines.md)            |[Конвейеры МАШИНного&nbsp;](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Конвейеры МАШИНного&nbsp;](concept-ml-pipelines.md)                                              |Н/Д                                           |[Конвейеры МАШИНного&nbsp;](concept-ml-pipelines.md)             |Н/Д                                                                         |
| Пакетная служба Azure                    |[Конвейеры МАШИНного&nbsp;](concept-ml-pipelines.md)                                               |Н/Д                                           |Н/Д         |Н/Д                                                                         |
| Azure Data Lake Analytics       |Н/Д                                           |Н/Д                                           |[Конвейеры МАШИНного&nbsp;](concept-ml-pipelines.md)             |Н/Д                                                                         |

> [!NOTE]
> Возможны ситуации, в которых очень итеративные процессы обработки больших данных выполняются быстрее с использованием `as_download()` вместо `as_mount()`; Это можно проверить в эксперименте.

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
| [Прогнозирование пакетной службы](how-to-run-batch-predictions.md) | ✔ | Асинхронное создание прогнозов на больших объемах данных. |
| [Веб-служба](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей в качестве веб-службы. |
| [Модуль IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Развертывание моделей для IoT Edge устройств. |

В ситуациях, когда пакет SDK не предоставляет доступ к хранилищам данных, вы можете создать пользовательский код, используя соответствующий пакет SDK Azure для доступа к данным. Например, [пакет SDK для службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python) — это клиентская библиотека, которую можно использовать для доступа к данным, хранящимся в больших двоичных объектах или файлах.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Перемещение данных в поддерживаемые решения службы хранилища Azure

Машинное обучение Azure поддерживает доступ к данным из большого двоичного объекта Azure, файла Azure, Azure Data Lake Gen 1, Azure Data Lake Gen 2, Azure SQL, Azure PostgreSQL. Для неподдерживаемого хранилища, чтобы сохранить стоимость исходящих данных во время экспериментов машинного обучения, мы рекомендуем перенести данные в поддерживаемые решения службы хранилища Azure с помощью фабрики данных Azure. Фабрика данных Azure обеспечивает эффективную и устойчивую пересылку данных с более чем 80 предварительно подготовленных соединителей, включая службы данных Azure, локальные источники данных, Amazon S3 и Redshift, и Google BigQuery — без дополнительной оплаты. Следуйте пошаговым инструкциям [, чтобы переместить данные с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Дальнейшие действия

* [Train a model](how-to-train-ml-models.md) (Обучение модели).

* [Развертывание модели](how-to-deploy-and-where.md).
