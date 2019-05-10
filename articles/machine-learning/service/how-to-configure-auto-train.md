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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 64ba7096f181371a378708e024f46bce17449e98
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510583"
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

Эта служба также поддерживает приведенные ниже алгоритмы для автоматизации и настройки. Пользователю не нужно указывать алгоритм. Хотя алгоритмы DNN доступны во время обучения, автоматических машинного Обучения не выполняет сборку моделей DNN.

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
[xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Регрессор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Регрессор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Линейный классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Линейной регрессии](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Линейной регрессии](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|


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
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
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

Ключ | type | Взаимно исключают друг друга с помощью    | Описание
---|---|---|---
X | Кадр данных Pandas или массив Numpy | data_train, метка, столбцы |  Все признаки для обучения
y | Кадр данных Pandas или массив Numpy |   label   | Данные метки для обучения. Для классификации это должен быть массив целых чисел
X_valid | Кадр данных Pandas или массив Numpy   | data_train, метка | _Необязательно._ Все признаки для проверки. Если не указано, X разделяется на экземпляры для обучения и проверки
y_valid |   Кадр данных Pandas или массив Numpy | data_train, метка | _Необязательно._ Данные метки для проверки. Если не указано, y разделяется на экземпляры для обучения и проверки
sample_weight | Кадр данных Pandas или массив Numpy |   data_train, метка, столбцы| _Необязательно._ Значение веса для каждой выборки. Используйте, если необходимо назначить разный вес для точек данных
sample_weight_valid | Кадр данных Pandas или массив Numpy | data_train, метка, столбцы |    _Необязательно._ Значение веса для каждой выборки для проверки. Если не указано, sample_weight разделяется на экземпляры для обучения и проверки
data_train |    Кадр данных Pandas |  X, y, X_valid, y_valid |    Все данные (признаки и метка) для обучения
label | string  | X, y, X_valid, y_valid |  Какой столбец в data_train представляет метку
columns | Массив строк  ||  _Необязательно._ Список разрешений столбцов для использования с признаками
cv_splits_indices   | массив целых чисел ||  _Необязательно._ Список индексов для разделения данных для перекрестной проверки

### <a name="load-and-prepare-data-using-data-prep-sdk"></a>Загрузка и Подготовка данных с помощью подготовки данных в пакет SDK
Автоматические экспериментов машинного обучения поддерживает загрузку данных и преобразования, с помощью пакета SDK для подготовки данных. Этот пакет SDK предоставляет следующие возможности:

>* загрузка из файлов многих типов с автоматическим обнаружением параметров для синтаксического анализа (кодировка, разделитель, заголовки);
>* преобразование типов на основе зависимостей во время загрузки файла;
>* поддержка подключений к MS SQL Server и хранилищу Azure Data Lake.
>* Добавление столбца с помощью выражения
>* Добавление отсутствующих значений
>* Получение столбцов по образцу
>* Фильтрация
>* Пользовательские преобразования Python

Дополнительные сведения о пакете SDK для подготовки данных см. в статье [Как подготовить данные для моделирования](how-to-load-data.md).
Ниже приведен пример загрузки данных с помощью пакета SDK для подготовки данных.
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Обучение и проверка данных

Указать отдельный набор для обучения и проверки можно с помощью get_data() либо непосредственно в методе `AutoMLConfig`.

## <a name="cross-validation-split-options"></a>Варианты разделения для перекрестной проверки

### <a name="k-folds-cross-validation"></a>Перекрестная проверка по K-сверткам

Используйте параметр `n_cross_validations`, чтобы указать количество перекрестных проверок. Набор данных для обучения будет разделен случайным образом на свертки `n_cross_validations` одинакового размера. Во время каждого цикла перекрестной проверки один из свертков будет использоваться для проверки модели, обученной на оставшихся свертках. Этот процесс повторяется для циклов `n_cross_validations`, пока каждый сверток не будет использован один раз в качестве набора для проверки. Будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения. 

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Монте Карло перекрестная проверка (повторяющиеся случайной вложенной выборки)

С помощью `validation_size` укажите процент набора данных для обучения, который должен использоваться для проверки, а с помощью `n_cross_validations` укажите число перекрестных проверок. Во время каждого цикла перекрестной проверки подмножество размера `validation_size` будет выбрано случайным образом для проверки модели, обученной с использованием оставшихся данных. Наконец, будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения. Монте-Карло не поддерживается для прогнозирования временных рядов.

### <a name="custom-validation-dataset"></a>Настраиваемый проверочный набор данных

Используйте пользовательскую проверку набора данных, если случайного разбиения неприемлем, обычно данные временных рядов или несбалансированных данных. Можно указать собственный проверочный набор данных. Модель будет анализироваться с использованием проверочного набора данных, указанного вместо случайного набора данных.

## <a name="compute-to-run-experiment"></a>Объект вычислений для выполнения эксперимента

Затем следует определить, где модель будет обучаться. Эксперимент автоматического машинного обучения можно запустить в следующих вычислительных средах.
*   Локальный компьютер или ноутбук. Как правило, при наличии небольшого набора данных и если вы по-прежнему находитесь на этапе изучения.
*   Удаленный компьютер в облаке. [Управляемая Вычислительная среда Машинного обучения Azure](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) — это управляемая служба, которая позволяет обучать модели машинного обучения в кластерах виртуальных машин Azure.

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

## <a name="primary-metric"></a>Основная метрика
Основной показатель; как показано в приведенных выше примерах, определяет метрику для использования во время обучения модели для оптимизации. Основным показателем, который можно выбрать определяется тип задачи, который выбран. Ниже приведен список доступных метрик.

|Классификация | Регрессия | Прогнозирование временных рядов
|-- |-- |--
|accuracy| spearman_correlation; | spearman_correlation;
|AUC_weighted | normalized_root_mean_squared_error; | normalized_root_mean_squared_error;
|average_precision_score_weighted | r2_score; | r2_score;
|norm_macro_recall | normalized_mean_absolute_error; | normalized_mean_absolute_error;
|precision_score_weighted |

## <a name="data-preprocessing--featurization"></a>Предварительная обработка данных и добавление признаков

В каждом автоматических эксперимента машинного обучения, данные будут [автоматически масштабируется и нормализовать](concept-automated-ml.md#preprocess) для алгоритмов, которые выполняются надлежащим образом.  Тем не менее можно также включить дополнительные предварительной обработки/Добавление признаков, например, отсутствие отсутствующих значений, кодирования и преобразования. [Дополнительные сведения о какие Добавление признаков включено](how-to-create-portal-experiments.md#preprocess). 

Чтобы включить это добавление признаков, укажите `"preprocess": True` для [ `AutoMLConfig` класс](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="time-series-forecasting"></a>Прогнозирование временных рядов
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

Отправьте эксперимент для выполнения и создания модели. Передайте `AutoMLConfig` в метод `submit`, чтобы создать модель.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Сначала зависимости устанавливаются на новую виртуальную машину.  Это может занять до 10 минут, прежде чем отобразятся выходные данные.
>Если для параметра `show_output` задать значение `True`, выходные данные отобразятся в консоли.

## <a name="exit-criteria"></a>Критерии выхода 
Существует несколько вариантов можно определить для выполнения эксперимента.
1. Критерии не - Если вы не определяйте любые выйдите из параметров, которые эксперимент будет продолжаться, пока не дальнейших выполнено на основной метрик. 
1. Число итераций - определить число итераций для эксперимента для запуска. Вы можете необязательно добавлять iteration_timeout_minutes определить ограничение по времени в минутах, в каждой итерации.
1. Выйдите из после периода времени — с помощью experiment_timeout_minutes в настройках, которые можно определить продолжительность в минутах следует продолжить эксперимента в сеансе.
1. Выйдите из после оценки достижения - с помощью experiment_exit_score, вы можете выполнить эксперимент после достижения оценку на основе вашей основной метрики.

## <a name="explore-model-metrics"></a>Изучение метрик модели
Просмотреть результаты можно в мини-приложении или во встроенном окне при работе с записной книжкой. Ознакомьтесь с разделом [Просмотр сведений о выполнении](how-to-track-experiments.md#view-run-details), чтобы получить дополнительные сведения.


### <a name="classification-metrics"></a>Метрики классификации
В каждой итерации для задачи классификации сохраняются следующие метрики.

|Метрика|Описание|Вычисление|Дополнительные параметры
--|--|--|--|
AUC_Macro| AUC представляет собой область под кривой рабочих характеристик приемника (ROC). Макрозначение — это среднее арифметическое значение AUC для каждого класса.  | [Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC представляет собой область под кривой рабочих характеристик приемника (ROC). Микрозначение вычисляется глобально путем объединения истинно положительных и истинно отрицательных результатов из каждого класса.| [Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC представляет собой область под кривой рабочих характеристик приемника (ROC). Взвешенное значение — среднее арифметическое значение оценки для каждого класса, взвешенное по числу экземпляров значения true в каждом классе.| [Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Точность — это процент прогнозируемых меток, которые точно соответствуют истинным меткам. |[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Нет|
average_precision_score_macro|Для вычисления средней точности используется кривая точности и полноты в качестве взвешенного среднего значения точности, полученного для каждого порогового значения, с увеличением полноты за счет предыдущего порогового значения, используемого в качестве весового коэффициента. Макрозначение — это арифметическое среднее значение точности оценки каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Для вычисления средней точности используется кривая точности и полноты в качестве взвешенного среднего значения точности, полученного для каждого порогового значения, с увеличением полноты за счет предыдущего порогового значения, используемого в качестве весового коэффициента. Микрозначение вычисляется глобально путем объединения истинно положительных и истинно отрицательных результатов для каждого порогового значения.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Для вычисления средней точности используется кривая точности и полноты в качестве взвешенного среднего значения точности, полученного для каждого порогового значения, с увеличением полноты за счет предыдущего порогового значения, используемого в качестве весового коэффициента. Взвешенное значение — среднее арифметическое значение для средней оценки точности для каждого класса, взвешенное по числу экземпляров значения true в каждом классе.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Сбалансированная точность — это среднее арифметическое значение полноты для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|Оценка F1 — это среднее гармоническое значение точности и полноты. Макрозначение — это среднее арифметическое оценки F1 для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|Оценка F1 — это среднее гармоническое значение точности и полноты. Микрозначение вычисляется глобально путем подсчета общего числа истинно положительных, ложно отрицательных и истинно отрицательных результатов.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|Оценка F1 — это среднее гармоническое значение точности и полноты. Взвешенное среднее значение по частоте класса оценки F1 для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Это функция потерь, используемая в логистической регрессии (полиномиальной) и ее дополнениях, например нейронных сетях, которая определяется как вероятность логистических потерь истинных меток для заданных прогнозов вероятностного классификатора. Для отдельной выборки с истинной меткой yt в {0,1} и оценочной вероятностью yp при yt = 1 логистические потери составят –log P(yt&#124;yp) = –(yt log(yp) + (1 – yt) log(1 – yp)).|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Нет|
norm_macro_recall|Нормализованная полнота макрозначений — это полнота макрозначений, нормализованная таким образом, чтобы случайному выполнению соответствовала оценка 0, а идеальному выполнению — оценка 1. Для этого используется формула norm_macro_recall := (recall_score_macro – R)/(1 – R), где R — ожидаемое значение recall_score_macro для произвольных прогнозов (т. е. R = 0,5 для задач бинарной классификации и R = (1/C) задач C-классовой классификации).|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro", затем (recall_score_macro – R)/(1 – R), где R — ожидаемое значение recall_score_macro для произвольных прогнозов (т. е. R = 0,5 для задач бинарной классификации и R = (1/C) задач C-классовой классификации).|
precision_score_macro|Точность — это процент элементов, отнесенных к определенному классу, которые действительно находятся в этом классе. Макрозначение — это среднее арифметическое значение точности для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Точность — это процент элементов, отнесенных к определенному классу, которые действительно находятся в этом классе. Микрозначение вычисляется глобально путем подсчета общего числа истинно положительных, ложно отрицательных и истинно отрицательных результатов.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Точность — это процент элементов, отнесенных к определенному классу, которые действительно находятся в этом классе. Взвешенное значение — среднее арифметическое значение точности для каждого класса, взвешенное по числу экземпляров значения true в каждом классе.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Полнота — это процент элементов, которые фактически находятся в определенном классе и правильно помечены. Макрозначение — это среднее арифметическое значение полноты для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Полнота — это процент элементов, которые фактически находятся в определенном классе и правильно помечены. Микрозначение вычисляется глобально путем подсчета общего числа истинно положительных и ложно отрицательных результатов.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Полнота — это процент элементов, которые фактически находятся в определенном классе и правильно помечены. Взвешенное значение — среднее арифметическое значение полноты для каждого класса, взвешенное по числу экземпляров значения true в каждом классе.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Взвешенная точность — это точность, для вычисления которой каждой выборке присваивается вес, соответствующий доле экземпляров true в классе true для этой выборки.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|Значение sample_weight представляет собой вектор, равный доле этого класса для каждого элемента в целевом элементе.|

### <a name="regression-and-time-series-forecasting-metrics"></a>Прогнозирование метрик регрессии и время рядов
В каждой итерации для задачи регрессии или прогнозирования сохраняются следующие метрики.

|Метрика|Описание|Вычисление|Дополнительные параметры
--|--|--|--|
explained_variance|Объяснимая дисперсия — это доля, учитываемая математической моделью при вычислении дисперсии заданного набора данных. Это процент уменьшения дисперсии исходных данных по отношению к дисперсии ошибок. Если среднее значение ошибок равно 0, оно равно объяснимой дисперсии.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Нет|
r2_score;|R2 — это коэффициент определения или процентное снижение квадратичных ошибок по сравнению с базовой моделью, которая выводит среднее значение. Если среднее значение ошибок равно 0, оно равно объяснимой дисперсии.|[Вычисление](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Нет|
spearman_correlation;|Корреляция Спирмена — это непараметрическая мера монотонности связи между двумя наборами данных. В отличие от корреляции Пирсона, для корреляции Спирмена не предполагается, что оба набора данных используют нормальное распределение. Как и другие коэффициенты корреляции, этот коэффициент принимает значения от –1 до + 1. Значение 0 означает отсутствие корреляции. Значения корреляции –1 и + 1 означают точную монотонную связь. Положительные значения корреляции означают, что при увеличении значения x также увеличивается значение y. Отрицательные значения корреляции означают, что при увеличении значения x значение y уменьшается.|[Вычисление](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Нет|
mean_absolute_error|Средняя абсолютная погрешность — это оценочная величина абсолютного значения отклонения между целевым и прогнозируемым значениями.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Нет|
normalized_mean_absolute_error;|Нормализованная средняя абсолютная погрешность равна средней абсолютной погрешности, деленной на диапазон данных.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Деление на диапазон данных|
median_absolute_error|Медиана абсолютной погрешности — это медиана всех абсолютных отклонений между целевым и прогнозируемым значениями. Такая потеря устойчива к выбросам.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Нет|
normalized_median_absolute_error|Нормализованная медиана абсолютной погрешности равна медиане абсолютной погрешности, деленной на диапазон данных.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Деление на диапазон данных|
root_mean_squared_error|Среднеквадратическая погрешность — это среднеквадратическое значение ожидаемого квадратичного отклонения между целевым и прогнозируемыми значениями.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Нет|
normalized_root_mean_squared_error;|Нормализованная среднеквадратическая погрешность равна среднеквадратической погрешности, деленной на диапазон данных.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Деление на диапазон данных|
root_mean_squared_log_error|Среднеквадратическая логарифмическая погрешность — это среднеквадратическое значение ожидаемой квадратичной логарифмической погрешности.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Нет|
normalized_root_mean_squared_log_error;|Нормализованная среднеквадратическая логарифмическая погрешность равна среднеквадратической логарифмической погрешности, деленной на диапазон данных.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Деление на диапазон данных|


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
   
   |Выход|Определение|
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

Ниже приведен пример выходных данных.

+ Конвейер с помощью конкретного алгоритма (LogisticRegression с RobustScalar в данном случае):

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
  
+ Конвейер с помощью совокупности подход: В данном случае это совокупности 2 разных конвейерах

  ```
  prefittedsoftvotingclassifier
  {'estimators': ['1', '18'],
  'weights': [0.6666666666666667,
              0.3333333333333333]}

  1 - RobustScaler
  {'copy': True,
   'quantile_range': [25, 75],
   'with_centering': True,
   'with_scaling': False}
  
  1 - LightGBMClassifier
  {'boosting_type': 'gbdt',
   'class_weight': None,
   'colsample_bytree': 0.2977777777777778,
   'importance_type': 'split',
   'learning_rate': 0.1,
   'max_bin': 30,
   'max_depth': 5,
   'min_child_samples': 6,
   'min_child_weight': 5,
   'min_split_gain': 0.05263157894736842,
   'n_estimators': 200,
   'n_jobs': 1,
   'num_leaves': 176,
   'objective': None,
   'random_state': None,
   'reg_alpha': 0.2631578947368421,
   'reg_lambda': 0,
   'silent': True,
   'subsample': 0.8415789473684211,
   'subsample_for_bin': 200000,
   'subsample_freq': 0,
   'verbose': -10}
  
  18 - StandardScalerWrapper
  {'class_name': 'StandardScaler',
   'copy': True,
   'module_name': 'sklearn.preprocessing.data',
   'with_mean': True,
   'with_std': True}
  
  18 - LightGBMClassifier
  {'boosting_type': 'goss',
   'class_weight': None,
   'colsample_bytree': 0.2977777777777778,
   'importance_type': 'split',
   'learning_rate': 0.07894947368421053,
   'max_bin': 30,
   'max_depth': 6,
   'min_child_samples': 47,
   'min_child_weight': 0,
   'min_split_gain': 0.2631578947368421,
   'n_estimators': 400,
   'n_jobs': 1,
   'num_leaves': 14,
   'objective': None,
   'random_state': None,
   'reg_alpha': 0.5789473684210527,
   'reg_lambda': 0.7894736842105263,
   'silent': True,
   'subsample': 1,
   'subsample_for_bin': 200000,
   'subsample_freq': 0,
   'verbose': -10}
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

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о том, [как и где можно развернуть модель](how-to-deploy-and-where.md).

Дополнительные сведения о [как обучить модель регрессии с помощью машинного обучения автоматическая](tutorial-auto-train-models.md) или [Практическое обучение с помощью автоматической машинного обучения на удаленном ресурсе](how-to-auto-train-remote.md).
