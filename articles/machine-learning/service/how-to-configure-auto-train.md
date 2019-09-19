---
title: Создание эксперимента автоматического машинного обучения
titleSuffix: Azure Machine Learning
description: Автоматическое машинное обучение выбирает алгоритм и создает модель, готовую для развертывания. Узнайте о параметрах, с помощью которых можно настроить эксперименты автоматического машинного обучения.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2ef501af63628b47bc52d416930c90057569b5de
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035030"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Настройка автоматизированных экспериментов машинного обучения в Python

В этом руководство вы узнаете, как определить различные параметры конфигурации для автоматических экспериментов машинного обучения с помощью [пакета SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Автоматическое машинное обучение выбирает алгоритм и гиперпараметры, а также создает модель, готовую для развертывания. Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения.

Примеры экспериментов автоматического машинного обучения см. в разделе [Руководство. Создание регрессионной модели с помощью автоматического машинного обучения](tutorial-auto-train-models.md) или [Обучение моделей с помощью автоматического машинного обучения в облаке](how-to-auto-train-remote.md).

Возможности настройки, доступные в автоматическом машинном обучении:

* выбор типа эксперимента: Классификация, регрессия или прогнозирование временных рядов
* источник данных, форматы и получение данных;
* выбор целевого объекта вычислений (локальный или удаленный);
* настройка параметров эксперимента автоматического машинного обучения;
* выполнение эксперимента автоматического машинного обучения;
* Изучение метрик модели
* регистрация и развертывание модели.

Если вы предпочитаете не использовать код, можно также [создать автоматические эксперименты машинного обучения в портал Azure](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Выбор типа эксперимента

Прежде чем начать эксперимент, следует определить тип задачи машинного обучения, которую необходимо решить. Автоматическое машинное обучение поддерживает задачи классификации, регрессии и прогнозирования.

Эта служба также поддерживает приведенные ниже алгоритмы для автоматизации и настройки. Пользователю не нужно указывать алгоритм.

Классификация | Регрессия | Прогнозирование временных рядов
|-- |-- |--
[Логистическая регрессия](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Эластичная сеть](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Эластичная сеть](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#regression)|[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#regression)
[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Линейная классификация опорных векторов](https://scikit-learn.org/stable/modules/svm.html#classification)|[Лассо LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Лассо LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Классификация опорных C-векторов (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNNная регрессия](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNNная регрессия](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNNй Линейный классификатор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Линейная регрессия](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Линейная регрессия](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

`task` Используйте параметр `AutoMLConfig` в конструкторе, чтобы указать тип эксперимента.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Источник данных и формат
Автоматическое машинное обучение поддерживает данные, находящиеся на локальном компьютере или в облаке в хранилище BLOB-объектов Azure. Данные можно считать в поддерживаемых форматах данных scikit-learn. Можно считывать данные в:
* массивы Numpy X (признаки) и Y (целевая переменная или метка);
* Кадр данных Pandas

>[!Important]
> Требования к данным для обучения:
>* Данные должны быть в табличной форме.
>* Значение, которое необходимо спрогнозировать (целевой столбец), должно присутствовать в данных.

Примеры:

*   Массивы Numpy

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Кадр данных Pandas

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Получение данных для выполнения эксперимента на удаленных вычислительных ресурсах

Для удаленных выполнений необходимо сделать данные доступными для удаленного вычислений. Это можно сделать, загружая данные в хранилище данных.

Ниже приведен пример использования `datastore`:

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Определение ссылок dprep

Определите X и y как ссылку на dprep, которая будет передана в автоматизированный `AutoMLConfig` объект машинного обучения, как показано ниже:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Обучение и проверка данных

Отдельный набор обучения и проверки можно указать непосредственно в `AutoMLConfig` методе.

### <a name="k-folds-cross-validation"></a>Перекрестная проверка по K-сверткам

Используйте параметр `n_cross_validations`, чтобы указать количество перекрестных проверок. Набор данных для обучения будет разделен случайным образом на свертки `n_cross_validations` одинакового размера. Во время каждого цикла перекрестной проверки один из свертков будет использоваться для проверки модели, обученной на оставшихся свертках. Этот процесс повторяется для циклов `n_cross_validations`, пока каждый сверток не будет использован один раз в качестве набора для проверки. Будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Монте-Карло перекрестная проверка (повторная произвольная подвыборка)

С помощью `validation_size` укажите процент набора данных для обучения, который должен использоваться для проверки, а с помощью `n_cross_validations` укажите число перекрестных проверок. Во время каждого цикла перекрестной проверки подмножество размера `validation_size` будет выбрано случайным образом для проверки модели, обученной с использованием оставшихся данных. Наконец, будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения. Монте-Карло не поддерживается для прогнозирования временных рядов.

### <a name="custom-validation-dataset"></a>Настраиваемый проверочный набор данных

Используйте пользовательский набор данных проверки, если случайное разбиение неприемлемо, обычно данные временных рядов или несбалансированные данные. Можно указать собственный проверочный набор данных. Модель будет анализироваться с использованием проверочного набора данных, указанного вместо случайного набора данных.

## <a name="compute-to-run-experiment"></a>Объект вычислений для выполнения эксперимента

Затем следует определить, где модель будет обучаться. Эксперимент автоматического машинного обучения можно запустить в следующих вычислительных средах.
*   Локальный компьютер или ноутбук. Как правило, при наличии небольшого набора данных и если вы по-прежнему находитесь на этапе изучения.
*   Удаленный компьютер в облаке. [Управляемая Вычислительная среда Машинного обучения Azure](concept-compute-target.md#amlcompute) — это управляемая служба, которая позволяет обучать модели машинного обучения в кластерах виртуальных машин Azure.

На [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) доступны примеры записных книжек с локальными и удаленными целевыми объектами вычислений.

*   Кластер Azure Databricks в подписке Azure. Дополнительные сведения см. здесь. [установка Azure Databricks кластера для автоматического ML](how-to-configure-environment.md#azure-databricks)

Примеры записных книжек с Azure Databricks см. на [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) .

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Настройка параметров эксперимента

Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения. Эти параметры задаются путем создания экземпляра объекта `AutoMLConfig`. Полный список параметров см. в статье [AutoMLConfig class](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) (Класс AutoMLConfig).

Некоторые примеры:

1.  Эксперимент классификации со значением AUC_weighted в качестве основной метрики с наибольшим максимальным временем в 12 000 секунд на итерацию и окончанием эксперимента после 50 итераций и 2 свертков перекрестной проверки.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Ниже приведен пример эксперимента регрессии, который заканчивается после 100 итераций, каждая из которых длится до 600 секунд и в течение которых выполняется 5 свертков перекрестной проверки.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

Три разных `task` значения параметров определяют список применяемых алгоритмов.  Используйте параметры `whitelist` или `blacklist` для дополнительного изменения итераций с помощью доступных алгоритмов для включения или исключения. Список поддерживаемых моделей можно найти в [классе суппортедалгорисмс](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Основная метрика
Основная метрика; как показано в приведенных выше примерах, определяется метрика, используемая во время обучения модели для оптимизации. Основная метрика, которую можно выбрать, определяется выбранным типом задачи. Ниже приведен список доступных метрик.

Дополнительные сведения об определенных определениях см. в статье сведения о [автоматических результатах машинного обучения](how-to-understand-automated-ml.md).

|Классификация | Регрессия | Прогнозирование временных рядов
|-- |-- |--
|accuracy| spearman_correlation; | spearman_correlation;
|AUC_weighted | normalized_root_mean_squared_error; | normalized_root_mean_squared_error;
|average_precision_score_weighted | r2_score; | r2_score;
|norm_macro_recall | normalized_mean_absolute_error; | normalized_mean_absolute_error;
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Предварительная обработка данных & Добавление признаков

В каждом автоматическом эксперименте машинного обучения данные [автоматически масштабируются и нормализованы](concept-automated-ml.md#preprocess) , чтобы помочь алгоритмам эффективно работать.  Однако можно также включить дополнительную предварительную обработку или добавление признаков, например отсутствующие значения добавления отсутствующих, кодировки и преобразования. [Узнайте больше о том, что входит в добавление признаков](how-to-create-portal-experiments.md#preprocess).

Чтобы включить этот Добавление признаков, укажите `"preprocess": True` [ `AutoMLConfig` для класса](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

> [!NOTE]
> Этапы предварительной обработки автоматизированного машинного обучения (нормализация компонентов, обработка недостающих данных, преобразование текста в числовой формат и т. д.) становятся частью базовой модели. При использовании модели для прогнозирования эти этапы предварительной обработки, которые выполнялись во время обучения, автоматически выполняются для входных данных.

### <a name="time-series-forecasting"></a>Прогнозирование временных рядов
Для типа задачи прогнозирования временных рядов вы можете определить дополнительные параметры.
1. time_column_name — это обязательный параметр, который определяет имя столбца в данных для обучения, содержащих ряды дат и времени.
1. max_horizon — определяет продолжительность прогнозирования в зависимости от периодичности обучающих данных. Например, если у вас есть обучающие данные с дневными гранями времени, то определите, сколько дней требуется для обучения модели.
1. grain_column_names — определяет имена столбцов, которые содержат отдельные данные временных рядов в обучающих данных. Например, при прогнозировании продаж определенной торговой марки по магазину необходимо определить столбцы Store и BRANDа в качестве столбцов детализации.

См. Пример использования этих параметров ниже, пример записной книжки доступен [здесь](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Конфигурация ансамблей

Модели ансамблей включены по умолчанию и отображаются как окончательные итерации выполнения в автоматическом запуске машинного обучения. Поддерживаемые в настоящее время методы ансамблей — это голосование и стек. Голосование реализуется как мягкое голосование с помощью взвешенных средних значений, а реализация стека использует реализацию 2 уровня, где первый слой имеет те же модели, что и ансамблей голосования, а вторая модель слоев используется для поиска оптимального сочетания модели из первого слоя. Если используются модели ONNX **или** включено объяснение модели, то стек будет отключен и будут использоваться только голосование.

Существует несколько аргументов по умолчанию, которые могут быть `kwargs` предоставлены `AutoMLConfig` как в объекте для изменения поведения ансамблей стека по умолчанию.

* `stack_meta_learner_type`: мета-изучение — это модель, обученная на выходе отдельных моделей разнородных. Мета-знания по умолчанию `LogisticRegression` предназначены для задач классификации ( `LogisticRegressionCV` или если включена перекрестная проверка), а `ElasticNet` также для задач «регрессия» и « `ElasticNetCV` прогнозирование» (или при включенной перекрестной проверке). Этот параметр может быть одной из `LogisticRegression`следующих строк:, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV` `LightGBMRegressor`, или `LinearRegression`.
* `stack_meta_learner_train_percentage`: определяет пропорцию обучающего набора (при выборе типа обучения и проверки), зарезервированного для обучения мета-знания. По умолчанию имеет значение `0.2`.
* `stack_meta_learner_kwargs`: необязательные параметры для передачи в инициализатор мета-знания. Эти параметры и типы параметров отражаются на основе соответствующего конструктора модели и пересылаются конструктору модели.

В следующем коде показан пример указания пользовательского поведения ансамблей в `AutoMLConfig` объекте.

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
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Обучение ансамблей включено по умолчанию, но его можно отключить с помощью `enable_voting_ensemble` логических параметров и. `enable_stack_ensemble`

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Выполнение эксперимента

Для автоматического создания машинного обучения `Experiment` создается объект, который является именованным объектом в `Workspace` , используемом для выполнения экспериментов.

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

### <a name="exit-criteria"></a>Условия выхода
Существует несколько параметров, которые можно определить для завершения эксперимента.
1. Без критериев. Если параметры выхода не определены, эксперимент будет продолжен до тех пор, пока не будет выполнен дальнейший переход к основной метрике.
1. Число итераций — вы определяете число итераций для запуска эксперимента. Можно дополнительно добавить iteration_timeout_minutes, чтобы определить предельное время в минутах для каждой итерации.
1. Завершите работу по истечении длительного времени. с помощью experiment_timeout_minutes можно определить продолжительность выполнения эксперимента в минутах.
1. Выход после достижения оценки. с помощью experiment_exit_score можно выбрать Завершение эксперимента после достижения оценки на основе основной метрики.

### <a name="explore-model-metrics"></a>Изучение метрик модели

Вы можете просмотреть результаты обучения в мини-приложении или встроенном приложении, если вы используете записную книжку. Ознакомьтесь с разделом [Просмотр сведений о выполнении](how-to-track-experiments.md#view-run-details), чтобы получить дополнительные сведения.

## <a name="understand-automated-ml-models"></a>Знакомство с автоматизированными моделями ML

Любая модель, созданная с помощью автоматизированного ML, включает следующие шаги:
+ Автоматизированное проектирование компонентов (если предварительная обработка = true)
+ Масштабирование, нормализация и алгоритм с использованием значений счетчиков

Мы сделаем его прозрачным, чтобы получить эту информацию из выходных данных fitted_model из автоматизированного ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Автоматизированное проектирование признаков

См. список предварительной обработки и [автоматизированной разработки](concept-automated-ml.md#preprocess) признаков, которая происходит при предварительной обработке = true.

Рассмотрим следующий пример.
+ Существует 4 функции ввода: A (числовой), B (числовой), C (numeric), D (DateTime)
+ Цифровая характеристика C удалена, так как это столбец ИДЕНТИФИКАТОРов со всеми уникальными значениями
+ Числовые функции A и B содержат отсутствующие значения, поэтому они добавленные по среднему значению
+ Функция DateTime D признаками в 11 различных сконструированных функций

Используйте эти два API на первом шаге модели, чтобы понять больше.  См. [Этот пример записной книжки](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` возвращает список имен сконструированных функций.

  Использование:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Этот список включает все имена сконструированных функций.

  >[!Note]
  >Используйте "тимесериестрансформер" для задачи = "прогнозирование"; в противном случае используйте "Преобразователь" для задачи "регрессия" или "Классификация".

+ API 2: `get_featurization_summary()` Возвращает сводку Добавление признаков для всех входных функций.

  Использование:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Используйте "тимесериестрансформер" для задачи = "прогнозирование"; в противном случае используйте "Преобразователь" для задачи "регрессия" или "Классификация".

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

   Описание

   |Вывод|Определение|
   |----|--------|
   |равфеатуренаме|Имя входного компонента или столбца из предоставленного набора данных.|
   |типедетектед|Обнаружен тип данных функции ввода.|
   |Рывает|Указывает, была ли функция ввода удалена или использована.|
   |енгинирингфеатурекаунт|Количество функций, созданных с помощью автоматизированных преобразований «разработка компонентов».|
   |Преобразования|Список преобразований, применяемых к функциям ввода для создания сконструированных функций.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Масштабирование, нормализация и алгоритм со значениями счетчиков:

Чтобы получить представление о масштабировании, нормализации и значении параметра Algorithm и параметров в конвейере, используйте fitted_model. шаги. Дополнительные [сведения о масштабировании и нормализации](concept-automated-ml.md#preprocess). Пример выходных данных:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Чтобы получить дополнительные сведения, используйте эту вспомогательную функцию, показанную в [этом примере записной книжки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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

Ниже приведен пример выходных данных для конвейера с использованием определенного алгоритма (Логистикрегрессион с Робустскалар, в данном случае).

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

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Объясните модель (интерпретируемость)

Автоматическое машинное обучение позволяет понять важность признаков.  В процессе обучения вы можете получить глобальные показатели важности признаков для модели.  Для сценариев классификации можно также получить показатели важности признаков на уровне класса.  Необходимо указать проверочный набора данных (X_valid), чтобы получить показатели важности признаков.

Существуют два способа создания этих значений.

*   После завершения эксперимента можно использовать метод `explain_model` для любой итерации.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Чтобы просмотреть показатели важности для всех итераций, задайте для флага `model_explainability` значение `True` в AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    После этого можно использовать метод retrieve_model_explanation, чтобы получить показатели важности признаков для конкретной итерации.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

Отобразите URL-адрес для просмотра важности функции с помощью объекта Run:

```
automl_run.get_portal_url()
```

Диаграмму важности функций можно визуализировать в рабочей области в портал Azure или на [целевой странице рабочей области (Предварительная версия)](https://ml.azure.com). Диаграмма также отображается при использовании `RunDetails` мини-приложения [Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) в записной книжке. Дополнительные сведения о диаграммах см. в статье сведения об [автоматических результатах машинного обучения](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![Диаграмма важности признаков](./media/how-to-configure-auto-train/feature-importance.png)

Дополнительные сведения о том, как пояснения к модели и важность признаков можно включить в других областях пакета SDK за пределами автоматизированного машинного обучения, см. в статье [концепции](machine-learning-interpretability-explainability.md) , посвященной возможностям интерпретации.

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о том, [как и где можно развернуть модель](how-to-deploy-and-where.md).

Узнайте больше о [том, как обучить модель регрессии с помощью автоматизированного машинного обучения](tutorial-auto-train-models.md) или [обучения использованию автоматизированного машинного обучения на удаленном ресурсе](how-to-auto-train-remote.md).
