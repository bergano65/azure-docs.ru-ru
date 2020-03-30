---
title: Понимание автоматизированных результатов ML
titleSuffix: Azure Machine Learning
description: Узнайте, как просматривать и понимать диаграммы и метрики для каждого из ваших автоматизированных запусков машинного обучения.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283463"
---
# <a name="understand-automated-machine-learning-results"></a>Общие сведения о результатах автоматизированного машинного обучения
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как просматривать и понимать диаграммы и метрики для каждого из ваших автоматизированных запусков машинного обучения. 

См. также:
+ [Метрики, диаграммы и кривые для классификационных моделей](#classification)
+ [Метрики, диаграммы и графики для регрессионных моделей](#regression)
+ [Интерпретация модели и важность функций](#explain-model)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

* Создайте эксперимент для автоматического запуска машинного обучения, либо с SDK, либо в студии машинного обучения Azure.

    * Используйте SDK для построения [модели классификации](how-to-auto-train-remote.md) или [регрессии](tutorial-auto-train-models.md)
    * Используйте [студию машинного обучения Azure](how-to-use-automated-ml-for-ml-models.md) для создания модели классификации или регрессии, загрузив соответствующие данные.

## <a name="view-the-run"></a>Просмотр запуска

После запуска автоматизированного эксперимента машинного обучения, история запусков можно найти в вашем рабочем пространстве машинного обучения. 

1. Перейдите в рабочую область.

1. В левой панели рабочего пространства выберите **Эксперименты.**

   ![Снимок экрана с меню эксперимента](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. В списке экспериментов выберите тот, который вы хотите исследовать.

   [![Список экспериментов](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. В нижней таблице выберите **Run**.

   Эксперимент запустить ) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. В моделях выберите **имя Алгоритм** для модели, которую вы хотите исследовать дальше.

   [![Модель эксперимента](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Вы также видите эти же результаты `RunDetails`во время запуска, когда вы используете [виджет Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification-results"></a><a name="classification"></a>Результаты классификации

Следующие метрики и диаграммы доступны для каждой модели классификации, которую вы создаете с помощью автоматизированных возможностей машинного обучения Azure Machine Learning

+ [Метрики](#classification-metrics)
+ [Матрица неточностей](#confusion-matrix)
+ [Диаграмма точного отзыва](#precision-recall-chart)
+ [ROC-кривые](#roc);
+ [кривая точности прогнозов](#lift-curve);
+ [кривая прироста](#gains-curve);
+ [график калибровки](#calibration-plot).

### <a name="classification-metrics"></a>Метрики классификации

Следующие метрики сохраняются в каждой итерации запуска для задачи классификации.

Метрика|Описание|Вычисление|Дополнительные параметры
--|--|--|--
AUC_Macro| AUC представляет собой область под кривой рабочих характеристик приемника (ROC). Макрозначение — это среднее арифметическое значение AUC для каждого класса.  | [Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC представляет собой область под кривой рабочих характеристик приемника (ROC). Micro вычисляется по всему миру путем объединения истинных срабатываний и ложных срабатываний от каждого класса.| [Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC представляет собой область под кривой рабочих характеристик приемника (ROC). Взвешенный является арифметическим средним значением оценки для каждого класса, взвешенным по количеству истинных экземпляров в каждом классе.| [Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Точность — это процент прогнозируемых меток, которые точно соответствуют истинным меткам. |[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Для вычисления средней точности используется кривая точности и полноты в качестве взвешенного среднего значения точности, полученного для каждого порогового значения, с увеличением полноты за счет предыдущего порогового значения, используемого в качестве весового коэффициента. Макро является арифметическим средним значением точности каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Для вычисления средней точности используется кривая точности и полноты в качестве взвешенного среднего значения точности, полученного для каждого порогового значения, с увеличением полноты за счет предыдущего порогового значения, используемого в качестве весового коэффициента. Micro вычисляется по всему миру путем объединения истинных срабатываний и ложных срабатываний при каждом отсечении.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Для вычисления средней точности используется кривая точности и полноты в качестве взвешенного среднего значения точности, полученного для каждого порогового значения, с увеличением полноты за счет предыдущего порогового значения, используемого в качестве весового коэффициента. Взвешенный является арифметическим средним значением точности для каждого класса, взвешенным по количеству истинных экземпляров в каждом классе.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Сбалансированная точность — это среднее арифметическое значение полноты для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|Оценка F1 — это среднее гармоническое значение точности и полноты. Макро является арифметическим средним баллом F1 для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|Оценка F1 — это среднее гармоническое значение точности и полноты. Micro вычисляется по всему миру путем подсчета общего количества истинных срабатываний, ложных негативов и ложных срабатываний.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|Оценка F1 — это среднее гармоническое значение точности и полноты. Взвешенное среднее значение по частоте класса оценки F1 для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Это функция потери, используемая в (многономиальной) логистической регрессии и расширениях, таких как нейронные сети, определяемые как отрицательная вероятность входа истинных меток, учитывая предсказания вероятностного классификатора. Для одного образца с истинной этикеткой yt и {0,1} предполагаемой вероятности yp, что yt No 1, потеря журнала -журнал P (yt&#124;yp) --(yt журнал (yp) q (1 - yt) журнал (1 - yp)).|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Нормализованная полнота макрозначений — это полнота макрозначений, нормализованная таким образом, чтобы случайному выполнению соответствовала оценка 0, а идеальному выполнению — оценка 1. Это достигается norm_macro_recall : ( recall_score_macro - R)/(1 - R), где R является ожидаемым значением recall_score_macro для случайных прогнозов (т.е. R'0.5 для двоичной классификации и R '(1/C) для проблем классификации C-класса).|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|средний - "макро" |
precision_score_macro|Точность — это процент положительно прогнозируемых элементов, которые правильно обозначены. Макро является арифметическим средним показателем точности для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Точность — это процент положительно прогнозируемых элементов, которые правильно обозначены. Micro вычисляется во всем мире путем подсчета общего количества истинных срабатываний и ложных срабатываний.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Точность — это процент положительно прогнозируемых элементов, которые правильно обозначены. Взвешенный является арифметическим средним значением точности для каждого класса, взвешенным по количеству истинных экземпляров в каждом классе.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Напомним, процент правильно обозначенных элементов определенного класса. Макро является арифметическим средним изветрием для каждого класса.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Напомним, процент правильно обозначенных элементов определенного класса. Micro вычисляется во всем мире путем подсчета общего истинного срабатывания, ложных негативов и ложных срабатываний|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Напомним, процент правильно обозначенных элементов определенного класса. Взвешенный является арифметическим средним значением отзыва для каждого класса, взвешенным по количеству истинных экземпляров в каждом классе.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Взвешенная точность — это точность, когда вес каждого примера равен пропорции истинных экземпляров в истинном классе этого примера.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|Значение sample_weight представляет собой вектор, равный доле этого класса для каждого элемента в целевом элементе.|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Матрица неточностей

#### <a name="what-is-a-confusion-matrix"></a>Что такое матрица путаницы?
Матрица неточностей используется для описания эффективности модели классификации. Каждая строка отображает экземпляры истинного или фактического класса в наборе данных, и каждый столбец представляет экземпляры класса, предсказанные моделью. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Что делает автоматизированный ML делать с матрицы путаницы?
Для решения проблем с классификацией Машинное обучение Azure автоматически предоставляет матрицу неточностей для каждой созданной модели. Для каждой матрицы путаницы автоматизированная ML будет отображать частоту каждой предсказанной метки (столбец) по сравнению с истинной меткой (строкой). Чем темнее цвет, тем выше количество в этой конкретной части матрицы. 

#### <a name="what-does-a-good-model-look-like"></a>Как выглядит хорошая модель?
Мы сравниваем фактическое значение набора данных с прогнозируемыми значениями, которые дала модель. Из-за этого модели машинного обучения имеют более высокую точность, если модель имеет большую часть своих значений по диагонали, то есть модель предсказала правильное значение. Если у модели есть классовый дисбаланс, матрица путаницы поможет обнаружить предвзятую модель.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Пример 1: Модель классификации с низкой точностью
![Модель классификации с низкой точностью](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Пример 2: Модель классификации с высокой точностью 
![Модель классификации с высокой точностью](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Пример 3: Модель классификации с высокой точностью и высокой предвзятостью в прогнозах моделей
![Модель классификации с высокой точностью и высокой предвзятостью в прогнозах моделей](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Диаграмма соотношения полноты и точности
#### <a name="what-is-a-precision-recall-chart"></a>Что такое диаграмма точного отзыва?
Кривая точного отзыва показывает связь между точностью и отзывом модели. Термин точность представляет собой способность модели правильно маркировать все экземпляры. Термин "полнота" обозначает способность классификатора найти все экземпляры для определенной метки.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Что делает автоматизированный ML с диаграммой точного отзыва?

С помощью этой диаграммы вы можете сравнить кривые полноты и точности для каждой модели, чтобы определить модель с допустимым соотношением этих параметров для поставленной бизнес-задачи. На этой схеме отображается среднее соотношение полноты и точности на макро-уровне, микро-уровне, а также для всех классов модели. 

Макро-средний будет вычислять метрику независимо от каждого класса, а затем взять средний, рассматривая все классы одинаково. Тем не менее, микро-средний будет агрегировать вклады всех классов для расчета среднего. Микро-средний предпочтительнее, если в наборе данных присутствует классовый дисбаланс.

#### <a name="what-does-a-good-model-look-like"></a>Как выглядит хорошая модель?
В зависимости от цели бизнес-задачи может отличаться идеальная кривая точного отзыва. Приведены ниже приведены некоторые примеры

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Пример 1: модель классификации с низкой точностью и низкой отзывом
![Модель классификации с низкой точностью и низкой отозванием](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Пример 2: Модель классификации с точностью 100% и отзывом 100% 
![Классификационная модель высокой точности и отзыва](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Диаграмма РПЦ

#### <a name="what-is-a-roc-chart"></a>Что такое диаграмма ROC?
Кривая ROC отображает правильно и неправильно классифицированные метки для конкретной модели. Кривая ROC может быть недостаточно информативной, если обучение моделей выполняется по наборам данных с высоким уровнем смещения, так как она не отображает ложноположительные метки.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Что делает автоматизированный ML с диаграммой ROC?
Автоматизированный ML генерирует Macro Average Precision-Recall, Micro Average Precision-Recall и точность отзыва, связанную со всеми классами для модели. 

Макро-средний будет вычислять метрику независимо от каждого класса, а затем взять средний, рассматривая все классы одинаково. Тем не менее, микро-средний будет агрегировать вклады всех классов для расчета среднего. Микро-средний предпочтительнее, если в наборе данных присутствует классовый дисбаланс.

#### <a name="what-does-a-good-model-look-like"></a>Как выглядит хорошая модель?
В идеале, модель будет иметь ближе к 100% истинной положительной ставке и ближе к 0% ложноположительной ставке. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Пример 1: Модель классификации с низкими истинными метками и высокими ложными метками
![Модель классификации с низкими истинными метками и высокими ложными метками](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Пример 2: Модель классификации с высокими истинными метками и низкими ложными метками
![модель классификации с высокими истинными метками и низкими ложными метками](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Диаграмма точности прогнозов
#### <a name="what-is-a-lift-chart"></a>Что такое график подъема?
Диаграммы точности прогнозов используются для оценки эффективности модели классификации. Он показывает, насколько лучше вы можете ожидать, чтобы сделать с генерируемой модели по сравнению с без модели с точки зрения точности.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Что делает автоматизированный ML с графиком подъема?
Вы можете сравнить показатели модели, автоматически созданной Машинным обучением Azure, с базовыми показателями, чтобы оценить прирост значений для конкретной модели.
#### <a name="what-does-a-good-model-look-like"></a>Как выглядит хорошая модель?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Пример 1: модель классификации, которая хуже, чем модель случайного выбора
![Модель классификации, которая хуже, чем модель случайного выбора](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Пример 2: Модель классификации, которая работает лучше, чем модель случайного выбора
![Модель классификации, которая работает лучше](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Диаграмма прибыли
#### <a name="what-is-a-gains-chart"></a>Что такое диаграмма прибыли?

Кривая прироста оценивает эффективность модели классификации по каждому сегменту данных. Она демонстрирует отдельно для каждого процентиля из набора данных, насколько ожидаемые результаты лучше, чем у модели случайного выбора.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Что делает автоматизированный ML делать с диаграммой прибыли?
Использование диаграммы суммарного прироста позволяет выбрать параметры отсечения для классификации по процентному уровню, который соответствующий требуемому приросту для модели. Здесь представлена та же информация, что и на диаграмме точности прогнозов, но в другом режиме отображения.

#### <a name="what-does-a-good-model-look-like"></a>Как выглядит хорошая модель?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Пример 1: Модель классификации с минимальным приростом
![модель классификации с минимальным приростом](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Пример 2: Модель классификации со значительным приростом
![Модель классификации со значительным приростом](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Диаграмма калибровки

#### <a name="what-is-a-calibration-chart"></a>Что такое калибровка диаграммы?
График калибровки отображает достоверность прогнозной модели. Он делает это, показывая связь между прогнозируемой вероятностью и фактической вероятностью, где "вероятность" представляет вероятность того, что конкретный экземпляр принадлежит под какой-либо меткой.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Что делает автоматизированный ML с калибровочной диаграммой?
Для всех задач классификации вы можете просмотреть строку калибровки с макро-усреднением, микро-усреднением и для каждого класса в выбранной прогнозной модели.

Макро-средний будет вычислять метрику независимо от каждого класса, а затем взять средний, рассматривая все классы одинаково. Тем не менее, микро-средний будет агрегировать вклады всех классов для расчета среднего. 
#### <a name="what-does-a-good-model-look-like"></a>Как выглядит хорошая модель?
 Хорошо откалиброванная модель выравнивается с линией y'x, где она достаточно уверена в своих прогнозах. Модель с высокой степенью достоверности приближается к графику y=0, где присутствует прогнозируемая вероятность, но отсутствует фактическая. 


##### <a name="example-1-a-well-calibrated-model"></a>Пример 1: Хорошо откалиброванная модель
![ более хорошо откалиброванные модели](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Пример 2: Чрезмерно уверенная модель
![Сверхуверенная модель](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Результаты регрессии

Следующие метрики и диаграммы доступны для каждой модели регрессии, которую вы создаете с помощью автоматизированных возможностей машинного обучения Azure Machine Learning

+ [Метрики](#reg-metrics)
+ [Прогнозируемые против Правда](#pvt)
+ [Гистограмма остатков](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Показатели регрессии

Следующие метрики сохраняются в каждой итерации запуска для задачи регрессии или прогнозирования.

|Метрика|Описание|Вычисление|Дополнительные параметры
--|--|--|--|
explained_variance|Объяснимая дисперсия — это доля, учитываемая математической моделью при вычислении дисперсии заданного набора данных. Это процент уменьшения дисперсии исходных данных по отношению к дисперсии ошибок. Если среднее значение ошибок равно 0, оно равно объяснимой дисперсии.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score;|R2 — это коэффициент определения или процентное снижение квадратичных ошибок по сравнению с базовой моделью, которая выводит среднее значение. |[Вычисление](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation;|Корреляция Спирмена — это непараметрическая мера монотонности связи между двумя наборами данных. В отличие от корреляции Пирсона, для корреляции Спирмена не предполагается, что оба набора данных используют нормальное распределение. Как и другие коэффициенты корреляции, этот коэффициент принимает значения от –1 до + 1. Значение 0 означает отсутствие корреляции. Значения корреляции –1 и + 1 означают точную монотонную связь. Положительные значения корреляции означают, что при увеличении значения x также увеличивается значение y. Отрицательные значения корреляции означают, что при увеличении значения x значение y уменьшается.|[Вычисление](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Средняя абсолютная погрешность — это оценочная величина абсолютного значения отклонения между целевым и прогнозируемым значениями.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error;|Нормализованная средняя абсолютная погрешность равна средней абсолютной погрешности, деленной на диапазон данных.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Деление на диапазон данных|
median_absolute_error|Медиана абсолютной погрешности — это медиана всех абсолютных отклонений между целевым и прогнозируемым значениями. Такая потеря устойчива к выбросам.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Нормализованная медиана абсолютной погрешности равна медиане абсолютной погрешности, деленной на диапазон данных.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Деление на диапазон данных|
root_mean_squared_error|Среднеквадратическая погрешность — это среднеквадратическое значение ожидаемого квадратичного отклонения между целевым и прогнозируемыми значениями.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error;|Нормализованная среднеквадратическая погрешность равна среднеквадратической погрешности, деленной на диапазон данных.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Деление на диапазон данных|
root_mean_squared_log_error|Среднеквадратическая логарифмическая погрешность — это среднеквадратическое значение ожидаемой квадратичной логарифмической погрешности.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error;|Нормализованная среднеквадратическая логарифмическая погрешность равна среднеквадратической логарифмической погрешности, деленной на диапазон данных.|[Вычисление](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Деление на диапазон данных|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Прогнозируемый и true диаграмма
#### <a name="what-is-a-predicted-vs-true-chart"></a>Что такое диаграмма «Предсказано против истины»?
Предсказанное против True показывает связь между предсказанным значением и его коррелятивным истинным значением для проблемы регрессии. Эта диаграмма позволяет оценить эффективность модели: чем ближе прогнозируемые значения к графику y=x, тем выше точность прогнозной модели.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Что делает автоматизированный ML делать с предсказанием против True диаграммы?
После каждого запуска для каждой модели регрессии отображается диаграмма прогнозируемых и истинных значений. Для обеспечения конфиденциальности данных значения группируются по ячейкам, размер каждой из которых отображается в виде линейчатой диаграммы в нижней части области диаграммы. Вы можете сравнить результаты прогнозной модели с более светлой областью, которая отображает идеальные значения, требуемые для этой модели, с учетом допусков.

#### <a name="what-does-a-good-model-look-like"></a>Как выглядит хорошая модель?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Пример 1: Модель классификации с низкой точностью
![Регрессионная модель с низкой точностью в прогнозах](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Пример 2: регрессионная модель с высокой точностью 
[![Регрессионная модель с высокой точностью в своих прогнозах](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Гистограмма диаграммы остатков
#### <a name="what-is-a-residuals-chart"></a>Что такое диаграмма остатков?
Гистограмма остатков отображает разность между наблюдаемыми и прогнозируемыми значениями y. Чтобы отобразить допуск ошибок с низким смещением, гистограмма остатков должна иметь форму колокола с центром около 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Что делает автоматизированный ML с диаграммой остатков?
Автоматизированный ML автоматически предоставляет диаграмму остатков, чтобы показать распределение ошибок в прогнозах.
#### <a name="what-does-a-good-model-look-like"></a>Как выглядит хорошая модель?
Хорошая модель, как правило, имеет кривую колокола или ошибки около нуля.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Пример 1: регрессионная модель с уклоном в своих ошибках
![Модель регрессии SA с предвзятостью в своих ошибках](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Пример 2: регрессионная модель с более равномерное распределение ошибок
![Регрессионная модель с более равномерное распределение ошибок](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Интерпретация модели и важность функций
Автоматизированный ML обеспечивает панель мониторинга интерпретации машинного обучения для ваших трасс.
Для получения дополнительной информации о [how-to](how-to-machine-learning-interpretability-automl.md) включении функций интерпретации, см.

## <a name="next-steps"></a>Дальнейшие действия

+ Узнайте больше об [автоматизированном мл](concept-automated-ml.md) в Azure Machine Learning.
+ Попробуйте [автоматизированное машинное обучение Модели Объяснение](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) образца ноутбуков.
