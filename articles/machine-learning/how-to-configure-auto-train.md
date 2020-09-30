---
title: Создание эксперимента автоматического машинного обучения
titleSuffix: Azure Machine Learning
description: Автоматическое машинное обучение выбирает алгоритм и создает модель, готовую для развертывания. Узнайте о параметрах, с помощью которых можно настроить эксперименты автоматического машинного обучения.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 09/29/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python,contperfq1
ms.openlocfilehash: 895bdfde80dfe10bef4b6aad12236db60e2c427b
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533151"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Настройка экспериментов автоматизированного машинного обучения на Python


В этом руководстве вы узнаете, как определить различные параметры конфигурации для экспериментов автоматизированного машинного обучения с помощью [пакета SDK для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true). Автоматическое машинное обучение выбирает алгоритм и гиперпараметры, а также создает модель, готовую для развертывания. Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения.

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

## <a name="prerequisites"></a>Предварительные условия

Для этой статьи требуется: 
* Рабочая область машинного обучения Azure. Дополнительные сведения см. в [инструкциях по созданию рабочей области Машинного обучения Azure](how-to-manage-workspace.md).

* Установленный пакет SDK для Машинное обучение Azure Python.
    Для установки пакета SDK можно либо 
    * Создайте вычислительный экземпляр, который автоматически устанавливает пакет SDK и предварительно настроен для рабочих процессов машинного обучения. Дополнительные сведения см. [в разделе что такое машинное обучение Azure вычислительного экземпляра?](concept-compute-instance.md#managing-a-compute-instance) . 

    * [Установите пакет SDK самостоятельно](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). Просто обязательно включите `automl` дополнительный объект. 

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

**Для удаленных экспериментов**обучающие данные должны быть доступны из удаленного вычислений. Аутомл принимает только [машинное обучение Azure табулардатасетс](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) при работе с удаленным вычислением. 

Машинное обучение Azure наборы данных предоставляют следующие функциональные возможности:

* Легко переносите данные из статических файлов или URL-источников в рабочую область.
* Сделайте свои данные доступными для обучения сценариям при выполнении в облачных ресурсах. Пример [How to train with datasets](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) использования `Dataset` класса для подключения данных к удаленному целевому объекту вычислений см. в разделе обучение с помощью наборов данных.

Следующий код создает Табулардатасет на основе URL-адреса. См. статью [Создание табулардатасетс](how-to-create-register-datasets.md#create-a-tabulardataset) для примеров кода, посвященных созданию наборов данных из других источников, таких как локальные файлы и хранилища данных.

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**Для локальных вычислительных экспериментов**рекомендуется Pandas кадры данных для ускорения обработки.

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

Затем следует определить, где модель будет обучаться. Обучающий эксперимент для автоматического машинного обучения можно запустить в следующих параметрах вычислений. Узнайте о [преимуществах и недостатках локальных и удаленных вычислений](concept-automated-ml.md#local-remote) . 

* **Локальный** компьютер (например, локальный Настольный компьютер или портативный компьютер), как правило, если у вас небольшой набор данных, и все еще находится на этапе исследования. Пример локального вычислений см. в [этой записной книжке](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb) . 
 
* **Удаленный** компьютер в облаке — [машинное обучение Azure управляемых вычислений](concept-compute-target.md#amlcompute) — это управляемая служба, которая позволяет обучать модели машинного обучения в кластерах виртуальных машин Azure. 

    Просмотрите [эту записную книжку](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) для удаленного примера с помощью машинное обучение Azure управляемых вычислений. 

* **Кластер Azure Databricks** в подписке Azure. Дополнительные сведения см. здесь: [установка Azure Databricks кластера для автоматического ML](how-to-configure-environment.md#aml-databricks). На [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) доступны примеры записных книжек с Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Настройка параметров эксперимента

Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения. Эти параметры задаются путем создания экземпляра объекта `AutoMLConfig`. Полный список параметров см. в статье [AutoMLConfig class](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) (Класс AutoMLConfig).

Некоторые примеры:

1. Эксперимент классификации со значением AUC_weighted в качестве основной метрики со временем ожидания эксперимента в 30 минут и двумя свертками перекрестной проверки.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. В следующем примере для эксперимента регрессии задано значение End после 60 минут с пятью перекрестными свертываниями проверки.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. Для выполнения задач прогнозирования требуется дополнительная настройка. Дополнительные сведения см. в статье [Автоматическое обучение модели прогнозирования временных рядов](how-to-auto-train-forecast.md) . 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
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
[Классификатор усредненного восприятия](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest&preserve-view=true)|[Регрессор вероятностного градиентного спуска](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest&preserve-view=true) |[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Быстрый линейный регрессор](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest&preserve-view=true)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[Линейный классификатор SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest&preserve-view=true)*||

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

## <a name="run-experiment"></a>Выполнение эксперимента

Для автоматизированного машинного обучения можно создать объект `Experiment`, который будет именованным объектом в `Workspace` для запуска экспериментов.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
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

 <a name="exit"></a> 

### <a name="exit-criteria"></a>Условия выхода

Существует несколько параметров, которые можно определить для завершения эксперимента.

|Критерии| description
|----|----
Без &nbsp; критериев | Если вы не определили никаких параметров выхода, эксперимент будет продолжен до тех пор, пока не будет выполнен дальнейший переход к основной метрике.
После &nbsp; &nbsp; длительного &nbsp; &nbsp; времени| Используйте `experiment_timeout_minutes` в параметрах, чтобы определить время, в течение которого ваш эксперимент должен продолжать выполняться. <br><br> Чтобы избежать сбоев при экспериментах, не менее 15 минут или 60 минут, если строка по размеру столбца превышает 10 000 000.
&nbsp; &nbsp; &nbsp; &nbsp; Достигнута Оценка| Использование `experiment_exit_score` завершает эксперимент после достижения указанного первичного показателя метрики.

## <a name="explore-models-and-metrics"></a>Изучение моделей и метрик

Просмотреть результаты обучения можно в мини-приложении или во встроенном окне при работе с записной книжкой. Ознакомьтесь с разделом [Просмотр сведений о выполнении](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs), чтобы получить дополнительные сведения.

Определения и примеры диаграмм производительности и метрик, предоставляемых для каждого запуска, см. в разделе Общие сведения об [автоматизированных результатах машинного обучения](how-to-understand-automated-ml.md) . 

Чтобы получить сводку Добавление признаков и понять, какие функции были добавлены в определенную модель, см. раздел [прозрачность Добавление признаков](how-to-configure-auto-features.md#featurization-transparency). 

## <a name="register-and-deploy-models"></a>Регистрация и развертывание моделей

Дополнительные сведения о загрузке или регистрации модели для развертывания в веб-службе см. в статье [как и где развертывать модель](how-to-deploy-and-where.md).

<a name="explain"></a>

## <a name="model-interpretability"></a>Интерпретируемость модели

Интерпретируемость модели позволяет понять, почему модели дали тот или иной прогноз, а также важность базовых признаков. Пакет SDK содержит различные пакеты для включения функций интерпретации модели как во время обучения, так и во время вывода для локальных и развернутых моделей.

Примеры кода, демонстрирующие включение функций интерпретации, в частности в экспериментах автоматизированного машинного обучения, см. в [этом руководстве](how-to-machine-learning-interpretability-automl.md).

Общие сведения о том, как можно включить пояснения к моделям и важность признаков в других компонентах пакета SDK, не относящихся к автоматизированному машинному обучению, см. в [концептуальной статье об интерпретируемости](how-to-machine-learning-interpretability.md).

> [!NOTE]
> Модель Форекастткн в настоящее время не поддерживается клиентом пояснения. Эта модель не будет возвращать панель мониторинга с объяснением, если она возвращается в качестве лучшей модели и не поддерживает выполнение объяснения по требованию.

## <a name="next-steps"></a>Дальнейшие действия

+ Узнайте больше о том, [как и где можно развернуть модель](how-to-deploy-and-where.md).

+ Узнайте больше о том, [как обучить модель регрессии с помощью автоматизированного машинного обучения](tutorial-auto-train-models.md) или [как обучить ее с использованием автоматизированного машинного обучения на удаленном ресурсе](how-to-auto-train-remote.md).

+ Узнайте, как обучить несколько моделей с помощью Аутомл в [акселераторе решений для многих моделей](https://aka.ms/many-models).
