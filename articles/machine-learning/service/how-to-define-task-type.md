---
title: Определение задачи машинного обучения для автоматического запуска машинного обучения
titleSuffix: Azure Machine Learning
description: Узнайте, как определить задачу машинного обучения для автоматического запуска машинного обучения.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: c34a8e999267a77fa4aabbd9dc3a73b2c4f5a0ef
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647210"
---
# <a name="how-to-define-a-machine-learning-task"></a>Определение задачи машинного обучения 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете о поддерживаемых задачах машинного обучения и о том, как определить их для автоматического запуска эксперимента машинного обучения.


## <a name="What is a machine learning task?"></a>Что такое задача машинного обучения?

Задача машинного обучения представляет тип решаемой проблемы, создавая прогнозную модель. Автоматический ML поддерживает три различных типа задач, включая классификацию, регрессию и прогнозирование временных рядов.

Тип задачи| Description (Описание)| Пример
----|----|----
классификация; | Задача для прогнозирования категории определенной строки в наборе данных. | Обнаружение мошенничества на кредитной карте. Целевой столбец будет выглядеть как **мошенничество** с категориями *true* или *false*. В этом случае мы будем классифицировать каждую строку данных как true или false.
регрессия; | Задача для прогнозирования выходных данных непрерывного количества. | Стоимость автомобилей зависит от ее характеристик. целевой столбец будет иметь значение **Price**.
Прогнозирование |Задача для принятия информированных оценок при определении направления будущих тенденций.| Прогнозирование спроса на энергию в течение следующих 48 часов. Целевой столбец будет **требоваться** , а прогнозируемые значения будут использоваться для отображения шаблонов в спросе на энергию.

Оно также поддерживает приведенные ниже алгоритмы во время автоматизации и настройки. Пользователю не нужно указывать алгоритм.

классификация; | регрессия; | Прогнозирование временных рядов
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
[Классификатор DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNNная регрессия](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNNная регрессия](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNNй Линейный классификатор](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Линейная регрессия](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Линейная регрессия](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Упрощенный алгоритм Байеса](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Стохастический градиентный спуск (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Задание типа задачи
Вы можете задать тип задачи для автоматических экспериментов ML с помощью пакета SDK или Машинное обучение Azure Studio.

Используйте параметр `task` в конструкторе `AutoMLConfig`, чтобы указать тип эксперимента.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Вы можете задать задачу как часть созданного автоматического запуска эксперимента ML в Машинное обучение Azure Studio. 

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Выбор типа задачи](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Дальнейшие действия

+ Дополнительные сведения об [автоматизированном ML](concept-automated-ml.md) в машинное обучение Azure.
+ Дополнительные сведения об [автоматическом обучении модели прогнозирования временных рядов](how-to-auto-train-forecast.md) в машинное обучение Azure
+ Воспользуйтесь руководством по [автоматизированной классификации машинное обучение](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
+ Попробуйте пример автоматизированной записной книжки [машинное обучение регрессии](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .

