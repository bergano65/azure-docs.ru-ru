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
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: seodec18
ms.openlocfilehash: 09f0e0f47ecd94c6db67b3973218cc1323bccde3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736166"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Настройка экспериментов автоматизированного машинного обучения на Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этом руководстве вы узнаете, как определить различные параметры конфигурации для экспериментов автоматизированного машинного обучения с помощью [пакета SDK для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Автоматическое машинное обучение выбирает алгоритм и гиперпараметры, а также создает модель, готовую для развертывания. Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения.

Примеры экспериментов автоматизированного машинного обучения см. в разделе [Руководство. Создание регрессионной модели с помощью автоматического машинного обучения](tutorial-auto-train-models.md) или [Обучение моделей с помощью автоматического машинного обучения в облаке](how-to-auto-train-remote.md).

Возможности настройки, доступные в автоматическом машинном обучении:

* выбор типа эксперимента: классификация, регрессия или прогнозирование временных рядов;
* источник данных, форматы и получение данных;
* выбор целевого объекта вычислений (локальный или удаленный);
* настройка параметров эксперимента автоматического машинного обучения;
* выполнение эксперимента автоматического машинного обучения;
* Изучение метрик модели
* регистрация и развертывание модели.

Если вы не хотите писать код, вы также можете [создавать эксперименты автоматизированного машинного обучения в Студии машинного обучения Azure](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Выбор типа эксперимента

Прежде чем начать эксперимент, следует определить тип задачи машинного обучения, которую необходимо решить. Автоматизированное машинное обучение поддерживает задачи классификации, регрессии и прогнозирования. Дополнительные сведения о [типах задач](how-to-define-task-type.md).

Эта служба также поддерживает приведенные ниже алгоритмы для автоматизации и настройки. Пользователю не нужно указывать алгоритм.

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
[Классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[Регрессор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Регрессор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Линейный классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Линейный регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Линейный регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Быстрый линейный регрессор](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Регрессор вероятностного градиентного спуска](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Классификатор усредненного восприятия](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Линейный классификатор SVM](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Чтобы указать тип эксперимента, используйте параметр `task` в конструкторе `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Источник данных и формат

Автоматическое машинное обучение поддерживает данные, находящиеся на локальном компьютере или в облаке в хранилище BLOB-объектов Azure. Данные можно считать в **кадр данных Pandas** или **табличный набор данных Машинного обучения Azure**.  [Дополнительные сведения о наборах данных](how-to-create-register-datasets.md).

Требования к обучающим данным:
- Данные должны иметь табличный формат.
- Прогнозируемое значение (целевой столбец) должно присутствовать в данных.

В приведенных ниже примерах кода показано, как можно хранить данные в этих форматах.

* Табличный набор данных

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

Для удаленных выполнений обучающие данные должны быть доступны из удаленной вычислительной среды. Класс [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) из пакета SDK предоставляет следующие функциональные возможности:

* простая передача данных из статических файлов или с URL-адресов в рабочую область;
* предоставление доступа к данным обучающим скриптам в облачной вычислительной среде.

Пример использования класса `Dataset` для подключения данных к целевому объекту вычислений см. в [этом руководстве](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets).

## <a name="train-and-validation-data"></a>Обучение и проверка данных

Указать обучающий и проверочный наборы данных можно непосредственно в конструкторе `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Перекрестная проверка по K-сверткам

Используйте параметр `n_cross_validations`, чтобы указать количество перекрестных проверок. Набор данных для обучения будет разделен случайным образом на свертки `n_cross_validations` одинакового размера. Во время каждого цикла перекрестной проверки один из свертков будет использоваться для проверки модели, обученной на оставшихся свертках. Этот процесс повторяется для циклов `n_cross_validations`, пока каждый сверток не будет использован один раз в качестве набора для проверки. Будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения.

Узнайте больше о том, как автоматизированное машинное обучение применяет перекрестную проверку, чтобы [предотвратить возникновение лжевзаимосвязи в моделях](concept-manage-ml-pitfalls.md#prevent-over-fitting).
### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Перекрестная проверка Монте-Карло (повторная случайная вложенная выборка)

С помощью `validation_size` укажите процент набора данных для обучения, который должен использоваться для проверки, а с помощью `n_cross_validations` укажите число перекрестных проверок. Во время каждого цикла перекрестной проверки подмножество размера `validation_size` будет выбрано случайным образом для проверки модели, обученной с использованием оставшихся данных. Наконец, будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения. Проверка Монте-Карло не поддерживается для прогнозирования временных рядов.

### <a name="custom-validation-dataset"></a>Настраиваемый проверочный набор данных

Используйте настраиваемый проверочный набор данных, если случайное разделение недопустимо (как правило, данные временных рядов или несбалансированные данные). Можно указать собственный проверочный набор данных. Модель будет анализироваться с использованием проверочного набора данных, указанного вместо случайного набора данных.

## <a name="compute-to-run-experiment"></a>Объект вычислений для выполнения эксперимента

Затем следует определить, где модель будет обучаться. Эксперимент автоматического машинного обучения можно запустить в следующих вычислительных средах.
* Локальный компьютер или ноутбук. Как правило, при наличии небольшого набора данных и если вы по-прежнему находитесь на этапе изучения.
* Удаленный компьютер в облаке. [Управляемая Вычислительная среда Машинного обучения Azure](concept-compute-target.md#amlcompute) — это управляемая служба, которая позволяет обучать модели машинного обучения в кластерах виртуальных машин Azure. 

  На [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) доступны примеры записных книжек с локальными и удаленными целевыми объектами вычислений.

* Кластер Azure Databricks в подписке Azure. Дополнительные сведения см. здесь: [Настройка кластера Azure Databricks для автоматизированного машинного обучения](how-to-configure-environment.md#azure-databricks).

  На [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) доступны примеры записных книжек с Azure Databricks.

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
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Ниже приведен пример эксперимента регрессии, который заканчивается через 60 минут и в течение которого выполняется пять сверток перекрестной проверки.

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

Три разных значения параметра `task` (третий тип задачи — `forecasting`; для него используется тот же пул алгоритмов, что и для задач `regression`), определяющие список применяемых моделей. Используйте параметры `whitelist` или `blacklist` для дополнительного изменения итераций с помощью доступных моделей для включения или исключения. Список поддерживаемых моделей можно найти в [описании класса SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) (для [классификации](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [прогнозирования](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting) и [регрессии](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

Чтобы избежать истечения времени ожидания при выполнении экспериментов, служба проверки автоматизированного машинного обучения требует задания для параметра `experiment_timeout_minutes` значения не менее 15 минут, или 60 минут, если количество строк, умноженное на количество столбцов, превышает 10 миллионов.

### <a name="primary-metric"></a>Основная метрика
Основная метрика — это метрика, которая должна использоваться для оптимизации во время обучения модели. Доступные для выбора метрики определяются выбранным типом задачи. В приведенной ниже таблице показаны допустимые основные метрики для каждого типа задачи.

|Классификация | Регрессия | Прогнозирование временных рядов
|-- |-- |--
|accuracy| spearman_correlation; | spearman_correlation;
|AUC_weighted | normalized_root_mean_squared_error; | normalized_root_mean_squared_error;
|average_precision_score_weighted | r2_score; | r2_score;
|norm_macro_recall | normalized_mean_absolute_error; | normalized_mean_absolute_error;
|precision_score_weighted |

Сведения об определениях этих метрик см. в статье [Общие сведения о результатах автоматизированного машинного обучения](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Конструирование признаков

В каждом эксперименте по автоматизированному машинному обучению данные [автоматически масштабируются или нормализуются](concept-automated-ml.md#preprocess), что способствует эффективному выполнению *некоторых* алгоритмов, чувствительных к разным масштабам признаков.  Однако можно также включить дополнительные методы конструирования признаков, например заполнение отсутствующих значений, кодирование и преобразования. [Узнайте подробнее о компонентах конструирования признаков](how-to-use-automated-ml-for-ml-models.md#featurization).

При настройке экспериментов можно включить дополнительный параметр `featurization`. В приведенной ниже таблице показаны допустимые параметры для конструирования признаков в [классе AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Конфигурация конструирования признаков | Описание |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Указывает, что следует использовать настраиваемый шаг конструирования признаков. [Узнайте, как настроить конструирование признаков](how-to-configure-auto-train.md#customize-feature-engineering).|
|`"featurization": 'off'`| Указывает, что настраиваемый шаг конструирования признаков не должен выполняться автоматически.|
|`"featurization": 'auto'`| Указывает, что в рамках предварительной обработки [проверка данных и шаги конструирования признаков](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) выполняются автоматически.|

> [!NOTE]
> Шаги конструирования признаков автоматизированного машинного обучения (нормализация признаков, обработка недостающих данных, преобразование текста в числовой формат и т. д.) становятся частью базовой модели. При использовании модели прогнозирования те же этапы конструирования признаков, которые выполнялись во время обучения, автоматически выполняются для входных данных.

### <a name="time-series-forecasting"></a>Прогнозирование временных рядов
Для задачи `forecasting` временных рядов требуются дополнительные параметры в объекте конфигурации:

1. `time_column_name`: Обязательный параметр, определяющий имя столбца в обучающих данных, который содержит допустимый временной ряд.
1. `max_horizon`: Определяет период времени, в течение которого необходимо прогнозировать данные, на основе периодичности обучающих данных. Например, если у вас есть обучающие данные с интервалами времени в один день, вы определяете, данные за сколько дней требуются для обучения модели.
1. `grain_column_names`: Определяет имена столбцов в обучающих данных, которые содержат отдельные временные ряды в обучающих данных. Например, при прогнозировании продаж определенной торговой марки по магазинам необходимо определить столбцы "Магазин" и "Торговая марка" в качестве столбцов детализации. Для каждого интервала и группирования будут созданы отдельные временные ряды и прогнозы. 

Примеры используемых ниже параметров см. в [примере записной книжки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a> Совокупная конфигурация

Коллективные модели включены по умолчанию и отображаются как окончательные итерации выполнения в запуске автоматизированного машинного обучения. В настоящее время поддерживаются следующие коллективные методы: голосование и наложение. Голосование реализуется как нестрогое голосование с взвешенными средними значениями, а реализация наложения является двухслойной: первый слой состоит из тех же моделей, что и голосование, а второй используется для поиска оптимального сочетания моделей из первого слоя. Если вы используете модели ONNX **или** включили объясняемость моделей, наложение будет отключено и использоваться будет только голосование.

Существует несколько аргументов по умолчанию, которые можно указать как `kwargs` в объекте `AutoMLConfig`, чтобы изменить поведение коллективной модели по умолчанию.

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

Коллективное обучение включено по умолчанию, но его можно отключить с помощью логических параметров `enable_voting_ensemble` и `enable_stack_ensemble`.

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

### <a name="exit-criteria"></a><a name="exit"></a> Условия выхода

Существует несколько параметров, которые можно определить для завершения эксперимента.
1. Нет условий. Если не определить параметры выхода, эксперимент будет продолжаться до тех пор, пока улучшение по основной метрике не прекратится.
1. Выход по истечении периода времени. С помощью параметра `experiment_timeout_minutes` можно определить время в минутах, в течение которого должен продолжаться эксперимент.
1. Выход после достижения оценки. При использовании `experiment_exit_score` эксперимент будет завершаться по достижении определенного значения основной метрики.

### <a name="explore-model-metrics"></a>Изучение метрик модели

Просмотреть результаты обучения можно в мини-приложении или во встроенном окне при работе с записной книжкой. Ознакомьтесь с разделом [Просмотр сведений о выполнении](how-to-track-experiments.md#view-run-details), чтобы получить дополнительные сведения.

## <a name="understand-automated-ml-models"></a>Анализ моделей автоматизированного машинного обучения

Любая модель, созданная с помощью автоматизированного машинного обучения, включает в себя следующие шаги:
+ автоматизированное конструирование признаков (если `"featurization": 'auto'`);
+ масштабирование, нормализация и применение алгоритма со значениями гиперпараметров.

Эти сведения можно легко получить из выходных данных fitted_model автоматизированного машинного обучения.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Автоматизированное конструирование признаков

См. список действий предварительной обработки и [автоматизированного конструирования признаков](concept-automated-ml.md#preprocess), выполняемых при `"featurization": 'auto'`.

Рассмотрим следующий пример.
+ Существует четыре входных признака: A (числовой), B (числовой), C (числовой), D (дата и время).
+ Числовой признак C удаляется, так как это столбец идентификаторов, все значения в котором уникальны.
+ Числовые признаки A и B имеют отсутствующие значения, которые заполняются средним значением.
+ Из признака D типа "дата и время" конструируется 11 различных признаков.

Используйте эти два интерфейса API на первом шаге подготовки модели, чтобы получить больше информации.  См. [пример записной книжки ](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` возвращает список имен сконструированных признаков.

  Использование:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Этот список включает в себя имена всех сконструированных признаков.

  >[!Note]
  >Используйте timeseriestransformer для задачи прогнозирования. Для задачи регрессии или классификации используйте datatransformer.

+ API 2: `get_featurization_summary()` возвращает сводку по конструированию для всех входных признаков.

  Использование:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Используйте timeseriestransformer для задачи прогнозирования. Для задачи регрессии или классификации используйте datatransformer.

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
   |RawFeatureName|Имя входного признака или столбца из предоставленного набора данных.|
   |TypeDetected|Распознанный тип данных входного признака.|
   |Dropped|Указывает, был ли входной признак удален или использован.|
   |EngineeringFeatureCount|Количество признаков, созданных с помощью преобразований в процессе автоматизированного конструирования признаков.|
   |Преобразования|Список преобразований, применяемых к входным признакам для создания сконструированных признаков.|
   
### <a name="customize-feature-engineering"></a>Настройка конструирования признаков
Чтобы настроить конструирование признаков, укажите  `"featurization": FeaturizationConfig`.

Поддерживаемые способы настройки:

|Настройка|Определение|
|--|--|
|Изменение назначения столбца|Переопределите тип признака для указанного столбца.|
|Изменение параметра преобразователя |Измените параметры для указанного преобразователя. В настоящее время поддерживаются Imputer (среднее, самое частое и медианное значения) и HashOneHotEncoder.|
|Удаление столбцов |Столбцы, которые следует исключить из конструирования признаков.|
|Блокирование преобразователей| Блокируйте преобразователи, используемые в процессе конструирования признаков.|

Создайте объект FeaturizationConfig с помощью вызовов API:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Масштабирование, нормализация и применение алгоритма со значениями гиперпараметров:

Чтобы получить представление о масштабировании, нормализации и значениях гиперпараметров алгоритма для конвейера, используйте fitted_model.steps. [Дополнительные сведения о масштабировании и нормализации](concept-automated-ml.md#preprocess). Пример выходных данных:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Чтобы получить дополнительные сведения, используйте эту вспомогательную функцию: 

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


print_model(model)
```

Ниже приведен пример выходных данных для конвейера, использующего определенный алгоритм (в данном случае LogisticRegression с RobustScalar).

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

### <a name="predict-class-probability"></a>Прогнозирование вероятности класса

Модели, созданные с помощью автоматизированного машинного обучения, имеют объекты-оболочки, которые отражают функциональные возможности исходного класса с открытым кодом. Большинство объектов-оболочек модели классификации, возвращаемых автоматизированным машинным обучением, реализуют функцию `predict_proba()`, которая принимает образец данных признаков (значения X) в виде массива или разреженной матрицы (значения X) и возвращает n-мерный массив, содержащий образец и соответствующую вероятность класса.

Если вы получили наиболее подходящую модель с помощью приведенных выше вызовов, вы можете вызвать `predict_proba()` непосредственно из подходящей модели, предоставив образец `X_test` в соответствующем формате в зависимости от типа модели.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Если базовая модель не поддерживает функцию `predict_proba()` или имеет неверный формат, будет вызвано исключение для класса модели. Примеры реализации этой функции для различных типов моделей см. в справочной документации по [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) и [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html).

<a name="explain"></a>

## <a name="model-interpretability"></a>Интерпретируемость модели

Интерпретируемость модели позволяет понять, почему модели дали тот или иной прогноз, а также важность базовых признаков. Пакет SDK содержит различные пакеты для включения функций интерпретации модели как во время обучения, так и во время вывода для локальных и развернутых моделей.

Примеры кода, демонстрирующие включение функций интерпретации, в частности в экспериментах автоматизированного машинного обучения, см. в [этом руководстве](how-to-machine-learning-interpretability-automl.md).

Общие сведения о том, как можно включить пояснения к моделям и важность признаков в других компонентах пакета SDK, не относящихся к автоматизированному машинному обучению, см. в [концептуальной статье об интерпретируемости](how-to-machine-learning-interpretability.md).

## <a name="next-steps"></a>Дальнейшие действия

+ Узнайте больше о том, [как и где можно развернуть модель](how-to-deploy-and-where.md).

+ Узнайте больше о том, [как обучить модель регрессии с помощью автоматизированного машинного обучения](tutorial-auto-train-models.md) или [как обучить ее с использованием автоматизированного машинного обучения на удаленном ресурсе](how-to-auto-train-remote.md).
+ Узнайте, как обучить несколько моделей с помощью автоматизированного машинного обучения, с помощью [акселератора решений для многих моделей](https://aka.ms/many-models).
