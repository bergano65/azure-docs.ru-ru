---
title: Создание эксперимента автоматического машинного обучения
titleSuffix: Azure Machine Learning
description: Автоматическое машинное обучение выбирает алгоритм и создает модель, готовую для развертывания. Узнайте о параметрах, с помощью которых можно настроить эксперименты автоматического машинного обучения.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 03e1d4aa74d2f71ab2f32ac55f4ad3d46f672f5c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618537"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Configure automated ML experiments in Python (Настройка экспериментов автоматизированного машинного обучения на Python)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этом руководстве узнайте, как определить различные настройки конфигурации ваших экспериментов по автоматическому машинному обучению с [помощью SDK Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Автоматическое машинное обучение выбирает алгоритм и гиперпараметры, а также создает модель, готовую для развертывания. Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения.

Для просмотра примеров экспериментов по автоматическому машинному обучению [см.](tutorial-auto-train-models.md) [Train models with automated machine learning in the cloud](how-to-auto-train-remote.md)

Возможности настройки, доступные в автоматическом машинном обучении:

* Выберите тип эксперимента: классификация, регрессия или прогнозирование временных рядов
* источник данных, форматы и получение данных;
* выбор целевого объекта вычислений (локальный или удаленный);
* настройка параметров эксперимента автоматического машинного обучения;
* выполнение эксперимента автоматического машинного обучения;
* Изучение метрик модели
* регистрация и развертывание модели.

Если вы предпочитаете отсутствие кода, вы также можете [создать свои автоматизированные эксперименты машинного обучения в студии машинного обучения Azure.](how-to-use-automated-ml-for-ml-models.md)

## <a name="select-your-experiment-type"></a>Выбор типа эксперимента

Прежде чем начать эксперимент, следует определить тип задачи машинного обучения, которую необходимо решить. Автоматизированное машинное обучение поддерживает типы задач классификации, регрессии и прогнозирования. Подробнее о [типах задач.](how-to-define-task-type.md)

Эта служба также поддерживает приведенные ниже алгоритмы для автоматизации и настройки. Пользователю не нужно указывать алгоритм.

Классификация | Регрессия | Прогнозирование временных рядов
|-- |-- |--
[Логистическая регрессия](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Эластичная сеть](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Эластичная сеть](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#regression)|[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#regression)
[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Линейная классификация опорных векторов](https://scikit-learn.org/stable/modules/svm.html#classification)|[Лассо LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Лассо LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Классификация векторов поддержки (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Линейный классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Линейный регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Линейный регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Быстрый линейный регрессор](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Авто-АРИМА](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Онлайн Градиентс спуск регрессор](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Пророк](https://facebook.github.io/prophet/docs/quick_start.html)
|[Средний классификатор перцептрона](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ПрогнозTCN
|[Линейный классификатор SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Используйте `task` параметр `AutoMLConfig` в конструкторе, чтобы указать тип эксперимента.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Источник данных и формат

Автоматическое машинное обучение поддерживает данные, находящиеся на локальном компьютере или в облаке в хранилище BLOB-объектов Azure. Данные можно прочитать в **Pandas DataFrame** или **ВАЗуре машинного обучения TabularDataset.**  [Подробнее о наборах данных.](how-to-create-register-datasets.md)

Требования к учебным данным:
- Данные должны быть в таблике.
- Значение для прогнозирования, целевой столбец, должно быть в данных.

Следующие примеры кода демонстрируют, как хранить данные в этих форматах.

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Кадр данных Pandas

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Получение данных для выполнения эксперимента на удаленных вычислительных ресурсах

Для удаленных выполнения обучающие данные должны быть доступны с помощью удаленных вычислений. Класс [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) в SDK предоставляет функциональные возможности:

* легко передавать данные из статических файлов или источников URL в рабочее пространство
* сделать ваши данные доступными для обучающие скрипты при запуске на ресурсах облачных вычислений

На примере использования `Dataset` класса для установки данных в вычислительную цель можно [ознакомиться.](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target)

## <a name="train-and-validation-data"></a>Обучение и проверка данных

Можно указать отдельные наборы поездов и валидаций непосредственно в конструкторе. `AutoMLConfig`

### <a name="k-folds-cross-validation"></a>Перекрестная проверка по K-сверткам

Используйте параметр `n_cross_validations`, чтобы указать количество перекрестных проверок. Набор данных для обучения будет разделен случайным образом на свертки `n_cross_validations` одинакового размера. Во время каждого цикла перекрестной проверки один из свертков будет использоваться для проверки модели, обученной на оставшихся свертках. Этот процесс повторяется для циклов `n_cross_validations`, пока каждый сверток не будет использован один раз в качестве набора для проверки. Будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Монте-Карло Крест аттестации (Неоднократное случайное подвыборки)

С помощью `validation_size` укажите процент набора данных для обучения, который должен использоваться для проверки, а с помощью `n_cross_validations` укажите число перекрестных проверок. Во время каждого цикла перекрестной проверки подмножество размера `validation_size` будет выбрано случайным образом для проверки модели, обученной с использованием оставшихся данных. Наконец, будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения. Монте-Карло не поддерживается для прогнозирования временных рядов.

### <a name="custom-validation-dataset"></a>Настраиваемый проверочный набор данных

Используйте пользовательский набор данных проверки, если случайный раскол не является приемлемым, как правило, данные временных рядов или несбалансированные данные. Можно указать собственный проверочный набор данных. Модель будет анализироваться с использованием проверочного набора данных, указанного вместо случайного набора данных.

## <a name="compute-to-run-experiment"></a>Объект вычислений для выполнения эксперимента

Затем следует определить, где модель будет обучаться. Эксперимент автоматического машинного обучения можно запустить в следующих вычислительных средах.
* Локальный компьютер или ноутбук. Как правило, при наличии небольшого набора данных и если вы по-прежнему находитесь на этапе изучения.
* Удаленный компьютер в облаке. [Управляемая Вычислительная среда Машинного обучения Azure](concept-compute-target.md#amlcompute) — это управляемая служба, которая позволяет обучать модели машинного обучения в кластерах виртуальных машин Azure.

  На этом [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) приведены примеры ноутбуков с локальными и дистанционными вычислениями.

* Кластер Данных Azure в подписке Azure. Вы можете найти более подробную информацию здесь - [Настройка Azure Databricks кластера для автоматизированных ML](how-to-configure-environment.md#azure-databricks)

  На этом [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) приведены примеры блокнотов с Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Настройка параметров эксперимента

Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения. Эти параметры задаются путем создания экземпляра объекта `AutoMLConfig`. Полный список параметров см. в статье [AutoMLConfig class](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) (Класс AutoMLConfig).

Некоторые примеры:

1. Классификационный эксперимент с использованием AUC взвешенных в качестве основной метрики с экспериментом тайм-аут минут, установленных на 30 минут и 2 перекрестной проверки складок.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Ниже приведен пример регрессии эксперимент, который должен закончиться через 60 минут с пятью поперечными складками проверки.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

Три различных `task` значения параметров (третий `forecasting`тип задачи и использует `regression` аналогичный пул алгоритмов в качестве задач) определяют список моделей для применения. Используйте `whitelist` `blacklist` или параметры для дальнейшего изменения итераций с имеющимися моделями, чтобы включить или исключить. Список поддерживаемых моделей можно найти на [классе SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) для[(классификация,](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification) [прогнозирование](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)и [регрессия).](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)

Чтобы избежать сбоев тайм-аута эксперимента, служба проверки Automated ML потребует, чтобы она `experiment_timeout_minutes` была установлена минимум на 15 минут, или 60 минут, если размер столбца превышает 10 миллионов.

### <a name="primary-metric"></a>Основная метрика
Первичная метрика определяет метрику, которая будет использоваться во время обучения модели для оптимизации. Доступные метрики, которые вы можете выбрать, определяются выбранным типом задачи, а в следующей таблице показаны действительные основные метрики для каждого типа задачи.

|Классификация | Регрессия | Прогнозирование временных рядов
|-- |-- |--
|accuracy| spearman_correlation; | spearman_correlation;
|AUC_weighted | normalized_root_mean_squared_error; | normalized_root_mean_squared_error;
|average_precision_score_weighted | r2_score; | r2_score;
|norm_macro_recall | normalized_mean_absolute_error; | normalized_mean_absolute_error;
|precision_score_weighted |

Узнайте о конкретных определениях этих показателей в [разделе Понимание результатов автоматизированного машинного обучения.](how-to-understand-automated-ml.md)

### <a name="data-featurization"></a>Феатуризация данных

В каждом автоматизированном эксперименте машинного обучения ваши данные [автоматически масштабируются и нормализуются,](concept-automated-ml.md#preprocess) чтобы помочь *определенным* алгоритмам, чувствительным к функциям, которые находятся в разных масштабах.  Тем не менее, можно также включить дополнительную фетурацию, такую как вычисление недостающих значений, кодирование и преобразование. [Узнайте больше о том, что феатуризация включена](how-to-use-automated-ml-for-ml-models.md#featurization).

При настройке экспериментов можно включить расширенную настройку. `featurization` В следующей таблице показаны принятые настройки для феатуризации в [ `AutoMLConfig` классе.](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)

|Конфигурация феатуризации | Описание |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Указывает настраиваемый шаг фетуризации должны быть использованы. [Узнайте, как настроить феатурацию.](how-to-configure-auto-train.md#customize-feature-engineering)|
|`"featurization": 'off'`| Указывает на этап феатуризации не следует делать автоматически.|
|`"featurization": 'auto'`| Указывает, что в рамках предварительной [обработки, ограждения данных и шаги феатуризации](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) выполняются автоматически.|

> [!NOTE]
> Автоматизированные шаги по фетуризации машинного обучения (нормализация функций, обработка недостающих данных, преобразование текста в числовую часть модели. При использовании модели для прогнозирования те же шаги фетуризации, применяемые во время обучения, автоматически применяются к входным данным.

### <a name="time-series-forecasting"></a>Прогнозирование временных рядов
Задача временных рядов `forecasting` требует дополнительных параметров в объекте конфигурации:

1. `time_column_name`: Необходимый параметр, определяющий название столбца в обучаемых данных, содержащих допустимые временные ряды.
1. `max_horizon`: Определяет время, время, что вы хотите предсказать, на основе периодичности обучаемых данных. Например, если у вас есть обучающие данные с ежедневными зернами времени, вы определяете, как далеко в дни вы хотите, чтобы модель тренировалась.
1. `grain_column_names`: Определяет название столбцов, содержащих отдельные данные временных рядов в учебных данных. Например, если вы прогнозируете продажи определенного бренда по магазину, вы определяете столбцы магазинов и брендов как столбцы зерна как столбцы зерна. Для каждого зерна/группирования будут созданы отдельные временные ряды и прогнозы. 

Для примеров параметров, используемых [sample notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)ниже, см.

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Конфигурация ансамбля

Модели ансамбля включены по умолчанию и отображаются в качестве окончательных итераций в автоматическом запуске машинного обучения. В настоящее время поддерживаемые методы ансамбля голосования и укладки. Голосование реализуется как мягкое голосование с использованием взвешенных средних, а реализация укладки использует двухслойную реализацию, где первый слой имеет те же модели, что и ансамбль для голосования, а модель второго слоя используется для поиска оптимального сочетания моделей с первого слоя. Если вы используете модели ONNX **или** включили моделирование-объяснимость, укладка будет отключена, и будет использовано только голосование.

Есть несколько аргументов по умолчанию, которые могут быть предоставлены как `kwargs` в объекте, `AutoMLConfig` чтобы изменить поведение ансамбля стека по умолчанию.

* `stack_meta_learner_type`: мета-учащийся является моделью, обучаемым на выходе отдельных неоднородных моделей. Мета-учащиеся по `LogisticRegression` умолчанию предназначены `LogisticRegressionCV` для задач классификации (или если включена перекрестная проверка) и `ElasticNet` для задач регрессии/прогнозирования (или `ElasticNetCV` если включена перекрестная проверка). Этот параметр может быть одним `LogisticRegression`из `LogisticRegressionCV` `LightGBMClassifier`следующих строк: , , `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`или `LinearRegression`.
* `stack_meta_learner_train_percentage`: определяет долю учебного набора (при выборе поезда и типа проверки), который должен быть зарезервирован для обучения мета-учащегося. Значение по умолчанию: `0.2`.
* `stack_meta_learner_kwargs`: дополнительные параметры для передачи инициализатору мета-учащегося. Эти параметры и типы параметров отражают параметры и типы параметров от соответствующего конструктора модели и направляются на конструктор модели.

В следующем коде показан пример указания пользовательского поведения ансамбля в объекте. `AutoMLConfig`

```python
ensemble_settings = {
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

Обучение ансамбля включено по умолчанию, но `enable_voting_ensemble` `enable_stack_ensemble` оно может быть отключено с помощью параметров boolean.

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

## <a name="run-experiment"></a>Выполнение эксперимента

Для автоматизированного ML `Experiment` вы создаете объект, который `Workspace` является названным объектом в использованном для запуска экспериментах.

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

### <a name="exit-criteria"></a>Критерии выхода
Есть несколько вариантов, которые вы можете определить, чтобы закончить эксперимент.
1. Нет критериев: Если вы не определяете параметры выхода, эксперимент будет продолжаться до тех пор, пока не будет достигнут дальнейший прогресс по вашей основной метрике.
1. Выход через некоторое время: Использование `experiment_timeout_minutes` в настройках позволяет определить, как долго в минутах, если эксперимент будет продолжаться в перспективе.
1. Выход после того, как `experiment_exit_score` оценка была достигнута: Использование завершит эксперимент после достижения первичной метрической оценки.

### <a name="explore-model-metrics"></a>Изучение метрик модели

Вы можете просматривать результаты обучения в виджете или в строке, если вы находитесь в блокноте. Ознакомьтесь с разделом [Просмотр сведений о выполнении](how-to-track-experiments.md#view-run-details), чтобы получить дополнительные сведения.

## <a name="understand-automated-ml-models"></a>Понимание автоматизированных моделей ML

Любая модель, производимая с использованием автоматизированного ML, включает в себя следующие шаги:
+ Автоматизированная инженерия функций (если) `"featurization": 'auto'`
+ Масштабирование/нормализация и алгоритм с значениями гиперпарамов

Мы делаем его прозрачным, чтобы получить эту информацию из fitted_model выход из автоматизированного ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Автоматизированная инженерия функций

Ознакомьтесь со списком предварительной `"featurization": 'auto'`обработки и [автоматизированной инженерии функций,](concept-automated-ml.md#preprocess) которая происходит при.

Рассмотрим следующий пример.
+ Есть четыре входиные функции: A (Число), B (Число), C (Число), D (DateTime)
+ Числовая функция C отбрасывается, потому что это идентификатор со всеми уникальными значениями
+ Числовые объекты A и B имеют недостающие значения и, следовательно, вменлены средним
+ Функция DateTime D разделена на 11 различных инженерных функций

Используйте эти 2 AI на первом этапе установленной модели, чтобы понять больше.  Смотрите [этот образец ноутбука](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` возвращает список сконструированных имен функций.

  Использование:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Этот список включает в себя все инженерные имена функций.

  >[!Note]
  >Используйте 'timeseriestransformer' для прогнозирования задач, иначе используйте 'Datatransformer' для задачи «регрессии» или «классификации».

+ API 2: `get_featurization_summary()` возвращает резюме феатуризации для всех функций ввода.

  Использование:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Используйте 'timeseriestransformer' для прогнозирования задач, иначе используйте 'Datatransformer' для задачи «регрессии» или «классификации».

  Выходные данные:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Где:

   |Выходные данные|Определение|
   |----|--------|
   |RawFeatureName|Имя функции ввода/колонки из предоставленного набора данных.|
   |TypeDetected|Обнаруженный тип данных функции ввода.|
   |Выполнен сброс|Указывает, была ли удалена или использована функция ввода.|
   |EngineeringFeatureCount|Количество функций, генерируемых с помощью автоматизированных преобразований инженерного функций.|
   |Преобразования|Список преобразований, применяемых к функциям ввода для генерации инженерных функций.|
   
### <a name="customize-feature-engineering"></a>Настройка инженерных функций
Чтобы настроить проектирование `"featurization": FeaturizationConfig`объектов, укажите.

Поддерживаемая настройка включает в себя:

|Настройка|Определение|
|--|--|
|Обновление цели столбца|Переопределение типа функции для указанного столбца.|
|Обновление параметра трансформатора |Параметры обновления указанного трансформатора. В настоящее время поддерживает Imputer (средний, наиболее часто & медианы) и HashOneHotEncoder.|
|Выпадение столбцов |Столбцы, чтобы отпасть от featurized.|
|Блок трансформаторы| Блок трансформаторы, которые будут использоваться в процессе феатуризации.|

Создание объекта FeaturizationConfig с помощью aPI-звонков:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Масштабирование/нормализация и алгоритм со значениями гиперпарамов:

Чтобы понять значения масштабирования/нормализации и алгоритма/гиперпарамета для конвейера, используйте fitted_model.steps. [Подробнее о масштабировании/нормализации.](concept-automated-ml.md#preprocess) Пример выходных данных:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Чтобы получить более подробную информацию, используйте эту функцию помощника, показанную в [этом образце ноутбука.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

Следующий выход образца предназначен для конвейера с использованием определенного алгоритма (LogisticRegression with RobustScalar, в данном случае).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Прогноз вероятности класса

Модели, производимые с использованием автоматизированного ML все имеют обертку объектов, которые отражают функциональность от их открытого исходного кода класса. Большинство объектов классификационной модели обертки, возвращенные автоматизированным ML, реализуют `predict_proba()` функцию, которая принимает массивоподобную или разреженую матричную выборку данных ваших объектов (x-значения) и возвращает n-мерный массив каждого образца и соответствующей вероятности класса.

Предполагая, что вы извлекли лучшую модель, используя те `predict_proba()` же вызовы сверху, вы `X_test` можете звонить непосредственно из установленной модели, поставляя образец в соответствующем формате в зависимости от типа модели.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Если базовая модель не `predict_proba()` поддерживает функцию или формат неправильный, будет брошено исключение для конкретного класса модели. Примеры того, как эта функция реализована для различных типов моделей, можно найти справочные документы [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) и [XGBoost.](https://xgboost.readthedocs.io/en/latest/python/python_api.html)

<a name="explain"></a>

## <a name="model-interpretability"></a>Интерпретируемость модели

Интерпретация модели позволяет понять, почему ваши модели делали прогнозы, а также значения значения значения значения, лежащего в основе объекта. SDK включает в себя различные пакеты для включения функций интерпретации моделей, как в учебное время, так и во время выводов, для локальных и развернутых моделей.

[Ознакомьтесь](how-to-machine-learning-interpretability-automl.md) с примерами кода о том, как включить функции интерпретации, специально в рамках автоматизированных экспериментов машинного обучения.

Для получения общей информации о том, как объяснения модели и важность функций могут быть [concept](how-to-machine-learning-interpretability.md) включены в других областях SDK за пределами автоматизированного машинного обучения, см.

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о том, [как и где можно развернуть модель](how-to-deploy-and-where.md).

Узнайте больше о [том, как обучить модель регрессии с помощью автоматизированного машинного обучения](tutorial-auto-train-models.md) или [как тренироваться с помощью Автоматизированного машинного обучения на удаленном ресурсе.](how-to-auto-train-remote.md)
