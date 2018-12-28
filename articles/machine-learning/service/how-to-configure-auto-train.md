---
title: Создание эксперимента автоматического машинного обучения
titleSuffix: Azure Machine Learning service
description: Автоматическое машинное обучение выбирает алгоритм и создает модель, готовую для развертывания. Узнайте о параметрах, с помощью которых можно настроить эксперименты автоматического машинного обучения.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3dedf5de1ac2c88a9a00fd5f62e0663b840c0fd9
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438529"
---
# <a name="configure-automated-machine-learning-experiments"></a>Настройка экспериментов автоматического машинного обучения

Автоматическое машинное обучение выбирает алгоритм и гиперпараметры, а также создает модель, готовую для развертывания. Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения. В этом руководстве вы узнаете, как определить различные параметры конфигурации.

Примеры экспериментов автоматического машинного обучения см. в разделе [Руководство. Создание регрессионной модели с помощью автоматического машинного обучения](tutorial-auto-train-models.md) или [Обучение моделей с помощью автоматического машинного обучения в облаке](how-to-auto-train-remote.md).

Возможности настройки, доступные в автоматическом машинном обучении:

* выбор типа эксперимента: классификация, регрессия или прогнозирование;
* источник данных, форматы и получение данных;
* выбор целевого объекта вычислений (локальный или удаленный);
* настройка параметров эксперимента автоматического машинного обучения;
* выполнение эксперимента автоматического машинного обучения;
* Изучение метрик модели
* регистрация и развертывание модели.

## <a name="select-your-experiment-type"></a>Выбор типа эксперимента
Прежде чем начать эксперимент, следует определить тип задачи машинного обучения, которую необходимо решить. Автоматическое машинное обучение поддерживает задачи классификации, регрессии и прогнозирования. 

Хотя выпущена общедоступная версия службы автоматического машинного обучения, **прогнозирование все еще находится на этапе общедоступной предварительной версии**.

Эта служба также поддерживает приведенные ниже алгоритмы для автоматизации и настройки. Пользователю не нужно указывать алгоритм.

классификация; | Регрессия | Прогнозирование
|-- |-- |--
[Логистическая регрессия](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Эластичная сеть](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Эластичная сеть](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Классификация опорных C-векторов (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#regression)|[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#regression)
[Линейная классификация опорных векторов](https://scikit-learn.org/stable/modules/svm.html#classification)|[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Алгоритм "К ближайших соседей"](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[Лассо LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Лассо LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Дерево принятия решений](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Случайный лес](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Крайне случайные деревья](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Градиентное усиление](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Упрощенный алгоритм GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


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

Ключ | type |    Взаимно исключают друг друга с помощью | ОПИСАНИЕ
---|---|---|---
X | Кадр данных Pandas или массив Numpy | data_train, метка, столбцы |  Все признаки для обучения
y | Кадр данных Pandas или массив Numpy |   label   | Данные метки для обучения. Для классификации это должен быть массив целых чисел
X_valid | Кадр данных Pandas или массив Numpy   | data_train, метка | _Необязательно._ Все признаки для проверки. Если не указано, X разделяется на экземпляры для обучения и проверки
y_valid |   Кадр данных Pandas или массив Numpy | data_train, метка | _Необязательно._ Данные метки для проверки. Если не указано, y разделяется на экземпляры для обучения и проверки
sample_weight | Кадр данных Pandas или массив Numpy |   data_train, метка, столбцы| _Необязательно._ Значение веса для каждой выборки. Используйте, если необходимо назначить разный вес для точек данных 
sample_weight_valid | Кадр данных Pandas или массив Numpy | data_train, метка, столбцы |    _Необязательно._ Значение веса для каждой выборки для проверки. Если не указано, sample_weight разделяется на экземпляры для обучения и проверки
data_train |    Кадр данных Pandas |  X, y, X_valid, y_valid |    Все данные (признаки и метка) для обучения
label | строка  | X, y, X_valid, y_valid |  Какой столбец в data_train представляет метку
columns | Массив строк  ||  _Необязательно._ Список разрешений столбцов для использования с признаками
cv_splits_indices   | массив целых чисел ||  _Необязательно._ Список индексов для разделения данных для перекрестной проверки

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Загрузка и подготовка данных с помощью пакета SDK для подготовки данных
Эксперименты автоматического машинного обучения поддерживают загрузку и преобразование данных с помощью пакета SDK для подготовки данных. Этот пакет SDK предоставляет следующие возможности:

>* загрузка из файлов многих типов с автоматическим обнаружением параметров для синтаксического анализа (кодировка, разделитель, заголовки);
>* преобразование типов на основе зависимостей во время загрузки файла;
>* поддержка подключений к MS SQL Server и хранилищу Azure Data Lake.
>* Добавление столбца с помощью выражения
>* Добавление отсутствующих значений
>* Получение столбцов по образцу
>* Фильтрация
>* Пользовательские преобразования Python

Дополнительные сведения о пакете SDK для подготовки данных см. в статье [Как подготовить данные для моделирования](how-to-load-data.md). Ниже приведен пример загрузки данных с помощью пакета SDK для подготовки данных. 
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

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Перекрестная проверка Монте-Карло (так называемая повторная случайная вложенная выборка)

С помощью `validation_size` укажите процент набора данных для обучения, который должен использоваться для проверки, а с помощью `n_cross_validations` укажите число перекрестных проверок. Во время каждого цикла перекрестной проверки подмножество размера `validation_size` будет выбрано случайным образом для проверки модели, обученной с использованием оставшихся данных. Наконец, будет отправлен отчет о средних показателях по всем циклам `n_cross_validations`, и соответствующая модель будет переобучена на целом наборе данных для обучения.

### <a name="custom-validation-dataset"></a>Настраиваемый проверочный набор данных

Используйте настраиваемый проверочный набор данных, если случайное разделение недопустимо (как правило, данные временных рядов или несбалансированные данные). Можно указать собственный проверочный набор данных. Модель будет анализироваться с использованием проверочного набора данных, указанного вместо случайного набора данных.

## <a name="compute-to-run-experiment"></a>Объект вычислений для выполнения эксперимента

Затем следует определить, где модель будет обучаться. Эксперимент автоматического машинного обучения можно запустить в следующих вычислительных средах.
*   Локальный компьютер или ноутбук. Как правило, при наличии небольшого набора данных и если вы по-прежнему находитесь на этапе изучения.
*   Удаленный компьютер в облаке. [Управляемая Вычислительная среда Машинного обучения Azure](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) — это управляемая служба, которая позволяет обучать модели машинного обучения в кластерах виртуальных машин Azure.

На [сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl) доступны примеры записных книжек с локальными и удаленными целевыми объектами вычислений.

<a name='configure-experiment'/>

## <a name="configure-your-experiment-settings"></a>Настройка параметров эксперимента

Доступно несколько параметров, с помощью которых можно настроить эксперименты автоматического машинного обучения. Эти параметры задаются путем создания экземпляра объекта `AutoMLConfig`.

Некоторые примеры:

1.  Эксперимент классификации со значением AUC_weighted в качестве основной метрики с наибольшим максимальным временем в 12 000 секунд на итерацию и окончанием эксперимента после 50 итераций и 2 свертков перекрестной проверки.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Ниже приведен пример эксперимента регрессии, который заканчивается после 100 итераций, каждая из которых длится до 600 секунд и в течение которых выполняется 5 свертков перекрестной проверки.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

В этой таблице приведены параметры, доступные для вашего эксперимента, и их значения по умолчанию.

Свойство |  ОПИСАНИЕ | По умолчанию
--|--|--
`task`  |Укажите тип задачи машинного обучения. Допустимые значения: <li>классификация;</li><li>Регрессия</li><li>Прогнозирование</li>    | Нет |
`primary_metric` |Метрика, которую требуется оптимизировать при компиляции модели. Например, если задать в качестве свойства primary_metric значение accuracy, служба автоматического машинного обучения выполнит поиск модели с максимальной точностью. Указать свойство primary_metric для эксперимента можно только один раз. Допустимые значения: <br/>**Классификация**:<br/><li> accuracy;  </li><li> AUC_weighted;</li><li> precision_score_weighted; </li><li> balanced_accuracy; </li><li> average_precision_score_weighted. </li><br/>**Регрессия:** <br/><li> normalized_mean_absolute_error; </li><li> spearman_correlation; </li><li> normalized_root_mean_squared_error; </li><li> normalized_root_mean_squared_log_error;</li><li> R2_score.  </li> | Для классификации: accuracy <br/>Для регрессии: spearman_correlation <br/> |
`experiment_exit_score` |   Для свойства primary_metric можно задать целевое значение. После обнаружения модели, которая отвечает целевой метрике primary_metric, служба автоматического машинного обучения прекращает итерацию и эксперимент завершается. Если это значение не задано (по умолчанию), эксперимент автоматического машинного обучения продолжит выполнять указанное количество итераций. Принимает значение типа double. Если целевой метрики невозможно достичь, служба автоматического машинного обучения будет выполнять итерации, пока не будет достигнуто указанное количество итераций.| Нет
`iterations` |Максимальное количество итераций. Каждая итерация равняется заданию обучения, которое предоставляет конвейер. Конвейер — это предварительная обработка данных и модель. Чтобы получить модель высокого качества, используйте 250 или более итераций.    | 100
`max_concurrent_iterations`|    Максимальное количество итераций, выполняемых параллельно. Этот параметр работает только для удаленных объектов вычислений.|   1
`max_cores_per_iteration`   | Указывает, сколько ядер в целевом объекте вычислений будет использоваться для обучения одного конвейера. Если для алгоритма может использоваться несколько ядер, это увеличивает производительность на компьютере с несколькими ядрами. Для него можно задать значение -1, чтобы использовать все доступные ядра на компьютере.|  1
`iteration_timeout_minutes` |   Ограничивает количество времени (в минутах), в течение которого длится определенная итерация. Если итерация превышает заданное количество, эта итерация отменяется. Если количество не задано, итерация продолжает выполняться до завершения. |   Нет
`n_cross_validations`   |Количество разделений перекрестной проверки.| Нет
`validation_size`   |Размер проверочного набора, выраженный в проценте от всего обучающего примера.|  Нет
`preprocess` | True или False <br/>Если задано значение true, эксперимент может выполнять предварительную обработку входных данных. Ниже приведено подмножество предварительной обработки:<li>отсутствующие данные: замещает отсутствующие числовые данные средним значением, текстовые — наиболее часто встречающимися вхождениями; </li><li>категориальные значения: если тип данных числовой, а количество уникальных значений — менее 5 %, преобразует в прямое кодирование. </li><li>И другое. Полный список см. в [репозитории GitHub](https://aka.ms/aml-notebooks).</li><br/>Примечание. Если данные разрежены, для предварительной обработки нельзя задать значение true. |  Ложь | 
`blacklist_models`  | В эксперименте автоматического машинного обучения есть множество различных алгоритмов, которые он пытается выполнить. Исключите определенные алгоритмы из эксперимента автоматического машинного обучения. Это полезно, если известно, что какой-либо алгоритм не подходит для вашего набора данных. Исключение алгоритмов может сэкономить вычислительные ресурсы и время обучения.<br/>Допустимые значения для классификации:<br/><li>LogisticRegression;</li><li>SGD</li><li>MultinomialNaiveBayes;</li><li>BernoulliNaiveBayes;</li><li>SVM</li><li>LinearSVM;</li><li>kNN;</li><li>DecisionTree;</li><li>RandomForest;</li><li>ExtremeRandomTrees;</li><li>LightGBM</li><li>GradientBoosting;</li><li>TensorFlowDNN;</li><li>TensorFlowLinearClassifier.</li><br/>Допустимые значения для регрессии:<br/><li>ElasticNet;</li><li>GradientBoosting;</li><li>DecisionTree;</li><li>kNN;</li><li>LassoLars;</li><li>SGD </li><li>RandomForest;</li><li>ExtremeRandomTree;</li><li>LightGBM</li><li>TensorFlowLinearRegressor;</li><li>TensorFlowDNN.</li></li><br/>Допустимые значения для прогнозирования:<br/><li>ElasticNet;</li><li>GradientBoosting;</li><li>DecisionTree;</li><li>kNN;</li><li>LassoLars;</li><li>SGD </li><li>RandomForest;</li><li>ExtremeRandomTree;</li><li>LightGBM</li><li>TensorFlowLinearRegressor;</li><li>TensorFlowDNN.</li></li>|   Нет
`whitelist_models`  | В эксперименте автоматического машинного обучения есть множество различных алгоритмов, которые он пытается выполнить. Включите определенные алгоритмы в эксперимент автоматического машинного обучения. Это полезно, если известно, что какой-либо алгоритм подходит для вашего набора данных. <br/>Допустимые значения для классификации:<br/><li>LogisticRegression;</li><li>SGD</li><li>MultinomialNaiveBayes;</li><li>BernoulliNaiveBayes;</li><li>SVM</li><li>LinearSVM;</li><li>kNN;</li><li>DecisionTree;</li><li>RandomForest;</li><li>ExtremeRandomTrees;</li><li>LightGBM</li><li>GradientBoosting;</li><li>TensorFlowDNN.</li><li>TensorFlowLinearClassifier.</li><br/>Допустимые значения для регрессии:<br/><li>ElasticNet;</li><li>GradientBoosting;</li><li>DecisionTree;</li><li>kNN;</li><li>LassoLars;</li><li>SGD </li><li>RandomForest;</li><li>ExtremeRandomTree;</li><li>LightGBM</li><li>TensorFlowLinearRegressor;</li><li>TensorFlowDNN.</li></li><br/>Допустимые значения для прогнозирования:<br/><li>ElasticNet;</li><li>GradientBoosting;</li><li>DecisionTree;</li><li>kNN;</li><li>LassoLars;</li><li>SGD </li><li>RandomForest;</li><li>ExtremeRandomTree;</li><li>LightGBM</li><li>TensorFlowLinearRegressor;</li><li>TensorFlowDNN.</li></li>|  Нет
`verbosity` |Определяет уровень ведения журнала со значением INFO для наиболее подробного протоколирования и CRITICAL для наименее подробного протоколирования. Уровень детализации принимает значения, определенные в пакете Python для ведения журнала. Допустимые значения:<br/><li>logging.INFO</li><li>logging.WARNING;</li><li>logging.ERROR;</li><li>logging.CRITICAL;</li>  | logging.INFO</li> 
`X` | Все признаки для обучения |  Нет
`y` |   Данные метки для обучения. Для классификации это должен быть массив целых чисел|  Нет
`X_valid`|_Необязательно._ Все признаки для проверки. Если не указано, X разделяется на экземпляры для обучения и проверки |   Нет
`y_valid`   |_Необязательно._ Данные метки для проверки. Если не указано, y разделяется на экземпляры для обучения и проверки    | Нет
`sample_weight` |   _Необязательно._ Значение веса для каждой выборки. Используйте, если необходимо назначить разный вес для точек данных |   Нет
`sample_weight_valid`   |   _Необязательно._ Значение веса для каждой выборки для проверки. Если не указано, sample_weight разделяется на экземпляры для обучения и проверки   | Нет
`run_configuration` |   Объект RunConfiguration.  Используется для удаленных выполнений. |Нет
`data_script`  |    Путь к файлу, содержащему метод get_data.  Требуется для удаленных выполнений.   |Нет
`model_explainability` | _Необязательно._ True или False <br/>  Значение True позволяет эксперименту вычислять важность признаков для каждой итерации. Можно также использовать метод explain_model() в определенной итерации, чтобы иметь возможность вычислить важность признаков по запросу для этой итерации после завершения эксперимента. | Ложь
`enable_ensembling`|Флаг, позволяющий включить итерацию сборки после завершения всех итераций.| Истина 
`ensemble_iterations`|Число итераций, во время которых мы выбираем подходящий конвейер для добавления в окончательную совокупность.| 15
`experiment_timeout_minutes`| Ограничивает длительность всего эксперимента (в минутах). | Нет

## <a name="data-pre-processing-and-featurization"></a>Предварительная обработка данных и добавление признаков

Если вы используете `preprocess=True`, то приведенные ниже этапы предварительной обработки данных выполняются автоматически.
1.  Удаление признаков с высокой кратностью или отсутствие вариативности.
    * Удалите признаки, не несущие никакой полезной информации, из наборов для обучения и проверки. К ним относятся признаки без значений, с одинаковыми значениями во всех строках или с очень высокой кратностью (например, хэши, идентификаторы или глобальные уникальные идентификаторы).
1.  Заполнение отсутствующих значений.
    *   Для числовых признаков можно добавить отсутствующие значения на основании среднего значения в столбце.
    *   Для категориальных признаков можно добавить отсутствующие значения на основании наиболее частого значения.
1.  Создание дополнительных признаков.
    * Для признаков даты и времени: год, месяц, день, день недели, день года, квартал, неделя года, час, минута, секунда.
    * Для текстовых признаков: частота терминов на основании словарной униграммы, биграмм, триграмм грамм и количественного векторизатора.
1.  Преобразование и кодирование.
    * Числовые признаки с очень малым числом уникальных значений преобразовываются в категориальные признаки.
    * В зависимости от кратности категориальных признаков можно выполнить кодирование меток или (хэширование) прямое унитарное кодирование.

## <a name="run-experiment"></a>Выполнение эксперимента

Отправьте эксперимент для выполнения и создания модели. Передайте `AutoMLConfig` в метод `submit`, чтобы создать модель.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Сначала зависимости устанавливаются на новую виртуальную машину.  Это может занять до 10 минут, прежде чем отобразятся выходные данные.
>Если для параметра `show_output` задать значение `True`, выходные данные отобразятся в консоли.


## <a name="explore-model-metrics"></a>Изучение метрик модели
Просмотреть результаты можно в мини-приложении или во встроенном окне при работе с записной книжкой. Ознакомьтесь с разделом [Просмотр сведений о выполнении](how-to-track-experiments.md#view-run-details), чтобы получить дополнительные сведения.


### <a name="classification-metrics"></a>Метрики классификации
В каждой итерации для задачи классификации сохраняются следующие метрики.

|Основная метрика|ОПИСАНИЕ|Вычисление|Дополнительные параметры
--|--|--|--|--|
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
norm_macro_recall|Нормализованная полнота макрозначений — это полнота макрозначений, нормализованная таким образом, чтобы случайному выполнению соответствовала оценка 0, а идеальному выполнению — оценка 1. Для этого используется формула norm_macro_recall := (recall_score_macro – R)/(1 – R), где R — ожидаемое значение recall_score_macro для произвольных прогнозов (т. е. R = 0,5 для задач бинарной классификации и R = (1/C) задач C-классовой классификации).|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro", затем (recall_score_macro – R)/(1 – R), где R — ожидаемое значение recall_score_macro для произвольных прогнозов (т. е. R = 0,5 для задач бинарной классификации и R = (1/C) задач C-классовой классификации).|
precision_score_macro|Точность — это процент элементов, отнесенных к определенному классу, которые действительно находятся в этом классе. Макрозначение — это среднее арифметическое значение точности для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Точность — это процент элементов, отнесенных к определенному классу, которые действительно находятся в этом классе. Микрозначение вычисляется глобально путем подсчета общего числа истинно положительных, ложно отрицательных и истинно отрицательных результатов.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Точность — это процент элементов, отнесенных к определенному классу, которые действительно находятся в этом классе. Взвешенное значение — среднее арифметическое значение точности для каждого класса, взвешенное по числу экземпляров значения true в каждом классе.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Полнота — это процент элементов, которые фактически находятся в определенном классе и правильно помечены. Макрозначение — это среднее арифметическое значение полноты для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Полнота — это процент элементов, которые фактически находятся в определенном классе и правильно помечены. Микрозначение вычисляется глобально путем подсчета общего числа истинно положительных и ложно отрицательных результатов.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Полнота — это процент элементов, которые фактически находятся в определенном классе и правильно помечены. Взвешенное значение — среднее арифметическое значение полноты для каждого класса, взвешенное по числу экземпляров значения true в каждом классе.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Взвешенная точность — это точность, для вычисления которой каждой выборке присваивается вес, соответствующий доле экземпляров true в классе true для этой выборки.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|Значение sample_weight представляет собой вектор, равный доле этого класса для каждого элемента в целевом элементе.|

### <a name="regression-and-forecasting-metrics"></a>Метрики регрессии и прогнозирования
В каждой итерации для задачи регрессии или прогнозирования сохраняются следующие метрики.

|Основная метрика|ОПИСАНИЕ|Вычисление|Дополнительные параметры
--|--|--|--|--|
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

## <a name="explain-the-model"></a>Объяснение модели

Хотя выпущена общедоступная версия службы автоматического машинного обучения, **функции объясняемости моделей все еще находятся на этапе общедоступной предварительной версии**.

Автоматическое машинное обучение позволяет понять важность признаков.  В процессе обучения вы можете получить глобальные показатели важности признаков для модели.  Для сценариев классификации можно также получить показатели важности признаков на уровне класса.  Необходимо указать проверочный набора данных (X_valid), чтобы получить показатели важности признаков.

Существуют два способа создания этих значений.

*   После завершения эксперимента можно использовать метод `explain_model` для любой итерации.

    ```
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

    ```
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

    ```
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

Вы можете отобразить диаграмму важности признаков в рабочей области на портале Azure. Эта диаграмма отображается также при использовании мини-приложения Jupyter в записной книжке. Дополнительные сведения о диаграммах см. в статье с [примерами записных книжек Машинного обучения Azure](samples-notebooks.md).

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![Диаграмма важности признаков](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о том, [как и где можно развернуть модель](how-to-deploy-and-where.md).

Узнайте больше о том, [как обучить модель классификации с помощью автоматического машинного обучения](tutorial-auto-train-models.md) или [как обучить ее с использованием автоматического машинного обучения на удаленном ресурсе](how-to-auto-train-remote.md). 
