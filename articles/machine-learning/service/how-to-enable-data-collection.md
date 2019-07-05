---
title: Сбор данных рабочих моделей
titleSuffix: Azure Machine Learning service
description: Сведения о сборе входных данных модели машинного обучения Azure в хранилище BLOB-объектов Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: f596fb3a066017f0236de5b79586891dd21efb11
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443977"
---
# <a name="collect-data-for-models-in-production"></a>Сбор данных для моделей в рабочей среде

Из этой статьи вы узнаете, как собирать входные данные модели от служб машинного обучения Azure, которые были развернуты в кластере Azure Kubernetes в хранилище BLOB-объектов Azure. 

После включения сбора данных собранные данные помогут вам:
* [Мониторинг данных drifts](how-to-monitor-data-drift.md) рабочими данными, вводимыми модели

* Принимать более взвешенные решения о необходимости повторного обучения и оптимизации модели

* Повторно обучить модель с использованием собранных данных

## <a name="what-is-collected-and-where-does-it-go"></a>Какие данные собираются и куда они попадают?

Вы можете собирать следующие данные:
* **Входные** данные модели от веб-служб, развернутых в кластере Azure Kubernetes (AKS) (голосовые данные, видео и изображения **не** собираются) 
  
* Прогнозирование моделей с использованием входных данных рабочей среды.

> [!Note]
> Предварительная статистическая обработка и предварительные вычислительные операции с этими данными сейчас не выполняются в рамках службы.   

Выходные данные сохраняются в BLOB-объекте Azure. Так как данные добавляются в BLOB-объект Azure, вы можете выбрать привычные инструменты для проведения анализа. 

Для пути к выходным данным в BLOB-объекте используется следующий синтаксис:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Технические условия

- Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

- Должны быть установлены: рабочая область службы "Машинное обучение Azure", локальный каталог со сценариями и пакет SDK Машинного обучения Azure для Python. Дополнительные сведения о получении этих необходимых компонентов см. в документе [Настройка среды разработки](how-to-configure-environment.md).

- Обученная модель машинного обучения для развертывания в службе Azure Kubernetes (AKS). Если у вас ее нет, обратитесь к [руководству по обучению модели классификации изображений](tutorial-train-models-with-aml.md).

- Кластер Службы Azure Kubernetes. Сведения о том, как создать и развернуть модель на кластере, см. в документе [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md).

- [Настройте свою среду](how-to-configure-environment.md) и установите [пакет SDK для мониторинга](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Включение сбора данных
Сбор данных можно включить независимо от модели, развертываемой с помощью службы машинного обучения Azure или других средств. 

Чтобы включить сбор данных, выполните следующие действия:

1. Откройте файл оценки. 

1. Добавьте [следующий код](https://aka.ms/aml-monitoring-sdk) в начало файла:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Объявите переменные коллекции данных в функции `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    Параметр *CorrelationId* является необязательным, и если он не требуется для модели, указывать его не нужно. Указание идентификатора корреляции упрощает сопоставление с другими данными. (Примеры: LoanNumber, CustomerId и т. д.)
    
    *Идентификатор* используется в дальнейшем для создания структуры папок BLOB-объекта. Его можно использовать, чтобы отделить "необработанные" данные от "обработанных".

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

5. Сведения о том, как создать образ и развернуть службу, см. в документе [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md).


Если в **файле среды** и **файле оценки** уже указана служба с установленными зависимостями, вы можете включить сбор данных следующим образом:

1. Перейдите на [портал Azure](https://portal.azure.com).

1. Перейдите в рабочую область.

1. Выберите **Развертывания** -> **Выбор службы** -> **Изменить**.

   ![Изменение службы](media/how-to-enable-data-collection/EditService.PNG)

1. В разделе **Дополнительные параметры** отмените выбор параметра **Включить сбор данных модели**. 

    [![Установка флажка сбора данных](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   В этом окне вы также можете установить параметр "Включить диагностику AppInsights", чтобы отслеживать работоспособность своей службы.  

1. Чтобы применить изменение, нажмите кнопку **Обновить**.


## <a name="disable-data-collection"></a>Отключение сбора данных
Вы можете остановить сбор данных в любое время. Отключение сбора данных с помощью кода Python или портала Azure.

+ Вариант 1 — отключение сбора данных на портале Azure: 
  1. Войдите на [портал Azure](https://portal.azure.com).

  1. Перейдите в рабочую область.

  1. Выберите **Развертывания** -> **Выбор службы** -> **Изменить**.

     [![Параметр "Изменить"](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. В разделе **Дополнительные параметры** отмените выбор параметра **Включить сбор данных модели**. 

     [![Снятие флажка сбора данных](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Чтобы применить изменение, нажмите кнопку **Обновить**.

+ Вариант 2 — отключение сбора данных с помощью Python:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Проверка и анализ данных
Для анализа данных, собранных в большом двоичном объекте Azure, можно выбрать любой предпочитаемый инструмент. 

Чтобы быстро получить доступ к данным большого двоичного объекта:
1. Войдите на [портал Azure](https://portal.azure.com).

1. Перейдите в рабочую область.
1. Выберите **Хранилище**.

    [![Хранилище](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Используйте для пути к выходным данным в большом двоичном объекте следующий синтаксис:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Анализ данных модели с помощью Power BI

1. Скачайте [PowerBi Desktop](https://www.powerbi.com) и откройте его.

1. Выберите **Получить данные**, а затем [**Хранилище BLOB-объектов Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Настройка большого двоичного объекта в Power BI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Добавьте имя учетной записи хранения и введите ключ к хранилищу данных. Эти сведения можно найти в разделе **Параметры** > "Ключи доступа" большого двоичного объекта. 

1. Выберите контейнер **modeldata** и щелкните **Изменить**. 

    [![Навигатор в Power BI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. В редакторе запросов щелкните в столбце "Имя" и добавьте учетную запись хранения 1. Введите путь к модели в фильтре. Примечание. Если вы хотите искать только файлы за определенный год или месяц, просто разверните путь фильтра. Например, взгляните на данные за март: /modeldata/ИД_подписки>/имя_группы_ресурсов>/имя_раочей_области>/имя_веб-службы>/имя_модели>/версия_модели>/идентификатор>/год>/3.

1. Отфильтруйте по **имени** для отображения нужных данных. Если вы сохранили **прогнозы** и **входные данные**, вам потребуется создать отдельные запросы для них.

1. Щелкните двойную стрелку рядом со столбцом **Содержимое**, чтобы объединить файлы. 

    [![Содержимое в Power BI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Нажмите кнопку "ОК", после чего данные будут предварительно загружены.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Теперь можно щелкнуть **Close and Apply** (Закрыть и применить).

1.  Если вы добавили входные данные и прогнозы, таблицы будут автоматически сопоставляться по идентификатору **RequestId**.

1. Приступите к созданию пользовательских отчетов о данных модели.


### <a name="analyzing-model-data-using-databricks"></a>Анализ данных модели с помощью Databricks

1. Создайте [рабочую область Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Перейдите к рабочей области Databricks. 

1. В рабочей области Databricks выберите **Отправить данные**.

    [![Отправка базы данных](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Создайте новую таблицу и выберите **Другие источники данных** -> "Хранилище BLOB-объектов Azure" -> "Create Table in Notebook" (Создать таблицу в записной книжке).

    [![Таблица базы данных](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Обновите расположение данных. Вот пример:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Следуйте инструкциям в шаблоне, чтобы просмотреть и проанализировать данные. 

## <a name="example-notebook"></a>Пример записной книжки

В записной книжке [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) демонстрируются понятия, описанные в данной статье.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
