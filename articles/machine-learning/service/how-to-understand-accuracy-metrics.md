---
title: Метрики точности обучения в автоматизированном ML
titleSuffix: Azure Machine Learning service
description: Сведения об автоматических метриках точности машинного обучения для каждого из ваших запусков.
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: dc147fd0252b2b5ec4ce334d6c1c464d9cde8ef5
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297892"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>Оценка точности обучения в автоматизированном ML с помощью метрик

В этой статье вы узнаете о различных метриках, доступных для автоматизированных моделей машинного обучения, в Машинное обучение Azure. 

Существует несколько способов просмотра метрик точности обучения для каждой итерации выполнения.
* Использование [мини-приложения Jupyter](how-to-track-experiments.md#view-run-details)
* Использование функции для любого `Run` объекта [ `get_metrics()` ](how-to-track-experiments.md#query-run-metrics)
* Просмотр [метрик эксперимента в портал Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="prerequisites"></a>предварительные требования
 
* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).
 
* Создайте Автоматический эксперимент машинного обучения с помощью пакета SDK или портал Azure.
 
    * Использование пакета SDK для построения [модели классификации](how-to-auto-train-remote.md) или [модели регрессии](tutorial-auto-train-models.md)
    * Используйте [портал Azure](how-to-create-portal-experiments.md) для создания модели классификации или регрессии путем передачи соответствующих данных.

## <a name="classification-metrics"></a>Метрики классификации

Следующие метрики сохраняются в каждой итерации выполнения для задачи классификации.

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

## <a name="regression-and-forecasting-metrics"></a>Метрики регрессии и прогнозирования

Следующие метрики сохраняются в каждой итерации выполнения для задачи регрессии или прогнозирования.

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

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об [автоматизированном ML](concept-automated-ml.md) в машинное обучение Azure.