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
> В ближайшее время этот пакет SDK будет снят с учета. Этот пакет SDK по-прежнему подходит для разработчиков, отслеживающих смещение данных в моделях, но большинство разработчиков должны использовать упрощенный [мониторинг данных с Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

Из этой статьи вы узнаете, как получить данные входной модели из Машинное обучение Azure, развернутого в кластере Azure Kubernetes (AKS) в хранилище BLOB-объектов Azure. 

После включения сбора данных собранные данные помогут вам:
* [Отслеживание смещения данных](how-to-monitor-data-drift.md) по мере того, как рабочие данные попадают в модель

* Принимать более взвешенные решения о необходимости повторного обучения и оптимизации модели

* Повторно обучить модель с использованием собранных данных

## <a name="what-is-collected-and-where-does-it-go"></a>Какие данные собираются и куда они попадают?

Вы можете собирать следующие данные:
* **Входные** данные модели от веб-служб, развернутых в кластере Azure Kubernetes (AKS) (голосовые данные, видео и изображения **не** собираются) 
  
* Прогнозирование моделей с использованием входных данных в рабочей среде

> [!Note]
> Предварительная статистическая обработка и предварительные вычислительные операции с этими данными сейчас не выполняются в рамках службы.   

Выходные данные сохраняются в BLOB-объекте Azure. Так как данные добавляются в BLOB-объект Azure, вы можете выбрать привычные инструменты для проведения анализа. 

Для пути к выходным данным в BLOB-объекте используется следующий синтаксис:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> В версиях пакета SDK до `0.1.0a16` аргумент `designation` был назван `identifier`. Если код был разработан с использованием более ранней версии, необходимо соответствующим образом обновить.

## <a name="prerequisites"></a>предварительным требованиям

- Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Попробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) прямо сейчас.

- Должны быть установлены рабочая область машинного обучения Azure, локальный каталог со сценариями и пакет SDK машинного обучения Azure для Python. Узнайте, как выполнить эти предварительные требования с помощью документа [Настройка среды разработки](how-to-configure-environment.md) .

- Обученная модель машинного обучения для развертывания в службе Azure Kubernetes (AKS). Если у вас ее нет, см. Руководство [обучение модели классификации изображений](tutorial-train-models-with-aml.md)

- Кластер Службы Azure Kubernetes. Сведения о создании и развертывании в одном из них см. в разделе [развертывание и размещение](how-to-deploy-and-where.md) документа.

- [Настройка среды](how-to-configure-environment.md) и установка [пакета SDK для мониторинга](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Включение сбора данных
Сбор данных можно включить независимо от модели, развертываемой с помощью Машинное обучение Azure или других средств. 

Чтобы включить сбор данных, выполните следующие действия:

1. Открытие файла оценки

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
    
    *Идентификатор* впоследствии используется для создания структуры папок в большом двоичном объекте, он может использоваться для разделения "необработанных" данных относительно "обработанных".

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

5. Сведения о создании нового образа и развертывании службы см. в разделе [развертывание и размещение](how-to-deploy-and-where.md) документа.


Если в **файле среды** и **файле оценки** уже указана служба с установленными зависимостями, вы можете включить сбор данных следующим образом:

1. Переход к [машинное обучение Azure Studio](https://ml.azure.com)

1. Откройте рабочую область

1. Перейдите в раздел **развертывания** -> **выберите Сервис** -> **изменить** .

   ![Изменение службы](media/how-to-enable-data-collection/EditService.PNG)

1. В окне **Дополнительные параметры**выберите **включить сбор данных модели** .

    [![Установка флажка сбора данных](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   В этом окне также можно выбрать "включить диагностику Appinsights", чтобы отвести наблюдение за работоспособностью службы.

1. Выберите **Обновление** , чтобы применить изменение


## <a name="disable-data-collection"></a>Отключение сбора данных
Вы можете остановить сбор данных в любое время. Чтобы отключить сбор данных, используйте код Python или Машинное обучение Azure Studio.

+ Вариант 1. Отключение в Машинное обучение Azure Studio: 
  1. Вход в [машинное обучение Azure Studio](https://ml.azure.com)

  1. Откройте рабочую область

  1. Перейдите в раздел **развертывания** -> **выберите Сервис** -> **изменить** .

     [![Параметр "Изменить"](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. В окне **Дополнительные параметры**снимите флажок **включить сбор данных модели** .

     [![Снятие флажка сбора данных](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Выберите **Обновление** , чтобы применить изменение

  Вы также можете получить доступ к этим параметрам в рабочей области в [машинное обучение Azure Studio](https://ml.azure.com).

+ Вариант 2 — отключение сбора данных с помощью Python:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Проверка и анализ данных
Для анализа данных, собранных в большом двоичном объекте Azure, можно выбрать любой предпочитаемый инструмент.

Чтобы быстро получить доступ к данным большого двоичного объекта:

1. Вход в [машинное обучение Azure Studio](https://ml.azure.com)

1. Откройте рабочую область
1. Щелкните **хранилище**

    [![Хранилище](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Используйте для пути к выходным данным в большом двоичном объекте следующий синтаксис:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Анализ данных модели с помощью Power BI

1. Скачать и открыть [Power BI Desktop](https://www.powerbi.com)

1. Выберите **получить данные** и щелкните [**хранилище BLOB-объектов Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources) .

    [![Настройка большого двоичного объекта в Power BI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Добавьте имя учетной записи хранения и введите ключ к хранилищу данных. Эти сведения можно найти в **параметрах** большого двоичного объекта > > ключи доступа.

1. Выберите контейнер **modelData** и щелкните " **изменить** ".

    [![Навигатор в Power BI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. В редакторе запросов щелкните в столбце "Имя" и добавьте учетную запись хранения 1. Введите путь к модели в фильтре. Примечание. Если вы хотите искать только файлы за определенный год или месяц, просто разверните путь фильтра. Например, просто взгляните на данные в марте:/моделдата/субскриптионид >/ресаурцеграупнаме >/воркспаценаме >/вебсервиценаме >/моделнаме >/modelversion >/designation >/Year >/3

1. Отфильтруйте по **имени** для отображения нужных данных. Если вы сохранили **прогнозы** и **входные данные**, необходимо создать запрос для каждого

1. Щелкните двойную стрелку в сторону столбца **содержимого** , чтобы объединить файлы.

    [![Содержимое в Power BI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Нажмите кнопку ОК, и данные будут предварительно загружены

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Теперь можно нажать кнопку **Закрыть и применить** .

1.  Если вы добавили входные данные и прогнозы, таблицы будут автоматически сопоставляться по **коду запроса** .

1. Приступите к созданию пользовательских отчетов на данных модели


### <a name="analyzing-model-data-using-databricks"></a>Анализ данных модели с помощью Databricks

1. Создание [рабочей области "кирпичи](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) "

1. Переход к рабочей области "кирпичы"

1. В рабочей области "сведения о модулях **данных" выберите Отправить данные** .

    [![Отправка базы данных](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Создание новой таблицы и выбор **других источников данных** — > хранилище BLOB-объектов Azure — > создать таблицу в записной книжке

    [![Таблица базы данных](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Обновите расположение данных. Пример:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Выполните действия, описанные в шаблоне, чтобы просмотреть и проанализировать данные.

## <a name="example-notebook"></a>Пример записной книжки

В записной книжке [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) демонстрируются понятия, описанные в данной статье.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
