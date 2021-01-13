---
title: Создание эксперимента автоматического машинного обучения
titleSuffix: Azure Machine Learning
description: Узнайте, как определить источники данных, расчеты и параметры конфигурации для автоматических экспериментов машинного обучения.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperf-fy21q1, automl
ms.openlocfilehash: f2170aad9bc0218d39244d08f5cc838235f8fee9
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134370"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Настройка экспериментов автоматизированного машинного обучения на Python


В этом руководстве вы узнаете, как определить различные параметры конфигурации для экспериментов автоматизированного машинного обучения с помощью [пакета SDK для Машинного обучения Azure](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Автоматическое машинное обучение выбирает алгоритм и гиперпараметры, а также создает модель, готовую для развертывания. Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения.

Примеры автоматизированных экспериментов машинного обучения см. в разделе [учебник. Обучение модели классификации с помощью автоматического машинного обучения](tutorial-auto-train-models.md) или [обучение моделей с помощью автоматизированного машинного обучения в облаке](how-to-auto-train-remote.md).

Возможности настройки, доступные в автоматическом машинном обучении:

* выбор типа эксперимента: классификация, регрессия или прогнозирование временных рядов;
* источник данных, форматы и получение данных;
* выбор целевого объекта вычислений (локальный или удаленный);
* настройка параметров эксперимента автоматического машинного обучения;
* выполнение эксперимента автоматического машинного обучения;
* Изучение метрик модели
* регистрация и развертывание модели.

Если вы не хотите писать код, вы также можете [создавать эксперименты автоматизированного машинного обучения в Студии машинного обучения Azure](how-to-use-automated-ml-for-ml-models.md).

## <a name="prerequisites"></a>Предварительные требования

Для этой статьи требуется: 
* Рабочая область машинного обучения Azure. Дополнительные сведения см. в [инструкциях по созданию рабочей области Машинного обучения Azure](how-to-manage-workspace.md).

* Установленный пакет SDK для Машинное обучение Azure Python.
    Для установки пакета SDK можно либо 
    * Создайте вычислительный экземпляр, который автоматически устанавливает пакет SDK и предварительно настроен для рабочих процессов машинного обучения. Дополнительные сведения см. [в разделе создание машинное обучение Azure вычислительных экземпляров и управление ими](how-to-create-manage-compute-instance.md) . 

    * [Установите `automl` пакет самостоятельно](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment), который включает установку пакета SDK [по умолчанию](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py#default-install) .

## <a name="select-your-experiment-type"></a>Выбор типа эксперимента

Прежде чем начать эксперимент, следует определить тип задачи машинного обучения, которую необходимо решить. Автоматическое машинное обучение поддерживает типы задач `classification` , `regression` и `forecasting` . Дополнительные сведения о [типах задач](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast).

В следующем коде `task` `AutoMLConfig` для указания типа эксперимента используется параметр в конструкторе `classification` .

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Источник данных и формат

Автоматическое машинное обучение поддерживает данные, находящиеся на локальном компьютере или в облаке в хранилище BLOB-объектов Azure. Данные можно считать в **кадр данных Pandas** или **табличный набор данных Машинного обучения Azure**. [Дополнительные сведения о наборах данных](how-to-create-register-datasets.md).

Требования к обучающим данным:
- Данные должны иметь табличный формат.
- Прогнозируемое значение (целевой столбец) должно присутствовать в данных.

**Для удаленных экспериментов** обучающие данные должны быть доступны из удаленного вычислений. При работе с удаленным вычислением AutoML принимает только [табличные наборы данных для Машинного обучения Azure](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py). 

Наборы данных Машинного обучения Azure предоставляют следующие функциональные возможности:

* Легко переносите данные из статических файлов или URL-источников в рабочую область.
* предоставление доступа к данным обучающим скриптам в облачной вычислительной среде. Пример [](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) использования `Dataset` класса для подключения данных к удаленному целевому объекту вычислений см. в разделе обучение с помощью наборов данных.

Следующий код создает Табулардатасет на основе URL-адреса. См. статью [Создание табулардатасетс](how-to-create-register-datasets.md#create-a-tabulardataset) для примеров кода, посвященных созданию наборов данных из других источников, таких как локальные файлы и хранилища данных.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Для локальных вычислительных экспериментов** рекомендуется Pandas кадры данных для ускорения обработки.

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>Обучающие, проверочные и проверочные данные

Можно указать отдельные **наборы для обучения и проверки** непосредственно в `AutoMLConfig` конструкторе. Дополнительные сведения о [настройке разбиения данных и перекрестной проверки](how-to-configure-cross-validation-data-splits.md) для экспериментов аутомл. 

Если не указать явно `validation_data` `n_cross_validation` параметр или, аутомл применяет методы по умолчанию для определения способа проверки. Это определение зависит от количества строк в наборе данных, назначенном вашему `training_data` параметру. 

|&nbsp;Размер данных для обучения &nbsp;| Метод проверки |
|---|-----|
|**Более &nbsp; &nbsp; 20 000 &nbsp; строк**| Применяется разбиение данных по обучению и проверке. Значение по умолчанию — использовать 10% начального набора данных для обучения в качестве набора проверки. В свою очередь, этот набор проверки используется для вычисления метрик.
|**Меньше &nbsp; &nbsp; 20 000 &nbsp; строк**| Применяется подход перекрестной проверки. Количество сверток по умолчанию зависит от количества строк. <br> **Если набор данных имеет менее 1 000 строк**, то используется 10 сверток. <br> **Если строки находятся в диапазоне от 1 000 до 20 000**, то используются три свертывания.

В настоящее время необходимо предоставить собственные **тестовые данные** для оценки модели. Пример кода для перевода собственных тестовых данных для оценки модели см. в разделе **Test** [этой записной книжки Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).

## <a name="compute-to-run-experiment"></a>Объект вычислений для выполнения эксперимента

Затем следует определить, где модель будет обучаться. Обучающий эксперимент для автоматизированного машинного обучения можно запустить на следующих вычислительных ресурсах. Узнайте о [достоинствах и недостатках локальных и удаленных вычислительных ресурсов](concept-automated-ml.md#local-remote). 

* **Локальный** компьютер (например, локальный Настольный компьютер или портативный компьютер), как правило, если у вас небольшой набор данных, и все еще находится на этапе исследования. Пример для локальных вычислительных ресурсов см. в [этой записной книжке](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb). 
 
* **Удаленный** компьютер в облаке — [машинное обучение Azure управляемых вычислений](concept-compute-target.md#amlcompute) — это управляемая служба, которая позволяет обучать модели машинного обучения в кластерах виртуальных машин Azure. 

    Пример удаленных вычислений с использованием управляемых вычислительных ресурсов Машинного обучения Azure см. в [этой записной книжке](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). 

* **Кластер Azure Databricks** в подписке Azure. Дополнительные сведения см. в подокне [Настройка кластера Azure Databricks для автоматического создания машинного обучения](how-to-configure-databricks-automl-environment.md). На [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) доступны примеры записных книжек с Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Настройка параметров эксперимента

Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения. Эти параметры задаются путем создания экземпляра объекта `AutoMLConfig`. Полный список параметров см. в статье [AutoMLConfig class](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) (Класс AutoMLConfig).

Некоторые примеры:

1. Эксперимент классификации со значением AUC_weighted в качестве основной метрики со временем ожидания эксперимента в 30 минут и двумя свертками перекрестной проверки.

   ```python
       automl_classifier=AutoMLConfig(task='classification',
                                      primary_metric='AUC_weighted',
                                      experiment_timeout_minutes=30,
                                      blocked_models=['XGBoostClassifier'],
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=2)
   ```
1. В следующем примере для эксперимента регрессии задано значение End после 60 минут с пятью перекрестными свертываниями проверки.

   ```python
      automl_regressor = AutoMLConfig(task='regression',
                                      experiment_timeout_minutes=60,
                                      allowed_models=['KNN'],
                                      primary_metric='r2_score',
                                      training_data=train_data,
                                      label_column_name=label,
                                      n_cross_validations=5)
   ```


1. Задачи прогнозирования требуют дополнительной настройки. Дополнительные сведения см. в статье [автообучение модели прогнозирования временных рядов](how-to-auto-train-forecast.md) . 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>Поддерживаемые модели

Автоматизированное машинное обучение пытается выполнить различные модели и алгоритмы в процессе автоматизации и настройки. Пользователю не нужно указывать алгоритм. 

Три разных `task` значения параметров определяют список алгоритмов или моделей, которые необходимо применить. Используйте параметры `allowed_models` или `blocked_models` для дополнительного изменения итераций с помощью доступных моделей для включения или исключения. 

В следующей таблице перечислены поддерживаемые модели по типам задач. 

> [!NOTE]
> Если вы планируете экспортировать модели, созданные автоматизированным ML, в [модель ONNX](concept-onnx.md), преобразовать в формат ONNX можно только алгоритмы, обозначенные символом "*". Дополнительные сведения о [преобразовании моделей в ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Также обратите внимание, что в настоящее время ONNX поддерживает только задачи классификации и регрессии. 

Классификация | Регрессия | Прогнозирование временных рядов
|-- |-- |--
[Логистическая регрессия](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Эластичная сеть](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Эластичная сеть](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#regression)
[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Линейная классификация опорных векторов](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Лассо LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[Лассо LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Классификация опорных векторов (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Классификатор усредненного восприятия](/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)|[Регрессор вероятностного градиентного спуска](/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?preserve-view=true&view=nimbusml-py-latest) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Быстрый линейный регрессор](/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?preserve-view=true&view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Линейный классификатор SVM](/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?preserve-view=true&view=nimbusml-py-latest)*||

### <a name="primary-metric"></a>Основная метрика
`primary metric`Параметр определяет метрику, используемую во время обучения модели для оптимизации. Доступные для выбора метрики определяются выбранным типом задачи. В приведенной ниже таблице показаны допустимые основные метрики для каждого типа задачи.

Сведения об определениях этих метрик см. в статье [Общие сведения о результатах автоматизированного машинного обучения](how-to-understand-automated-ml.md).

|Классификация | Регрессия | Прогнозирование временных рядов
|--|--|--
|accuracy| spearman_correlation; | spearman_correlation;
|AUC_weighted | normalized_root_mean_squared_error; | normalized_root_mean_squared_error;
|average_precision_score_weighted | r2_score; | r2_score;
|norm_macro_recall | normalized_mean_absolute_error; | normalized_mean_absolute_error;
|precision_score_weighted |

### <a name="data-featurization"></a>Конструирование признаков

В каждом эксперименте по автоматизированному машинному обучению данные автоматически масштабируются или нормализуются, что способствует эффективному выполнению *некоторых* алгоритмов, чувствительных к разным масштабам признаков. Такое масштабирование и нормализация называются Добавление признаков. Дополнительные сведения и примеры кода см. [в разделе Добавление признаков в аутомл](how-to-configure-auto-features.md#) . 

При настройке экспериментов в `AutoMLConfig` объекте можно включить или отключить параметр `featurization` . В следующей таблице показаны допустимые параметры для Добавление признаков в [объекте аутомлконфиг](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Конфигурация конструирования признаков | Описание |
| ------------- | ------------- |
|`"featurization": 'auto'`| Указывает, что в рамках предварительной обработки [проверка данных и шаги конструирования признаков](how-to-configure-auto-features.md#featurization) выполняются автоматически. **Значение по умолчанию**.|
|`"featurization": 'off'`| Указывает, что шаг добавление признаков не должен выполняться автоматически.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Указывает, что следует использовать настраиваемый шаг конструирования признаков. [Узнайте, как настроить конструирование признаков](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Шаги конструирования признаков автоматизированного машинного обучения (нормализация признаков, обработка недостающих данных, преобразование текста в числовой формат и т. д.) становятся частью базовой модели. При использовании модели прогнозирования те же этапы конструирования признаков, которые выполнялись во время обучения, автоматически выполняются для входных данных.

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> Совокупная конфигурация

Модели ансамблей включены по умолчанию и отображаются как окончательные итерации выполнения в Аутомлном запуске. В настоящее время поддерживаются **вотинженсембле** и **стаккенсембле** . 

Голосование реализует мягкое голосование, в котором используются взвешенные средние значения. Реализация с накоплением использует две реализации слоев, где первый слой имеет те же модели, что и ансамблей голосования, а вторая модель слоев используется для поиска оптимального сочетания моделей из первого слоя. 

Если используются модели ONNX **или** включено объяснение модели, то стек отключен и используются только голосование.

Обучение ансамблей можно отключить с помощью `enable_voting_ensemble` `enable_stack_ensemble` параметров и логического типа.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

Чтобы изменить поведение ансамблей по умолчанию, существует несколько аргументов по умолчанию, которые могут быть предоставлены как `kwargs` в `AutoMLConfig` объекте.

> [!IMPORTANT]
>  Следующие параметры не являются явными параметрами класса Аутомлконфиг. 

* `ensemble_download_models_timeout_sec`: Во время создания моделей **VotingEnsemble** и **StackEnsemble** скачивается несколько подходящих моделей из предыдущих дочерних запусков. Если возникает ошибка `AutoMLEnsembleException: Could not find any models for running ensembling`, может потребоваться дополнительное время для скачивания моделей. Значение по умолчанию составляет 300 секунд для параллельного скачивания этих моделей. Время ожидания не ограничено. Если требуется больше времени, настройте для этого параметра значение более 300 секунд. 

  > [!NOTE]
  >  Если время ожидания истекло и имеются скачанные модели, то сборка продолжается с тем количеством моделей, которое было скачано. Не все модели должны быть обязательно скачаны до истечения времени ожидания.

Перечисленные ниже параметры применимы только к моделям **StackEnsemble**. 

* `stack_meta_learner_type`: мета-обучение — это модель, обученная на основе выходных данных отдельных разнородных моделей. Модели мета-обучения по умолчанию: `LogisticRegression` для задач классификации (или `LogisticRegressionCV`, если включена перекрестная проверка) и `ElasticNet` для задач регрессии и прогнозирования (или `ElasticNetCV`, если включена перекрестная проверка). Значением этого параметра может быть одна из следующих строк: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` или `LinearRegression`.

* `stack_meta_learner_train_percentage`: определяет долю обучающего набора (при выборе типа обучения "обучение и проверка"), зарезервированного для обучения модели мета-обучения. Значение по умолчанию: `0.2`. 

* `stack_meta_learner_kwargs`: необязательные параметры для передачи в инициализатор мета-обучения. Эти параметры и типы параметров отражают параметры и типы параметров из соответствующего конструктора модели и перенаправляются в конструктор модели.

В приведенном ниже коде показан пример настройки пользовательского поведения коллективных моделей в объекте `AutoMLConfig`.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

<a name="exit"></a> 

### <a name="exit-criteria"></a>Условия выхода

Существует несколько параметров, которые можно определить в Аутомлконфиг, чтобы завершить эксперимент.

|Критерии| description
|----|----
Без &nbsp; критериев | Если вы не определили никаких параметров выхода, эксперимент будет продолжен до тех пор, пока не будет выполнен дальнейший переход к основной метрике.
После &nbsp; &nbsp; длительного &nbsp; &nbsp; времени| Используйте `experiment_timeout_minutes` в параметрах, чтобы определить время, в течение которого ваш эксперимент должен продолжать выполняться. <br><br> Чтобы избежать сбоев при экспериментах, не менее 15 минут или 60 минут, если строка по размеру столбца превышает 10 000 000.
&nbsp; &nbsp; &nbsp; &nbsp; Достигнута Оценка| Использование `experiment_exit_score` завершает эксперимент после достижения указанного первичного показателя метрики.

## <a name="run-experiment"></a>Выполнение эксперимента

Для автоматизированного машинного обучения можно создать объект `Experiment`, который будет именованным объектом в `Workspace` для запуска экспериментов.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'Tutorial-automl'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Отправьте эксперимент для выполнения и создания модели. Передайте `AutoMLConfig` в метод `submit`, чтобы создать модель.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Сначала зависимости устанавливаются на новую виртуальную машину.  Это может занять до 10 минут, прежде чем отобразятся выходные данные.
>Если для параметра `show_output` задать значение `True`, выходные данные отобразятся в консоли.

### <a name="multiple-child-runs-on-clusters"></a>Несколько дочерних запусков в кластерах

В кластере, на котором уже выполняется другой эксперимент, можно выполнять дочерние запуски эксперимента ML. Однако время зависит от количества узлов в кластере, а также от того, доступны ли эти узлы для выполнения другого эксперимента.

Каждый узел в кластере выступает в качестве отдельной виртуальной машины (ВМ), которая может выполнить один обучающий запуск. для автоматического выполнения машинного обучения это означает дочерний запуск. Если все узлы заняты, новый эксперимент помещается в очередь. Но если имеются свободные узлы, новый эксперимент будет запускать автоматический дочерний элемент ML в параллельном режиме на доступных узлах и виртуальных машинах.

Для упрощения управления дочерними запусками и времени их выполнения рекомендуется создать выделенный кластер для каждого эксперимента и сопоставить число `max_concurrent_iterations` узлов в этом кластере с количеством ваших экспериментов. Таким образом, вы одновременно используете все узлы кластера с числом одновременно выполняемых дочерних запусков и итераций.

Настройте  `max_concurrent_iterations` в `AutoMLConfig` объекте. Если он не настроен, то по умолчанию для каждого эксперимента разрешен только один одновременный дочерний запуск или Итерация.  

## <a name="explore-models-and-metrics"></a>Изучение моделей и метрик

Просмотреть результаты обучения можно в мини-приложении или во встроенном окне при работе с записной книжкой. Ознакомьтесь с разделом [Просмотр сведений о выполнении](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs), чтобы получить дополнительные сведения.

Определения и примеры диаграмм производительности и метрик, предоставляемых для каждого запуска, см. в разделе [Оценка результатов автоматического эксперимента по машинному обучению](how-to-understand-automated-ml.md) . 

Чтобы получить сводку Добавление признаков и понять, какие функции были добавлены в определенную модель, см. раздел [прозрачность Добавление признаков](how-to-configure-auto-features.md#featurization-transparency). 

> [!NOTE]
> Алгоритмы, применяемые автоматизированным МАШИНным шифрованием, имеют случайное значение, что может привести к незначительной вариации в окончательной оценке метрик в рекомендуемой модели, такой как точность. При необходимости автоматизированное ML выполняет операции с данными, такими как разбиение по учению и проверке, разбиение на действия по обучению или перекрестная проверка. Поэтому, если вы запускаете эксперимент с теми же параметрами конфигурации и основной метрикой несколько раз, вы, вероятно, увидите вариант в последних метриках каждого эксперимента в соответствии с этими факторами. 

## <a name="register-and-deploy-models"></a>Регистрация и развертывание моделей

Дополнительные сведения о загрузке или регистрации модели для развертывания в веб-службе см. в статье [как и где развертывать модель](how-to-deploy-and-where.md).

<a name="explain"></a>

## <a name="model-interpretability"></a>Интерпретируемость модели

Интерпретируемость модели позволяет понять, почему модели дали тот или иной прогноз, а также важность базовых признаков. Пакет SDK содержит различные пакеты для включения функций интерпретации модели как во время обучения, так и во время вывода для локальных и развернутых моделей.

Примеры кода, демонстрирующие включение функций интерпретации, в частности в экспериментах автоматизированного машинного обучения, см. в [этом руководстве](how-to-machine-learning-interpretability-automl.md).

Общие сведения о том, как можно включить пояснения к моделям и важность признаков в других компонентах пакета SDK, не относящихся к автоматизированному машинному обучению, см. в [концептуальной статье об интерпретируемости](how-to-machine-learning-interpretability.md).

> [!NOTE]
> Модель Форекастткн в настоящее время не поддерживается клиентом пояснения. Эта модель не будет возвращать панель мониторинга с объяснением, если она возвращается в качестве лучшей модели и не поддерживает выполнение объяснения по требованию.

## <a name="troubleshooting"></a>Устранение неполадок

* **Последнее обновление `AutoML` зависимостей до более новых версий приведет к нарушению совместимости**: в версии 1.13.0 пакета SDK модели не будут загружаться в старые пакеты SDK из-за несовместимости старых версий, которые были закреплены в предыдущих пакетах, и более новых версий, которые мы заносимся в код. Вы увидите следующее сообщение об ошибке:
  * Модуль не найден: например `No module named 'sklearn.decomposition._truncated_svd` ,
  * Ошибки импорта: `ImportError: cannot import name 'RollingOriginValidator'` пример.
  * Ошибки атрибутов: пример. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Чтобы обойти эту ошибку, выполните один из следующих двух шагов в зависимости от вашей `AutoML` обучающей версии пакета SDK:
    * Если ваша `AutoML` учебная версия пакета SDK больше 1.13.0, вам потребуется `pandas == 0.25.1` и `sckit-learn==0.22.1` . Если обнаружено несовпадение версий, обновите scikit-учиться и/или Pandas, чтобы исправить версию, как показано ниже:
      
      ```bash
         pip install --upgrade pandas==0.25.1
         pip install --upgrade scikit-learn==0.22.1
      ```
      
    * Если ваша `AutoML` учебная версия пакета SDK меньше или равна 1.12.0, вам потребуется `pandas == 0.23.4` и `sckit-learn==0.20.3` . Если обнаружено несовпадение версий, можно перейти на более раннюю версию scikit и/или Pandas, как показано ниже:
  
      ```bash
        pip install --upgrade pandas==0.23.4
        pip install --upgrade scikit-learn==0.20.3
      ```

* **Сбой развертывания**: для версий <= 1.18.0 пакета SDK, базовый образ, созданный для развертывания, может завершиться со следующей ошибкой: "импортеррор: не удается импортировать имя `cached_property` из `werkzeug` ". 

  Для устранения этой проблемы можно выполнить следующие действия.
  1. Скачивание пакета модели
  2. Распакуйте пакет
  3. Развертывание с помощью распакованных ресурсов

* **Показатель "прогнозирование R2" всегда равен нулю**. Эта проблема возникает, если в предоставленных данных для обучения есть временные ряды, которые содержат одинаковые значения для последних `n_cv_splits`  +  `forecasting_horizon` точек данных. Если этот шаблон ожидается в ряде временных рядов, можно переключить основную метрику на нормализованное среднее значение ошибки в корне.
 
* **TensorFlow**: начиная с версии 1.5.0 пакета SDK, автоматизированное машинное обучение по умолчанию не устанавливает модели TensorFlow. Чтобы установить TensorFlow и использовать его с автоматизированными экспериментами ML, установите TensorFlow = = 1.12.0 через КондадепендеЦиес. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```

* **Диаграммы экспериментов**. диаграммы двоичной классификации (точность-отзыв, Roc, прибыль и т. д.), показанные в автоматических итерациях эксперимента ml, неправильно отображаются в пользовательском интерфейсе с 4/12. Графики диаграммы в настоящее время показывают обратные результаты, в которых более эффективные модели отображаются с более низкими результатами. Решение находится в процессе исследования.

* Для **отмены автоматического запуска машинного обучения отменяются**: при использовании средств автоматического машинного обучения в Azure Databricks для отмены запуска и запуска нового эксперимента перезапустите кластер Azure Databricks.

* **Модуль данных >10 итераций для автоматического машинного обучения**: в автоматических параметрах машинного обучения при наличии более 10 итераций задайте значение `show_output` `False` при отправке выполнения.

* **Мини-приложение "кирпичи" для машинное обучение Azure пакета SDK и автоматизированного машинного обучения**. в записной книжке для модуля "приложения" не поддерживается приложение машинное обучение Azure SDK, так как записные книжки не могут анализировать элементы HTML. Вы можете просмотреть мини-приложение на портале с помощью этого кода Python в Azure Databricks записной книжке:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **сбой automl_setup**: 
    * В Windows запустите automl_setup из командной строки Anaconda. Используйте эту ссылку для [установки Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * Убедитесь, что установлен conda 64-bit, а не 32-bit, выполнив `conda info` команду. `platform` `win-64` Для Windows или `osx-64` для Mac.
    * Убедитесь, что установлен conda 4.4.10 или более поздней версии. Версию можно проверить с помощью команды `conda -V` . Если установлена предыдущая версия, ее можно обновить с помощью команды: `conda update conda` .
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  Если возникает `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` ошибка, установите основные компоненты сборки с помощью команды `sudo apt-get install build-essential` .
      * Передайте новое имя в качестве первого параметра, чтобы automl_setup для создания новой среды conda. Просмотрите существующие среды conda `conda env list` , используя и удалите их с помощью `conda env remove -n <environmentname>` .
      
* **automl_setup_linux. sh завершается ошибкой**: Если automl_setup_linus. sh завершается сбоем в Ubuntu Linux с ошибкой: `unable to execute 'gcc': No such file or directory`-
  1. Убедитесь, что включены исходящие порты 53 и 80. На виртуальной машине Azure это можно сделать из портал Azure, выбрав виртуальную машину и щелкнув сеть.
  2. Выполните команду `sudo apt-get update`.
  3. Выполните команду `sudo apt-get install build-essential --fix-missing`.
  4. `automl_setup_linux.sh`Повторить запуск

* **сбой Configuration. ipynb**:
  * Для локальных conda сначала убедитесь, что automl_setup успешно запущен.
  * Убедитесь, что subscription_id правильно. Найдите subscription_id в портал Azure, выбрав все службы, а затем — подписки. Символы "<" и ">" не должны включаться в значение subscription_id. Например, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` имеет допустимый формат.
  * Убедитесь, что участник или владелец имеют доступ к подписке.
  * Убедитесь, что регион является одним из поддерживаемых регионов: `eastus2` , `eastus` ,,,, `westcentralus` `southeastasia` `westeurope` `australiaeast` , `westus2` , `southcentralus` .
  * Убедитесь, что доступ к региону осуществляется с помощью портал Azure.
  
* **`import AutoMLConfig` Ошибка**: в 1.0.76 версии автоматизированного машинного обучения были внесены изменения, которые потребовали удаления предыдущей версии перед обновлением до новой версии. Если `ImportError: cannot import name AutoMLConfig` обнаружено после обновления с версии пакета SDK до 1.0.76 v 1.0.76 или более поздней, устраните ошибку, выполнив `pip uninstall azureml-train automl` команду `pip install azureml-train-auotml` . Сценарий automl_setup. cmd делает это автоматически. 

* **сбой Workspace.from_config**: сбой вызова ws = Workspace.from_config ()
  1. Убедитесь, что Записная книжка Configuration. ipynb успешно запущена.
  2. Если Записная книжка запускается из папки, которая не находится в папке, в которой выполнялась эта папка `configuration.ipynb` , скопируйте папку aml_config и файл config.jsна том, который она содержит, в новую папку. Workspace.from_config считывает config.jsдля папки записной книжки или ее родительской папки.
  3. Если используется новая подписка, Группа ресурсов, Рабочая область или регион, убедитесь, что вы повторно запускаете `configuration.ipynb` записную книжку. Изменение config.jsнапрямую будет работать только в том случае, если Рабочая область уже существует в указанной группе ресурсов в указанной подписке.
  4. Если вы хотите изменить регион, измените рабочую область, группу ресурсов или подписку. `Workspace.create` не будет создавать или обновлять рабочую область, если она уже существует, даже если указана другая область.
  
* **Пример записной книжки завершается ошибкой**: Если пример записной книжки завершается ошибкой, что свойство, метод или библиотека не существуют:
  * Убедитесь, что в Jupyter Notebook выбран правильный kernel. Ядро отображается в правом верхнем углу страницы записной книжки. Значение по умолчанию — azure_automl. Ядро сохраняется как часть записной книжки. Поэтому при переключении на новую среду conda необходимо выбрать новый ядро в записной книжке.
      * Для записных книжек Azure это должен быть Python 3,6. 
      * Для локальных сред conda это должно быть имя среды conda, указанное в automl_setup.
  * Убедитесь, что Записная книжка предназначена для используемой версии пакета SDK. Версию пакета SDK можно проверить, выполнив `azureml.core.VERSION` в Jupyter Notebook ячейку. Вы можете скачать предыдущую версию образцов записных книжек из GitHub, нажав `Branch` кнопку, выбрав `Tags` вкладку и выбрав версию.

* **`import numpy` завершается ошибкой в Windows**: в некоторых средах Windows отображается ошибка при загрузке NumPy с последней версией Python 3.6.8. Если вы видите эту ошибку, попробуйте использовать Python версии 3.6.7.

* **`import numpy` сбой**: Проверьте версию TensorFlow в среде автоматизированного conda ml. Поддерживаемые версии: < 1,13. Удалите TensorFlow из среды, если версия >= 1,13. Вы можете проверить версию TensorFlow и удалить следующим образом:
  1. Запустите командную оболочку и активируйте среду conda, в которой установлены автоматические пакеты ml.
  2. Введите `pip freeze` и найдите `tensorflow` , если он найден, указанная версия должна быть < 1,13
  3. Если указанная версия не является поддерживаемой, `pip uninstall tensorflow` в командной оболочке введите y в поле Подтверждение.
  
 * **Сбой выполнения с `jwt.exceptions.DecodeError`**: точный текст сообщения об ошибке: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

    Для версий <= 1.17.0 пакета SDK, установка может привести к неподдерживаемой версии Пижвт. Проверьте версию Пижвт в среде автоматизированного conda ml. Поддерживаемые версии < 2.0.0. Вы можете проверить версию Пижвт следующим образом:
    1. Запустите командную оболочку и активируйте среду conda, в которой установлены автоматические пакеты ml.
    2. Введите `pip freeze` и найдите `PyJWT` , если он найден, указанная версия должна быть < 2.0.0

    Если указанная версия не является поддерживаемой версией, выполните следующие действия.
    1. Рассмотрите возможность обновления до последней версии пакета SDK для Аутомл: `pip install -U azureml-sdk[automl]` .
    2. Если это неприемлемо, удалите Пижвт из среды и установите правильную версию следующим образом:
        - `pip uninstall PyJWT` в командной оболочке и введите `y` для подтверждения.
        - Установите с помощью `pip install 'PyJWT<2.0.0'` .

## <a name="next-steps"></a>Дальнейшие действия

+ Узнайте больше о том, [как и где можно развернуть модель](how-to-deploy-and-where.md).

+ Узнайте больше о том, [как обучить модель регрессии с помощью автоматизированного машинного обучения](tutorial-auto-train-models.md) или [как обучить ее с использованием автоматизированного машинного обучения на удаленном ресурсе](how-to-auto-train-remote.md).

+ Узнайте, как обучить несколько моделей с помощью Аутомл в [акселераторе решений для многих моделей](https://aka.ms/many-models).
