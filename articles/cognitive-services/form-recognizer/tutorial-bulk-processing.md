---
title: Руководство. Пакетное извлечение данных форм с помощью Фабрики данных Azure — Распознаватель документов
titleSuffix: Azure Cognitive Services
description: Настройте действия Фабрики данных Azure, чтобы активировать обучение и запуск моделей Распознавателя документов для оцифровки большого объема невыполненной работы с документами.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 1780aebc113fa68a9a89cfce9fd67c9b5911fc58
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606707"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>Руководство по пакетному извлечению данных форм с помощью службы "Фабрика данных Azure"

Из этого руководства вы узнаете, как с помощью служб Azure выполнить прием большого пакета форм в файлы мультимедиа. В этом руководстве показано, как автоматизировать прием данных документов из Azure Data Lake в Базе данных SQL Azure. Вы сможете быстро обучать модели и обрабатывать новые документы несколькими щелчками мыши.

## <a name="business-need"></a>Бизнес-потребности

Сейчас большинство организаций понимают, насколько ценными являются данные в разных форматах (PDF, изображения, видео). Компаниям нужны рекомендации и сведения о наиболее экономичных способах оцифровки данных.

Кроме того, наши клиенты часто имеют дело с формами различного типа, поступающими от множества клиентов и пользователей. В отличие от [кратких руководств](./quickstarts/client-library.md), в этом руководстве показано, как автоматически обучить модель с помощью форм различных и новых типов, используя подход на основе метаданных. Если у вас нет модели для форм определенного типа, система создаст ее и предоставит вам идентификатор модели. 

Извлекая данные из форм и объединяя их с существующими операционными системами и хранилищами данных, компании могут получать ценные сведения и предоставлять преимущества своим клиентам и бизнес-пользователям.

С помощью Распознавателя документов Azure мы помогаем организациям использовать свои данные, автоматизировать процессы (платежи по счетам, обработку налогов и т. д.), а также экономить деньги и время и повышать точность данных.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * настройка Azure Data Lake для хранения форм;
> * создание таблицы параметризации с помощью базы данных Azure;
> * хранение конфиденциальных учетных данных с использованием Azure Key Vault;
> * обучение модели Распознавателя документов в записной книжке Databricks;
> * извлечение данных из формы с помощью записной книжки Databricks;
> * автоматизация обучения и извлечения форм с помощью Фабрики данных Azure.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Получив подписку Azure, перейдите к <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Создание ресурса Распознавателя документов"  target="_blank">созданию ресурса Распознавателя документов <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания ресурса выберите элемент **Перейти к ресурсу**.
    * Для подключения приложения к API "Распознаватель документов" потребуется ключ и конечная точка созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.
* Минимум пять документов одного типа. В идеале этот рабочий процесс предназначен для больших наборов документов. Советы и варианты для объединения данных в набор для обучения см. в статье [Создание набора данных для обучения для пользовательской модели](./build-training-data-set.md). При работе с этим руководством можно использовать файлы в папке **Train** из [примера набора данных](https://go.microsoft.com/fwlink/?linkid=2128080).

## <a name="project-architecture"></a>Архитектура проекта 

Этот проект включает набор конвейеров Фабрики данных Azure для активации записных книжек Python, которые обучают, анализируют и извлекают данные из документов в учетной записи хранения Azure Data Lake.

В качестве входных данных REST API "Распознаватель документов" требуются определенные параметры. Из соображений безопасности некоторые из этих параметров будут храниться в Azure Key Vault, а другие, менее конфиденциальные (например, имя папки большого двоичного объекта хранилища), — в таблице параметризации в базе данных SQL Azure.

Тип анализируемой формы инженеры данных или специалисты по обработке и анализу данных указывают в строке таблицы параметров. Затем в Фабрике данных Azure они выполняют итерацию по списку обнаруженных типов форм и передают соответствующие параметры в записную книжку Databricks для обучения или переобучения моделей Распознавателя документов. Кроме того, здесь можно использовать одну из функций Azure.

Затем записная книжка Azure Databricks использует обученные модели для извлечения данных из форм и экспортирует их в базу данных SQL Azure.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="Архитектура проекта":::


## <a name="set-up-azure-data-lake"></a>Настройка Azure Data Lake

Список невыполненной работы с формами может находиться в локальной среде или на сервере (s)FTP. При работе с этим руководством используются формы в учетной записи хранения Azure Data Lake 2-го поколения. Вы можете передавать файлы с помощью Фабрики данных Azure, Обозревателя службы хранилища Azure или AzCopy. Наборы данных для обучения и оценки могут находиться в разных контейнерах, но наборы данных для обучения для форм всех типов должны размещаться в одном контейнере (хотя они могут быть в разных папках).

Для создания озера данных выполните инструкции, приведенные в статье [Создание учетной записи хранения для использования с Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/create-data-lake-storage-account).

## <a name="create-a-parameterization-table"></a>Создание таблицы параметризации

Далее мы создадим таблицу метаданных в базе данных SQL Azure. Эта таблица будет содержать неконфиденциальные данные, необходимые REST API "Распознаватель документов". При наличии в наборе данных формы нового типа мы вставляем новую запись в эту таблицу и активируем конвейер обучения и оценки (который будет реализован позже).

В таблице будут использоваться следующие поля:

* **form_description**: это поле не является обязательным в рамках обучения. Оно содержит описание типа форм, для которых обучается модель (например, "формы клиента А", "формы гостиницы Б").
* **training_container_name**: это поле содержит имя контейнера учетной записи хранения, в котором мы сохранили набор данных для обучения. Это может быть тот же контейнер, что и **scoring_container_name**.
* **training_blob_root_folder**: папка в учетной записи хранения, где будут храниться файлы для обучения модели.
* **scoring_container_name**: это поле содержит имя контейнера учетной записи хранения, где сохранены файлы, из которых нужно извлечь пары "ключ — значение". Это может быть тот же контейнер, что и **training_container_name**.
* **scoring_input_blob_folder**: папка в учетной записи хранения, в которой будут храниться файлы для извлечения данных.
* **model_id**: идентификатор модели, которую нужно обучить повторно. Для первого запуска значение должно быть равно -1. Это позволит создать в учебной записной книжке новую настраиваемую модель для обучения. Учебная записная книжка вернет только что созданный идентификатор модели в экземпляр Фабрики данных Azure, а с помощью действия хранимой процедуры мы обновим это значение в базе данных SQL Azure.

  Если нужно принять форму нового типа, необходимо вручную сбросить идентификатор модели до -1, прежде чем обучать ее.

* **file_type**: поддерживаемые типы форм: `application/pdf`, `image/jpeg`, `image/png` и `image/tif`.

  При наличии форм с различными типами файлов необходимо изменить это значение и **model_id** при обучении формы нового типа.
* **form_batch_group_id**: со временем у вас может быть несколько типов форм, обучаемых по одной и той же модели. **form_batch_group_id** позволяет указать все типы форм, которые были обучены с помощью определенной модели.

### <a name="create-the-table"></a>Создание таблицы

[Создайте базу данных SQL Azure](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase), а затем выполните приведенный ниже скрипт SQL в [редакторе запросов](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal), чтобы создать необходимую таблицу.

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Выполните приведенный ниже скрипт, чтобы создать процедуру автоматического обновления **model_id** после обучения модели.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Хранение конфиденциальных учетных данных с использованием Azure Key Vault

Из соображений безопасности не следует хранить определенные конфиденциальные данные в таблице параметризации в базе данных SQL Azure. Конфиденциальные параметры будут храниться в виде секретов Azure Key Vault.

### <a name="create-an-azure-key-vault"></a>создать Azure Key Vault;

[Создайте ресурс Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault). После создания ресурса Key Vault перейдите к нему и в разделе **параметров** выберите элемент **Секреты**, чтобы добавить параметры.

В открывшемся окне выберите элемент **Generate/import** (Создать или импортировать). Введите имя параметра и его значение, затем нажмите кнопку "Создать". Выполните это действие для следующих параметров:

* **CognitiveServiceEndpoint**: URL-адрес конечной точки API "Распознаватель документов".
* **CognitiveServiceSubscriptionKey**: ключ доступа для службы "Распознаватель документов". 
* **StorageAccountName**. Учетная запись хранения, в которой хранятся набор данных для обучения и формы, из которых необходимо извлечь пары "ключ — значение". Если они находятся в разных учетных записях, введите каждое имя учетной записи в виде отдельного секрета. Учитывайте, что наборы данных для обучения должны находиться в одном контейнере для форм всех типов, но они могут размещаться в разных папках.
* **StorageAccountSasKey**: подписанный URL-адрес (SAS) в учетной записи хранения. Чтобы получить URL-адрес SAS, перейдите к своему ресурсу хранилища и выберите вкладку **Обозреватель службы хранилища**. Перейдите к контейнеру, щелкните правой кнопкой мыши и выберите элемент **Получить подписанный URL-адрес**. Важно получить SAS для вашего контейнера, а не для самой учетной записи хранения. Убедитесь, что разрешение на **чтение** и разрешение **списка** установлены и нажмите кнопку **Создать**. Затем скопируйте значение в разделе **URL-адрес**. Оно должно быть в таком формате: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Обучение модели Распознавателя документов в записной книжке Databricks

Для хранения и запуска кода Python, взаимодействующего со службой "Распознаватель документов", мы будем использовать Azure Databricks.

### <a name="create-a-notebook-in-databricks"></a>Создание записной книжки в Databricks

[Создайте ресурс Azure Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) на портале Azure. Перейдите к созданному ресурсу и запустите рабочую область.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Создание области секретов на основе Azure Key Vault

Чтобы ссылаться на секреты в созданном ранее ресурсе Azure Key Vault, необходимо создать область секретов в Databricks. Выполните инструкции, приведенные в разделе [Создание области секретов с поддержкой Azure Key Vault](https://docs.microsoft.com/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Создание кластера Databricks

Кластер — это коллекция вычислительных ресурсов Databricks. Чтобы создать кластер, сделайте следующее:

1. На боковой панели нажмите кнопку **Кластеры**.
1. На странице **Кластеры** щелкните элемент **Создать кластер**.
1. На странице **Создание кластера** укажите имя кластера и выберите элемент **7.2 (Scala 2.12, Spark 3.0.0)** в раскрывающемся списке версий Databricks Runtime.
1. Щелкните **Create cluster** (Создать кластер).

### <a name="write-a-settings-notebook"></a>Создание записной книжки параметров

Теперь можно добавить записные книжки Python. Сначала создайте записную книжку с именем **Settings**. В этой записной книжке значения в таблице параметризации будут присвоены переменным в скрипте. Эти значения позже будут переданы в качестве параметров Фабрики данных Azure. Кроме того, мы присвоим переменным значения из секретов в Key Vault. 

1. Чтобы создать записную книжку **Settings**, нажмите кнопку **Рабочая область** на вкладке "Создать" и щелкните раскрывающийся список. Затем последовательно выберите элементы **Создать** и **Записная книжка**.
1. Во всплывающем окне введите имя, которое нужно присвоить записной книжке, и выберите **Python** в качестве языка по умолчанию. Выберите кластер Databricks и нажмите кнопку **Создать**.
1. В первой ячейке записной книжки мы извлекаем параметры, переданные Фабрикой данных Azure.

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. Во второй ячейке мы извлекаем секреты из Key Vault и присваиваем их переменным.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Создание учебной записной книжки

Теперь, когда записная книжка **Settings** готова, мы можем создать записную книжку для обучения модели. Как упоминалось выше, мы будем использовать файлы, хранящиеся в папке в учетной записи хранения Azure Data Lake 2-го поколения (**training_blob_root_folder**). Имя папки передано в качестве переменной. Все наборы типов форм будут находиться в одной папке. По мере выполнения цикла по таблице параметров мы будем обучать модель с использованием форм всех типов. 

1. Создайте новую записную книжку с именем **TrainFormRecognizer**. 
1. В первой ячейке выполните записную книжку Settings:

    ```python
    %run "./Settings"
    ```

1. В следующей ячейке назначьте переменные из файла **Settings** и динамически обучите модель для каждого типа формы, применив код из [краткого руководства по REST](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. Заключительный этап процесса обучения — получение результата обучения в формате JSON.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-using-a-notebook"></a>Извлечение данных из формы с помощью записной книжки

### <a name="mount-the-azure-data-lake-storage"></a>Подключение Azure Data Lake Storage

Следующий этап — оценка различных форм с использованием обученной модели. Мы подключим учетную запись хранения Azure Data Lake в Databricks и будем обращаться к подключению во время приема данных.

Как и на этапе обучения, для вызова извлечения пар "ключ — значение" из форм мы будем использовать Фабрику данных Azure. Мы будем циклически проходить по формам в папках, указанных в таблице параметров.

1. Давайте создадим записную книжку, чтобы подключить учетную запись хранения в Databricks. Мы назовем ее **MountDataLake**. 
1. Сначала необходимо вызвать записную книжку **Settings**:

    ```python
    %run "./Settings"
    ```

1. Во второй ячейке мы определим переменные для конфиденциальных параметров, которые получим из наших секретов Key Vault.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Затем мы попытаемся отключить учетную запись хранения, если она была подключена ранее.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Наконец, мы подключим учетную запись хранения.


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > Мы подключили только учебную учетную запись хранения &mdash; в этом случае учебные файлы и файлы, из которых требуется извлечь пары "ключ — значение", находятся в одной и той же учетной записи хранения. Если для оценки и обучения используются разные учетные записи хранения, вам потребуется подключить обе учетные записи хранения. 

### <a name="write-the-scoring-notebook"></a>Создание записной книжки оценки

Теперь мы можем создать записную книжку оценки. Как и для учебной записной книжки, мы будем использовать файлы, хранящиеся в папках в учетной записи хранения Azure Data Lake, которую мы только что подключили. Имя папки передается в виде переменной. Мы будем циклически перебирать все формы в указанной папке и извлекать из них пары "ключ — значение". 

1. Создайте новую записную книжку и назовите ее **ScoreFormRecognizer**. 
1. Выполните записные книжки **Settings** и **MountDataLake**.

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Затем добавьте приведенный ниже код, который вызывает API [анализа](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. В следующей ячейке мы будем получать результаты извлечения пар "ключ — значение". В этой ячейке выводится результат. Нам нужно, чтобы результат в формате JSON обрабатывался в нашей базе данных SQL Azure или Cosmos DB. Поэтому он будет записан в JSON-файл. Имя выходного файла будет представлять собой имя оцененного файла, сцепленное с "_output.json". Файл будет храниться в той же папке, что и исходный файл.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Выполните процедуру записи файла в новой ячейке:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>Автоматизация обучения и оценки с помощью Фабрики данных Azure

Заключительный этап — настройка службы "Фабрика данных Azure" (ADF) для автоматизации обучения и оценки. Сначала выполните действия, описанные в разделе [Создание фабрики данных](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). После создания ресурса ADF необходимо создать три конвейера: один для обучения и два для оценки (см. описание ниже).

### <a name="training-pipeline"></a>Конвейер обучения

Первое действие в конвейере обучения — это запрос на поиск для чтения и возврата значений из таблицы параметризации в базе данных SQL Azure. Так как все наборы данных для обучения будут находиться в одной учетной записи хранения и контейнере (но, возможно, в разных папках), мы сохраним атрибут **First row only** (Только первая строка) значения по умолчанию в параметрах действия поиска. Для каждого типа формы, используемого для обучения модели, мы применим все файлы **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Конвейер обучения в фабрике данных":::

Хранимая процедура принимает два параметра: **model_id** и **form_batch_group_id**. Код для возврата идентификатора модели из записной книжки Databricks — `dbutils.notebook.exit(model_id)`, а код для чтения кода в действии хранимой процедуры в фабрике данных — `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`.

### <a name="scoring-pipelines"></a>Конвейеры оценки

Чтобы извлечь пары "ключ — значение", мы проверим все папки в таблице параметризации и для каждой папки извлечем пары "ключ — значение" из всех содержащихся в ней файлов. Сейчас ADF не поддерживает вложенные циклы ForEach. Поэтому мы создадим два конвейера. Первый конвейер выполняет запрос на поиск в таблице параметризации и передает список папок в качестве параметра во второй конвейер.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="первый конвейер оценки в фабрике данных":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="первый конвейер оценки в фабрике данных, сведения":::

Второй конвейер с помощью действия GetMeta извлекает список файлов в папке и передает его в качестве параметра в записную книжку оценки в Databricks.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="второй конвейер оценки в фабрике данных":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="второй конвейер оценки в фабрике данных, сведения":::

### <a name="specify-a-degree-of-parallelism"></a>Указание степени параллелизма

В конвейерах обучения и оценки можно указать степень параллелизма для одновременной обработки нескольких форм.

Чтобы задать степень параллелизма в конвейере ADF, сделайте следующее:

* Выберите действие ForEach.
* Снимите флажок **Последовательно**.
* Задайте степень параллелизма в текстовом поле **Batch count** (Число пакетов). Для оценки рекомендуется использовать не более 15 пакетов.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Настройка параллелизма для действия оценки в ADF":::

## <a name="how-to-use"></a>Использование

Теперь у вас есть автоматизированный конвейер для оцифровки невыполненной работы с формами и последующего выполнения аналитики на основе полученных данных. При добавлении новых форм уже изученного типа в существующую папку хранилища просто перезапустите конвейеры оценки. Они будут обновлять все выходные файлы, включая выходные файлы для новых форм. 

Если вы добавляете новые формы нового типа, в соответствующий контейнер необходимо также передать набор данных для обучения. Затем добавьте новую строку в таблицу параметризации, указав расположения новых документов и набор данных для обучения. Введите значение -1 для **model_ID**, чтобы указать, что для этих форм нужно обучить новую модель. Затем запустите конвейер обучения в ADF. Он считает данные из таблицы, обучит модель и перезапишет идентификатор модели в таблице. Затем вы можете вызвать конвейеры оценки, чтобы начать запись выходных файлов.

## <a name="next-steps"></a>Следующие шаги

При работе с этим руководством вы настроили конвейеры Фабрики данных Azure, чтобы активировать обучение и запуск моделей Распознавателя документов для оцифровки большого объема невыполненной работы с файлами. Теперь изучите API "Распознаватель документов", чтобы узнать о других его возможностях.

* [REST API "Распознаватель документов"](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
