---
title: Определение задачи машинного обучения для автоматического запуска машинного обучения
titleSuffix: Azure Machine Learning
description: Узнайте, как определить задачу машинного обучения для автоматического запуска машинного обучения
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475501"
---
# <a name="how-to-define-a-machine-learning-task"></a>Как определить задачу машинного обучения 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете поддерживаемые задачи машинного обучения и как определить их для запуска эксперимента автоматизированного машинного обучения (автоматизированного ML).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Что такое задача машинного обучения?

Задача машинного обучения представляет тип проблемы, решаемый путем создания прогностической модели. Автоматизированный ML поддерживает три различных типа задач, включая классификацию, регрессию и прогнозирование временных рядов.

Тип задачи| Описание| Пример
----|----|----
Классификация | Задача для прогнозирования категории определенной строки в наборе данных. | Обнаружение мошенничества на кредитной карте. Целевой столбец будет **Мошенничество Обнаружено** с категориями *True* или *False*. В этом случае мы классифицируя каждую строку в данных как истинную или ложную.
Регрессия | Задача для прогнозирования непрерывного объема вывода. | Стоимость автомобиля, основанная на его особенностях, целевой столбец будет **цена**.
Прогнозирование |Задача составления обоснованных оценок при определении направления будущих тенденций.| Прогнозирование спроса на энергию для них в ближайшие 48 часов. Целевая колонка будет **востребована,** а прогнозируемые значения будут использоваться для отображать закономерности спроса на энергию.

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
[Классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Линейный классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Линейный регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Линейный регрессор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Установка типа задачи
Вы можете настроить тип задачи для автоматизированных экспериментов ML с SDK или студией машинного обучения Azure.

Используйте `task` параметр `AutoMLConfig` в конструкторе, чтобы указать тип эксперимента.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Вы можете поставить задачу в рамках автоматического эксперимента ML, созданного в студии машинного обучения Azure. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Выбор типа задачи](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Следующие шаги

+ Узнайте больше об [автоматизированном мл](concept-automated-ml.md) в Azure Machine Learning.
+ Подробнее об [автоматическом обучении модели прогнозирования временных рядов](how-to-auto-train-forecast.md) читайте в разделе Azure Machine Learning
+ Попробуйте учебник по [классификации автоматизированных машин.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)
+ Попробуйте [автоматизированный машинное обучение Регрессии](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) образец ноутбука.

