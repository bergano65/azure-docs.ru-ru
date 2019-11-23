---
title: Сбор данных рабочих моделей
titleSuffix: Azure Machine Learning
description: Сведения о сборе входных данных модели машинного обучения Azure в хранилище BLOB-объектов Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406452"
---
# <a name="collect-data-for-models-in-production"></a>Сбор данных для моделей в рабочей среде
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> This SDK is retiring soon. This SDK is still appropriate for developers monitoring data drift in models but most developers should use the simplified [data monitoring with Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

In this article, you can learn how to collect input model data from Azure Machine Learning you've deployed into Azure Kubernetes Cluster (AKS) into an Azure Blob storage. 

После включения сбора данных собранные данные помогут вам:
* [Monitor data drifts](how-to-monitor-data-drift.md) as production data enters your model

* Принимать более взвешенные решения о необходимости повторного обучения и оптимизации модели

* Повторно обучить модель с использованием собранных данных

## <a name="what-is-collected-and-where-does-it-go"></a>Какие данные собираются и куда они попадают?

Вы можете собирать следующие данные:
* **Входные** данные модели от веб-служб, развернутых в кластере Azure Kubernetes (AKS) (голосовые данные, видео и изображения **не** собираются) 
  
* Model predictions using production input data

> [!Note]
> Предварительная статистическая обработка и предварительные вычислительные операции с этими данными сейчас не выполняются в рамках службы.   

Выходные данные сохраняются в BLOB-объекте Azure. Так как данные добавляются в BLOB-объект Azure, вы можете выбрать привычные инструменты для проведения анализа. 

Для пути к выходным данным в BLOB-объекте используется следующий синтаксис:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> In versions of the SDK prior to `0.1.0a16` the `designation` argument was named `identifier`. If your code was developed with an earlier version, you will need to update accordingly.

## <a name="prerequisites"></a>Технические условия

- Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) прямо сейчас.

- Должны быть установлены рабочая область машинного обучения Azure, локальный каталог со сценариями и пакет SDK машинного обучения Azure для Python. Learn how to get these prerequisites using the [How to configure a development environment](how-to-configure-environment.md) document

- Обученная модель машинного обучения для развертывания в службе Azure Kubernetes (AKS). If you don't have one, see the [train image classification model](tutorial-train-models-with-aml.md) tutorial

- Кластер Службы Azure Kubernetes. For information on how to create and deploy to one, see the [How to deploy and where](how-to-deploy-and-where.md) document

- [Set up your environment](how-to-configure-environment.md) and install the [Monitoring SDK](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Включение сбора данных
Data collection can be enabled regardless of the model being deployed through Azure Machine Learning or other tools. 

Чтобы включить сбор данных, выполните следующие действия:

1. Open the scoring file

1. Добавьте [следующий код](https://aka.ms/aml-monitoring-sdk) в начало файла:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Объявите переменные коллекции данных в функции `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    Параметр *CorrelationId* является необязательным, и если он не требуется для модели, указывать его не нужно. Указание идентификатора корреляции упрощает сопоставление с другими данными. (К ним относятся LoanNumber, CustomerId и т. д.)
    
    *Identifier* is later used for building the folder structure in your Blob, it can be used to divide “raw” data versus “processed”

3.  Добавьте в функцию `run(input_df)` следующие строки кода.

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Сбор данных **не** **включается** автоматически при развертывании службы в AKS, поэтому вам необходимо обновить файл конфигурации, например: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Также можно включить AppInsights для мониторинга служб, изменив эту конфигурацию:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. To create a new image and deploy the service, see the [How to deploy and where](how-to-deploy-and-where.md) document


Если в **файле среды** и **файле оценки** уже указана служба с установленными зависимостями, вы можете включить сбор данных следующим образом:

1. Go to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace

1. Go to **Deployments** -> **Select service** -> **Edit**

   ![Изменение службы](media/how-to-enable-data-collection/EditService.PNG)

1. In **Advanced Settings**, select **Enable Model data collection**

    [![Установка флажка сбора данных](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In this window, you can also choose to "Enable Appinsights diagnostics" to track the health of your service

1. Select **Update** to apply the change


## <a name="disable-data-collection"></a>Отключение сбора данных
Вы можете остановить сбор данных в любое время. Use Python code or Azure Machine Learning studio to disable data collection.

+ Option 1 - Disable in Azure Machine Learning studio: 
  1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

  1. Open your workspace

  1. Go to **Deployments** -> **Select service** -> **Edit**

     [![Параметр "Изменить"](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. In **Advanced Settings**, deselect **Enable Model data collection**

     [![Снятие флажка сбора данных](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Select **Update** to apply the change

  You can also access these settings in your workspace in [Azure Machine Learning studio](https://ml.azure.com).

+ Вариант 2 — отключение сбора данных с помощью Python:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Проверка и анализ данных
Для анализа данных, собранных в большом двоичном объекте Azure, можно выбрать любой предпочитаемый инструмент.

Чтобы быстро получить доступ к данным большого двоичного объекта:

1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace
1. Click on **Storage**

    [![Хранилище](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Используйте для пути к выходным данным в большом двоичном объекте следующий синтаксис:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Анализ данных модели с помощью Power BI

1. Download and Open [Power BI Desktop](https://www.powerbi.com)

1. Select **Get Data** and click on [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![Настройка большого двоичного объекта в Power BI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Добавьте имя учетной записи хранения и введите ключ к хранилищу данных. You can find this information in your blob's **Settings** >> Access keys

1. Select the container **modeldata** and click on **Edit**

    [![Навигатор в Power BI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. В редакторе запросов щелкните в столбце "Имя" и добавьте учетную запись хранения 1. Введите путь к модели в фильтре. Примечание. Если вы хотите искать только файлы за определенный год или месяц, просто разверните путь фильтра. For example, just look into March data: /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/designation>/year>/3

1. Отфильтруйте по **имени** для отображения нужных данных. If you stored **predictions** and **inputs**, you'll need to create a query for each

1. Click on the double arrow aside the **Content** column to combine the files

    [![Содержимое в Power BI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Click OK and the data will preload

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. You can now click **Close and Apply**

1.  If you added inputs and predictions, your tables will automatically correlate by **RequestId**

1. Start building your custom reports on your model data


### <a name="analyzing-model-data-using-databricks"></a>Анализ данных модели с помощью Databricks

1. Create a [Databricks workspace](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Go to your Databricks workspace

1. In your databricks workspace, select **Upload Data**

    [![Отправка базы данных](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Create New Table and select **Other Data Sources** -> Azure Blob Storage -> Create Table in Notebook

    [![Таблица базы данных](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Обновите расположение данных. Вот пример:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Follow the steps on the template in order to view and analyze your data

## <a name="example-notebook"></a>Пример записной книжки

В записной книжке [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) демонстрируются понятия, описанные в данной статье.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
