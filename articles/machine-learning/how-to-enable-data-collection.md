---
title: Сбор данных рабочих моделей
titleSuffix: Azure Machine Learning
description: Узнайте, как собирать данные модели ввода машинНого обучения Azure Machine Learning в хранилище Azure Blob.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771399"
---
# <a name="collect-data-for-models-in-production"></a>Сбор данных для моделей в рабочей среде

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Мониторинг машинного обучения Azure SDK скоро будет отменен. SDK по-прежнему подходит для разработчиков, которые в настоящее время используют SDK для мониторинга дрейфа данных в моделях. Но для новых клиентов мы рекомендуем использовать упрощенный [мониторинг данных с помощью Application Insights.](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)

В этой статье показано, как собирать данные входиной модели из машинного обучения Azure. Он также показывает, как развертывать входные данные в кластер Службы Azure Kubernetes (AKS) и хранить выходные данные в хранилище Azure Blob.

После включения сбора данных, которые вы собираете, вы можете:

* [Мониторинг данных дрейфует](how-to-monitor-data-drift.md) по мере ввода производственных данных в модель.

* Принимайте более обоснованные решения о том, когда следует переквалифицировать или оптимизировать модель.

* Переподготовка модели с помощью собранных данных.

## <a name="what-is-collected-and-where-it-goes"></a>Что собрано и куда он идет

Вы можете собирать следующие данные:

* Модель входных данных веб-служб, развернутых в кластере AKS. Голосовое аудио, изображения и видео *не* собираются.
  
* Прогнозирование моделей с использованием входных данных рабочей среды.

>[!NOTE]
> Предварительная сборизация и предварительные расчеты по этим данным в настоящее время не являются частью службы сбора.

Выход сохраняется в хранилище Blob. Поскольку данные добавляются в хранилище Blob, вы можете выбрать свой любимый инструмент для выполнения анализа.

Для пути к выходным данным в BLOB-объекте используется следующий синтаксис:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> В версиях SDK Машинного Обучения Azure для Python раньше версии 0.1.0a16 `designation` аргумент называется `identifier`. Если вы разработали свой код с более ранней версией, вам нужно обновить его соответствующим образом.

## <a name="prerequisites"></a>Предварительные требования

- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://aka.ms/AMLFree), прежде чем начинать работу.

- Необходимо установить рабочее пространство AzureMachine Learning, локальный каталог, содержащий скрипты, и SDK для Python. Чтобы узнать, как установить их, см. [Как настроить среду разработки.](how-to-configure-environment.md)

- Для развертывания в AKS нужна обученная модель машинного обучения. Если у вас нет модели, [Train image classification model](tutorial-train-models-with-aml.md) см.

- Вам нужен кластер AKS. Для получения информации о том, как создать один и развернуть его, [см. Как развернуть и где](how-to-deploy-and-where.md).

- [Навлажьте среду](how-to-configure-environment.md) и установите [SDK мониторинга машинного обучения Azure.](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Включение сбора данных

Вы можете включить сбор данных независимо от модели, которую вы развертываете с помощью машинного обучения Azure или других инструментов.

Для обеспечения сбора данных необходимо:

1. Откройте файл оценки.

1. Добавьте [следующий код](https://aka.ms/aml-monitoring-sdk) в начало файла:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Объявите переменные коллекции данных в функции `init`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* является необязательным параметром. Вам не нужно использовать его, если модель не требует этого. Использование *CorrelationId* помогает вам легче картографируть другие данные, такие как *LoanNumber* или *CustomerId.*
    
    Параметр *идентификатора* позже используется для создания структуры папки в каплей. Его можно использовать для дифференциации необработанных данных от обработанных данных.

1. Добавьте в функцию `run(input_df)` следующие строки кода.

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Сбор данных *не* **устанавливается** автоматически при развертывании службы в AKS. Обновление файла конфигурации, как в следующем примере:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Вы также можете включить Application Insights для мониторинга служб, изменив эту конфигурацию:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Чтобы создать новое изображение и развернуть модель машинного обучения, [см. Как развернуть и где](how-to-deploy-and-where.md).

Если у вас уже есть служба с зависимостями, установленными в файле среды и файле скоринга, включите сбор данных, выполнить следующие действия:

1. Перейти к [Лазурное машинное обучение](https://ml.azure.com).

1. Перейдите в рабочую область.

1. Выберите развертывание**Выберите службу** > **Отсечения** **Deployments** > .

   ![Оторитете службу](././media/how-to-enable-data-collection/EditService.PNG)

1. В **расширенных настройках**выберите **сбор данных Enable Model.**

    [![Выберите сбор данных](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Вы также можете выбрать **диагностику Enable AppInsights** для отслеживания работоспособности службы.

1. Выберите **обновление,** чтобы применить изменения.

## <a name="disable-data-collection"></a>Отключение сбора данных

Вы можете прекратить сбор данных в любое время. Используйте код Python или Azure Machine Learning для отсеивателя сбора данных.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Вариант 1 - Отстеутый сбор данных в машинном обучении Azure

1. Вопием в [Azure Machine Learning](https://ml.azure.com).

1. Перейдите в рабочую область.

1. Выберите развертывание**Выберите службу** > **Отсечения** **Deployments** > .

   [![Выберите опцию «Отобрагивать»](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. В **расширенных настройках**очистите **сбор данных Enable Model.**

    [![Очистить флажок для сбора данных](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Чтобы применить изменение, нажмите кнопку **Обновить**.

Вы также можете получить доступ к этим параметрам в рабочей области в [Azure Machine Learning.](https://ml.azure.com)

### <a name="option-2---use-python-to-disable-data-collection"></a>Вариант 2 - Использование Python для отскакивки сбора данных

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Проверка и анализ данных

Вы можете выбрать предпочтительный инструмент для анализа данных, собранных в вашем хранилище Blob.

### <a name="quickly-access-your-blob-data"></a>Быстрый доступ к данным капли

1. Вопием в [Azure Machine Learning](https://ml.azure.com).

1. Перейдите в рабочую область.

1. Выберите пункт **Служба хранилища**.

    [![Выберите опцию хранения данных](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Следуйте по пути к выходным данным капли с помощью этого синтаксиса:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Анализ данных модели с помощью Power BI

1. Скачать и открыть [Power BI Desktop](https://www.powerbi.com).

1. Выберите **Get Data** и выберите [**хранилище Azure Blob.**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![Мощность BI капля установки](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Добавьте имя учетной записи хранения и введите ключ к хранилищу данных. Вы можете найти эту информацию, выбрав > **ключи доступа** **к настройкам**в каплей.

1. Выберите контейнер **данных модели** и **отображайте edit.**

    [![Мощность BI Навигатор](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. В редакторе запроса нажмите под столбец **«Имя»** и добавьте учетную запись хранения.

1. Введите путь модели в фильтр. Если вы хотите заглянуть только в файлы из определенного года или месяца, просто расширьте путь фильтра. Например, чтобы посмотреть только в мартовские данные, используйте этот путь фильтра:

   /modeldata/\<subscriptionid\<>/\<resourcegroupname>/\<workspacename>/ webservicename>/\<modelname>/\<modelversion>/\<designation>/year\<>/3

1. Фильтруй данные, имеющие отношение к вам, на основе значений **имен.** При хранении прогнозов и входов необходимо создать запрос для каждого из них.

1. Выберите двойные стрелки вниз рядом с столбцей **Содержимого,** чтобы объединить файлы.

    [![Мощность BI Содержание](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Нажмите кнопку **ОК**. Предзагрузок данных.

    [![Файлы комбайна Power BI](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Выберите **Закрыть и применить**.

1. При добавлении входных данных и прогнозов ваши таблицы автоматически заказываются значениями **RequestId.**

1. Приступите к созданию пользовательских отчетов о данных модели.

### <a name="analyze-model-data-using-azure-databricks"></a>Анализ данных модели с помощью Azure Databricks

1. Создайте [рабочее пространство Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Перейдите к рабочей области Databricks.

1. В рабочем пространстве Databricks выберите **Загрузить данные.**

    [![Выбор варианта загрузки данных Databricks](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Выберите **Создать новую таблицу** и выберите **другие источники** > данных**Azure Blob Storage** > **Создать таблицу в блокноте.**

    [![Создание таблицы Databricks](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Обновление местоположения данных. Например:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Установка Databricks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Выполните действия по шаблону, чтобы просмотреть и проанализировать данные.
