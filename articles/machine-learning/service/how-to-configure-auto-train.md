---
title: Создание эксперимента автоматического машинного обучения
titleSuffix: Azure Machine Learning service
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
ms.openlocfilehash: 0d9019a6b4a32066480a70f72562bc5a7a9a1e8b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797652"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Настройка автоматических эксперименты машинного Обучения на Python

В этом руководстве рассматриваются способы определения различных параметров конфигурации из автоматических эксперименты машинного обучения с [пакета SDK для Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Автоматическое машинное обучение выбирает алгоритм и гиперпараметры, а также создает модель, готовую для развертывания. Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения.

Примеры экспериментов автоматического машинного обучения см. в разделе [Руководство. Создание регрессионной модели с помощью автоматического машинного обучения](tutorial-auto-train-models.md) или [Обучение моделей с помощью автоматического машинного обучения в облаке](how-to-auto-train-remote.md).

Возможности настройки, доступные в автоматическом машинном обучении:

* выбор типа эксперимента: Классификации, регрессии или прогнозирования временных рядов
* источник данных, форматы и получение данных;
* выбор целевого объекта вычислений (локальный или удаленный);
* настройка параметров эксперимента автоматического машинного обучения;
* выполнение эксперимента автоматического машинного обучения;
* Изучение метрик модели
* регистрация и развертывание модели.

Если вы предпочитаете не предусмотрена возможность код, вы также можете [создание автоматических эксперименты машинного обучения на портале Azure](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Выбор типа эксперимента

Прежде чем начать эксперимент, следует определить тип задачи машинного обучения, которую необходимо решить. Автоматическое машинное обучение поддерживает задачи классификации, регрессии и прогнозирования.

Эта служба также поддерживает приведенные ниже алгоритмы для автоматизации и настройки. Пользователю не нужно указывать алгоритм. 

классификация; | Регрессия | Прогнозирование временных рядов
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
[xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Регрессор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Регрессор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Линейный классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Линейной регрессии](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Линейной регрессии](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Используйте `task` параметр в `AutoMLConfig` конструктора для указания типа вашего эксперимента.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Источник данных и формат
Автоматическое машинное обучение поддерживает данные, находящиеся на локальном компьютере или в облаке в хранилище BLOB-объектов Azure. Данные можно считать в поддерживаемых форматах данных scikit-learn. Можно считывать данные в:
* массивы Numpy X (признаки) и Y (целевая переменная или метка);
* Кадр данных Pandas

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

Если эксперимент выполняется на удаленных вычислительных ресурсах, получение данных должно быть помещено в отдельный сценарий Python `get_data()`. Этот сценарий выполняется на удаленных вычислительных ресурсах, в которых выполняется эксперимент автоматического машинного обучения. `get_data` избавляет от необходимости получать данные по сети для каждой итерации. Без `get_data` эксперимент завершится ошибкой при выполнении на удаленных вычислительных ресурсах.

Вот пример `get_data`:

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

В вашем объекте `AutoMLConfig` можно указать параметр `data_script` и ввести путь к файлу сценария `get_data`, как показано ниже:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

Сценарий `get_data` может вернуть:

Ключ | Тип | Взаимно исключают друг друга с помощью    | Описание
---|---|---|---
X | Кадр данных Pandas или массив Numpy | data_train, метка, столбцы |  Все признаки для обучения
y | Кадр данных Pandas или массив Numpy |   label   | Данные метки для обучения. Для классификации это должен быть массив целых чисел
X_valid | Кадр данных Pandas или массив Numpy   | data_train, метка | _Необязательный_ признаков. данные, относящиеся к формирующему набора для проверки. Если не указано, X разделяется на экземпляры для обучения и проверки
y_valid |   Кадр данных Pandas или массив Numpy | data_train, метка | _Необязательно._ Данные метки для проверки. Если не указано, y разделяется на экземпляры для обучения и проверки
sample_weight | Кадр данных Pandas или массив Numpy |   data_train, метка, столбцы| _Необязательно._ Значение веса для каждой выборки. Используйте, если необходимо назначить разный вес для точек данных
sample_weight_valid | Кадр данных Pandas или массив Numpy | data_train, метка, столбцы |    _Необязательно._ Значение веса для каждой выборки для проверки. Если не указано, sample_weight разделяется на экземпляры для обучения и проверки
data_train |    Кадр данных Pandas |  X, y, X_valid, y_valid |    Все данные (признаки и метка) для обучения
label | строка  | X, y, X_valid, y_valid |  Какой столбец в data_train представляет метку
columns | Массив строк  ||  _Необязательно._ Список разрешений столбцов для использования с признаками
cv_splits_indices   | массив целых чисел ||  _Необязательно._ Список индексов для разделения данных для перекрестной проверки

## <a name="train-and-validation-data"></a>Обучение и проверка данных

Указать отдельный набор для обучения и проверки можно с помощью get_data() либо непосредственно в методе `AutoMLConfig`.

### <a name="k-folds-cross-validation"></a>Перекрестная проверка по K-сверткам

Используйте параметр `n_cross_validations`, чтобы указать количество перекрестных проверок. Набор данных для обучения будет разделен случайным образом на свертки `n_cross_validations` одинакового размера. Во время каждого цикла перекрестной проверки один из свертков будет использоваться для проверки модели, обученной на оставшихся свертках. Этот процесс повторяется для циклов `n_cross_validations`, пока каждый сверток не будет использован один раз в качестве набора для проверки. Будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Монте Карло перекрестная проверка (повторяющиеся случайной вложенной выборки)

С помощью `validation_size` укажите процент набора данных для обучения, который должен использоваться для проверки, а с помощью `n_cross_validations` укажите число перекрестных проверок. Во время каждого цикла перекрестной проверки подмножество размера `validation_size` будет выбрано случайным образом для проверки модели, обученной с использованием оставшихся данных. Наконец, будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения. Монте-Карло не поддерживается для прогнозирования временных рядов.

### <a name="custom-validation-dataset"></a>Настраиваемый проверочный набор данных

Используйте пользовательскую проверку набора данных, если случайного разбиения неприемлем, обычно данные временных рядов или несбалансированных данных. Можно указать собственный проверочный набор данных. Модель будет анализироваться с использованием проверочного набора данных, указанного вместо случайного набора данных.

## <a name="compute-to-run-experiment"></a>Объект вычислений для выполнения эксперимента

Затем следует определить, где модель будет обучаться. Эксперимент автоматического машинного обучения можно запустить в следующих вычислительных средах.
*   Локальный компьютер или ноутбук. Как правило, при наличии небольшого набора данных и если вы по-прежнему находитесь на этапе изучения.
*   Удаленный компьютер в облаке. [Управляемая Вычислительная среда Машинного обучения Azure](concept-compute-target.md#amlcompute) — это управляемая служба, которая позволяет обучать модели машинного обучения в кластерах виртуальных машин Azure.

На [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) доступны примеры записных книжек с локальными и удаленными целевыми объектами вычислений.

*   Кластер Azure Databricks в вашей подписке Azure. Дополнительные сведения см. здесь: [Azure Databricks установки кластера для автоматической машинного Обучения](how-to-configure-environment.md#azure-databricks)

См. в разделе [сайт GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) например записные книжки с Azure Databricks.

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

С тремя `task` значения параметра определить список алгоритмов для применения.  Используйте параметры `whitelist` или `blacklist` для дополнительного изменения итераций с помощью доступных алгоритмов для включения или исключения. Список поддерживаемых моделей можно найти на [SupportedAlgorithms класс](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Основная метрика
Основной показатель; как показано в приведенных выше примерах, определяет метрику для использования во время обучения модели для оптимизации. Основным показателем, который можно выбрать определяется тип задачи, который выбран. Ниже приведен список доступных метрик.

|классификация; | Регрессия | Прогнозирование временных рядов
|-- |-- |--
|accuracy| spearman_correlation; | spearman_correlation;
|AUC_weighted | normalized_root_mean_squared_error; | normalized_root_mean_squared_error;
|average_precision_score_weighted | r2_score; | r2_score;
|norm_macro_recall | normalized_mean_absolute_error; | normalized_mean_absolute_error;
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Предварительная обработка данных и добавление признаков

В каждом автоматических эксперимента машинного обучения, данные будут [автоматически масштабируется и нормализовать](concept-automated-ml.md#preprocess) для алгоритмов, которые выполняются надлежащим образом.  Тем не менее можно также включить дополнительные предварительной обработки/Добавление признаков, например, отсутствие отсутствующих значений, кодирования и преобразования. [Дополнительные сведения о какие Добавление признаков включено](how-to-create-portal-experiments.md#preprocess).

Чтобы включить это добавление признаков, укажите `"preprocess": True` для [ `AutoMLConfig` класс](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Прогнозирование временных рядов
Для типа задачи прогнозирования ряда времени у вас есть дополнительные параметры для определения.
1. time_column_name — это обязательный параметр, который определяет имя столбца, содержащего даты и времени ряда обучающих данных.
1. max_horizon - определяет продолжительность времени, который требуется спрогнозировать основании периодичности обучающих данных. Например при наличии данных для обучения с ежедневной интервалов времени, вы определить, насколько далеко в днях должна обучить модель.
1. grain_column_names - определяет имена столбцов, которые содержат данные отдельных временных рядов в обучающих данных. Например если выполнить прогноз продаж по магазинам отдельной марки, хранилище и торговой марки столбцы бы определить как столбцы детализации.

См. пример из этих параметров ниже, пример записной книжки доступен [здесь](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

## <a name="run-experiment"></a>Выполнение эксперимента

Для автоматических машинного Обучения необходимо создать `Experiment` объект, являющийся именованной объекта в `Workspace` используется для выполнения экспериментов.

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
Существует несколько вариантов можно определить для выполнения эксперимента.
1. Критерии не - Если вы не определяйте любые выйдите из параметров, которые эксперимент будет продолжаться, пока не дальнейших выполнено на основной метрик.
1. Число итераций - определить число итераций для эксперимента для запуска. Вы можете необязательно добавлять iteration_timeout_minutes определить ограничение по времени в минутах, в каждой итерации.
1. Выйдите из после периода времени — с помощью experiment_timeout_minutes в настройках, которые можно определить продолжительность в минутах следует продолжить эксперимента в сеансе.
1. Выйдите из после оценки достижения - с помощью experiment_exit_score, вы можете выполнить эксперимент после достижения оценку на основе вашей основной метрики.

### <a name="explore-model-metrics"></a>Изучение метрик модели

Можно просмотреть результаты обучения в мини-приложение или встроенная в записную книжку. Ознакомьтесь с разделом [Просмотр сведений о выполнении](how-to-track-experiments.md#view-run-details), чтобы получить дополнительные сведения.

## <a name="understand-automated-ml-models"></a>Понять автоматических моделей машинного Обучения

Все модели, формируемой с помощью автоматических машинного Обучения включает следующие шаги:
+ Автоматизировать проектирование признаков (если предварительная обработка = True)
+ Масштабирование нормализации и алгоритм hypermeter значениями

Мы сделать прозрачной для получения этой информации из выходных данных fitted_model из автоматических машинного Обучения.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Проектирование автоматических функций

См. в списке предварительной обработки и [автоматической признаков](concept-automated-ml.md#preprocess) происходит, когда предварительная обработка = True.

Рассмотрим следующий пример.
+ Существует 4 входные признаки: A (числовые) B (числовой) (числовой) C, D (DateTime)
+ Удаляется числовых признаков C, так как он является столбцом идентификатора с все уникальные значения
+ Числовые функции A и B имеют отсутствующие значения и таким образом считать среднее
+ Функция DateTime D — признаки в 11 различных реконструированные признаки

Используйте эти 2 API в сначала соединяются гладкой модели, чтобы узнать больше.  См. в разделе [эту записную книжку образец](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` возвращает список имен реконструирования.

  Использование:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Этот список входят все имена реконструирования.

  >[!Note]
  >Используйте для задачи «timeseriestransformer» = «прогнозирование», воспользуйтесь «datatransformer» для задачи «Регрессия» или «классификации».

+ API 2: `get_featurization_summary()` возвращает сводку для всех входных функций Добавление признаков.

  Использование:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Используйте для задачи «timeseriestransformer» = «прогнозирование», воспользуйтесь «datatransformer» для задачи «Регрессия» или «классификации».

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
   |RawFeatureName|Имя ввода функции или столбца в набор данных.|
   |TypeDetected|Тип данных, обнаруженных входной функции.|
   |Удалены|Указывает, если функция ввода удалена или использовать.|
   |EngineeringFeatureCount|Количество функций, созданных с помощью преобразований engineering автоматическая функция.|
   |Преобразования|Список преобразования входных данных функции для создания реконструированные признаки.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Масштабирование нормализации и алгоритм hypermeter значениями:

Чтобы понять значения масштабирования нормализации и алгоритм/гиперпараметров для конвейера, используйте fitted_model.steps. [Дополнительные сведения о масштабировании нормализации](concept-automated-ml.md#preprocess). Пример выходных данных:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Чтобы получить дополнительные сведения, используйте Эта вспомогательная функция, показанный на [эту записную книжку образец](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

Ниже приведен пример выходных данных для конвейера с помощью конкретного алгоритма (LogisticRegression с RobustScalar, в данном случае).

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

## <a name="explain-the-model-interpretability"></a>Описание модели (interpretability)

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

Вы можете отобразить диаграмму важности признаков в рабочей области на портале Azure. Эта диаграмма отображается также при использовании мини-приложения Jupyter в записной книжке. Дополнительные сведения о диаграммах см. в статье [Использование записных книжек Jupyter в Azure Machine Learning Workbench](samples-notebooks.md).

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![Диаграмма важности признаков](./media/how-to-configure-auto-train/feature-importance.png)

Дополнительные сведения о как можно включить объяснения модели и показатели важности компонентов в других частях пакета SDK за пределами автоматических машинного обучения, см. в разделе [концепция](machine-learning-interpretability-explainability.md) статьи на interpretability.

## <a name="next-steps"></a>Следующие шаги

Узнайте больше о том, [как и где можно развернуть модель](how-to-deploy-and-where.md).

Дополнительные сведения о [как обучить модель регрессии с помощью машинного обучения автоматическая](tutorial-auto-train-models.md) или [Практическое обучение с помощью автоматической машинного обучения на удаленном ресурсе](how-to-auto-train-remote.md).
