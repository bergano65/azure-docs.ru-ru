---
title: Определение задачи машинного обучения для автоматического запуска машинного обучения
titleSuffix: Azure Machine Learning
description: Описание процедуры создания задачи машинного обучения для автоматического запуска машинного обучения.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: cb2fbcda7f30db24b876a66df22071f14df74814
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653253"
---
# <a name="how-to-define-a-machine-learning-task"></a>Определение задачи машинного обучения 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете о поддерживаемых задачах машинного обучения и о том, как определить их для автоматического запуска эксперимента машинного обучения.


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a> Что такое задача машинного обучения?

Задача машинного обучения представляет тип проблемы, решаемой созданием прогнозной модели. Автоматическое машинное обучение поддерживает три различных типа задач, включая классификацию, регрессию и прогнозирование временных рядов.

Тип задачи| Описание| Пример
----|----|----
Классификация | Задача для прогнозирования категории определенной строки в наборе данных. | Обнаружение мошенничества на кредитной карте. Целевой столбец будет отмечен как **обнаружено мошенничество** с категориями *true* или *false*. В этом случае мы будем классифицировать каждую строку данных как true или false.
Регрессия | Задача для прогнозирования непрерывного количества выходных данных. | Стоимость автомобиля зависит от его характеристик; целевым столбцом будет **Цена**.
Прогнозирование |Задача для выработки информированных оценок при определении будущих тенденций.| Прогнозирование спроса на энергию на следующие 48 часов. Целевым столбцом будет **Спрос**, и прогнозируемые значения будут использоваться для отображения закономерностей в спросе на энергию.

Оно также поддерживает приведенные ниже алгоритмы во время автоматизации и настройки. Пользователю не нужно указывать алгоритм.

Классификация | Регрессия | Прогнозирование временных рядов
-- |-- |--
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
[Классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Регрессор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Регрессор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Линейный классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Линейный регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Линейный регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Определение типа задачи
Вы можете задать тип задачи для экспериментов автоматизированного машинного обучения с помощью пакета SDK или студии Машинного обучения Azure.

Чтобы указать тип эксперимента, используйте параметр `task` в конструкторе `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Вы можете задать задачу как часть создания прогона эксперимента автоматизированного машинного обучения в студии Машинного обучения Azure. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Выбор типа задачи](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Дальнейшие действия

+ Дополнительные сведения об [автоматизированном машинном обучении](concept-automated-ml.md) в Машинном обучении Azure.
+ Дополнительные сведения об [автоматизированном обучении модели прогнозирования временных рядов](how-to-auto-train-forecast.md) в Машинном обучении Azure.
+ Воспользуйтесь руководством [Классификация автоматизированного машинного обучения](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).
+ Воспользуйтесь сборником примеров [Регрессия в автоматизированном машинном обучении](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb).

