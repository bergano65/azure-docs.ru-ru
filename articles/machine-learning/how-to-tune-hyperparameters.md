---
title: Настройка гиперпараметров модели
titleSuffix: Azure Machine Learning
description: Эффективная настройка параметров для моделей глубокого обучения и машинного обучения с помощью Машинное обучение Azure.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 16a1c966b3f5a674f0ae1dc9c7ee078f45f8bdc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598236"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Настройка параметров для модели с помощью Машинное обучение Azure


Автоматизируйте эффективную настройку параметров с помощью Машинное обучение Azure [пакета](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true). Узнайте, как выполнить шаги, необходимые для настройки параметров с помощью [пакета SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true).

1. определение пространства поиска параметров;
1. Указание основной метрики для оптимизации  
1. Укажите политику раннего завершения для выполнения с низким уровнем выполнения
1. Выделение ресурсов
1. Запуск эксперимента с заданной конфигурацией
1. визуализация учебных запусков;
1. Выберите оптимальную конфигурацию для модели

## <a name="what-are-hyperparameters"></a>Сведения о гиперпараметрах

**Параметры** — это настраиваемые параметры, позволяющие управлять процессом обучения модели. Например, в нейронных сетях необходимо выбрать количество скрытых слоев и количество узлов в каждом слое. Производительность модели в значительной степени зависит от параметров.

 **Настройка** параметров «помощник» — это процесс поиска конфигурации параметров, которые приводят к лучшей производительности. Процесс обычно является дорогостоящим и выполняемым вручную.

Машинное обучение Azure позволяет автоматизировать настройку параметров и выполнять эксперименты в параллельном режиме для эффективной оптимизации параметров.


## <a name="define-the-search-space"></a>Определение области поиска

Настройте параметры, просмотрев диапазон значений, определенных для каждого параметра.

Параметры могут быть дискретными или непрерывными и иметь распределение значений, описываемых [выражением параметра](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py&preserve-view=true).

### <a name="discrete-hyperparameters"></a>Дискретные гиперпараметры 

Дискретные гиперпараметры определяются как `choice` в дискретных значениях. Параметр `choice` может принимать следующие значения:

* одно или несколько значений, разделенных запятыми;
* объект `range`;
* любой произвольный объект `list`.


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

В этом случае `batch_size` одно из значений [16, 32, 64, 128] и `number_of_hidden_layers` принимает одно из значений [1, 2, 3, 4].

Можно также указать следующие дополнительные дискретные параметры с помощью распределения:

* `quniform(low, high, q)` — возвращает значение типа round(uniform(low, high) / q) * q.
* `qloguniform(low, high, q)` — возвращает значение типа round(exp(uniform(low, high)) / q) * q.
* `qnormal(mu, sigma, q)` — возвращает значение типа round(normal(mu, sigma) / q) * q.
* `qlognormal(mu, sigma, q)` — возвращает значение типа round(exp(normal(mu, sigma)) / q) * q.

### <a name="continuous-hyperparameters"></a>Непрерывные гиперпараметры 

Непрерывные параметры задаются в виде распределения по непрерывному диапазону значений:

* `uniform(low, high)` — возвращает значение, равномерно распределенное между верхней и нижней границами.
* `loguniform(low, high)` — возвращает значение, полученное в соответствии с exp(uniform(low, high)), так что логарифм возвращаемого значения равномерно распределен.
* `normal(mu, sigma)` — возвращает реальное значение, которое обычно распределяется со средним значением mu и стандартным отклонением sigma.
* `lognormal(mu, sigma)` — возвращает значение, полученное в соответствии с exp(normal(mu, sigma)), так что логарифм возвращаемого значения нормально распределен.

Ниже приведен пример определения пространства параметров:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Этот код определяет пространство поиска с двумя параметрами: `learning_rate` и `keep_probability`. `learning_rate` имеет нормальное распределение со средним значением 10 и стандартным отклонением 3. `keep_probability` имеет равномерное распределение с минимальным значением 0,05 и максимальным значением 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Выборка пространства гиперпараметров

Укажите метод выборки параметров для использования в пространстве параметров. Машинное обучение Azure поддерживает следующие методы:

* Случайная выборка
* Решетчатая выборка
* Байесовская выборка

#### <a name="random-sampling"></a>Случайная выборка

[Случайная выборка](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py&preserve-view=true) поддерживает дискретные и непрерывные параметры. Он поддерживает раннее прекращение выполнения с низкой производительностью. Некоторые пользователи выполняют первоначальный поиск с случайной выборкой, а затем уточняют область поиска для улучшения результатов.

При случайной выборке значения гиперпараметров выбираются случайным образом из определенного пространства поиска. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Решетчатая выборка

[Выборка по сетке](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py&preserve-view=true) поддерживает дискретные параметры. Использование выборки по сетке можно использовать, если вы можете выполнять поиск в области поиска по бюджету. Поддерживает раннее завершение низкоуровневых запусков.

Выполняет простой поиск в сетке по всем возможным значениям. Выборка по сетке может использоваться только с `choice` параметрами. Например, в следующем пространстве имеется шесть выборок:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Байесовская выборка

[Выборка Байеса](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py&preserve-view=true) основана на алгоритме оптимизации Байеса. Выборка выбирается в зависимости от того, как выполнялись предыдущие выборки, чтобы новые примеры могли улучшить основную метрику.

Выборка Байеса рекомендуется при наличии достаточного бюджета для изучения пространства параметров. Для получения наилучших результатов рекомендуется, чтобы максимальное число запусков, большее или равное 20, превышало количество настраиваемых параметров. 

Количество одновременных запусков влияет на эффективность процесса настройки. Меньшее количество одновременных запусков может привести к повышению уровня конвергенции, так как меньшая степень параллелизма увеличивает количество запусков, которые пользуются преимуществами ранее завершенных запусков.

Выборка Байеса поддерживает `choice` только `uniform` `quniform` распределения по области поиска.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Укажите основную метрику

Укажите [основную метрику](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py&preserve-view=true) , для которой требуется оптимизировать настройку параметров. Для основной метрики оценивается каждый учебный запуск. Политика раннего завершения использует основную метрику для обнаружения запусков с низкой производительностью.

Укажите следующие атрибуты для основной метрики:

* `primary_metric_name`: Имя основной метрики должно точно совпадать с именем метрики, регистрируемой сценарием обучения
* `primary_metric_goal`: это может быть `PrimaryMetricGoal.MAXIMIZE` или `PrimaryMetricGoal.MINIMIZE`. Это свойство определяет, что будет выполняться при оценке прогонов: максимизация или минимизация основной метрики. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Этот пример увеличивает «точность».

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Ведение журнала метрик для настройки гиперпараметров

Сценарий обучения для модели **должен** регистрировать основную метрику во время обучения модели, чтобы он мог получить доступ к нему для настройки параметров.

Заполните запись основной метрики в скрипте обучения, используя следующий фрагмент кода:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Сценарий обучения вычисляет `val_accuracy` и регистрирует его в качестве основной метрики "точность". Каждый раз, когда метрика заносится в журнал, она получается службой настройки параметров. Вы можете определить частоту создания отчетов.

Дополнительные сведения о записи значений в журнал в ходе обучения модели см. в статье [Включение ведения журнала в запусках обучения Azure ML](how-to-track-experiments.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Укажите политику раннего завершения

Автоматическое завершение плохо выполняемых запусков с помощью политики раннего завершения. Раннее завершение улучшает вычислительную эффективность.

Можно настроить следующие параметры, определяющие, когда применяется политика.

* `evaluation_interval`: частота применения политики. Каждый раз, когда сценарий обучения регистрирует основную метрику, это считается одним интервалом. Значение, `evaluation_interval` равное 1, применит политику каждый раз, когда сценарий обучения сообщает основную метрику. Значение, `evaluation_interval` равное 2, применит политику каждый раз. Если значение для `evaluation_interval` не указано, по умолчанию используется 1.
* `delay_evaluation`: задерживает первую оценку политики для определенного количества интервалов. Это необязательный параметр, который позволяет избежать преждевременного завершения обучающих запусков, позволяя выполнять все конфигурации в течение минимального числа интервалов. Если этот параметр указан, политика применяет каждое кратное значение evaluation_interval, которое больше или равно delay_evaluation.

Машинное обучение Azure поддерживает следующие политики раннего завершения:
* [Политика Bandit](#bandit-policy)
* [Политика медианной остановки](#median-stopping-policy)
* [Политика выбора усечения](#truncation-selection-policy)
* [Политика без завершения](#no-termination-policy-default)


### <a name="bandit-policy"></a>Политика Bandit

[Политика бандит](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py&preserve-view=true#&preserve-view=truedefinition) основана на значении коэффициента времени и времени резерва и интервале оценки. Бандит завершает работу, когда основная метрика не попадает в указанный коэффициент временного резерва или сумму временного резерва по сравнению с наилучшим выполнением.

> [!NOTE]
> Выборка Байеса не поддерживает раннее завершение. При использовании выборки Байеса задайте `early_termination_policy = None` .

Укажите следующие параметры конфигурации:

* `slack_factor` или `slack_amount`: резерв времени, допустимый в отношении обучающего прогона с самой высокой эффективностью. `slack_factor` задает допустимый резерв времени как коэффициент. `slack_amount` указывает допустимый резерв времени как абсолютную величину, а не коэффициент.

    Например, рассмотрим политику бандит, применяемую с интервалом 10. Предположим, что наиболее производительное выполнение с интервалом 10 сообщило о первичной метрике 0,8 с целью максимизации основной метрики. Если в политике указано значение `slack_factor` 0,2, то все запуски обучения, для которых наилучшая метрика в интервале 10, не превышает 0,66 (0,8/(1 + `slack_factor` )), будут прерваны.
* `evaluation_interval`(необязательно) частота применения политики.
* `delay_evaluation`: (необязательно) откладывает первую оценку политики на указанное число интервалов


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

В этом примере политика раннего завершения применяется в каждом интервале, когда указываются метрики, начиная с оценочного интервала 5. Любой прогон, лучшая метрика которого меньше (1/(1+0,1)) или соответствует 91 % от наилучшего прогона, будет завершен.

### <a name="median-stopping-policy"></a>Политика медианной остановки

[Средняя остановка](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py&preserve-view=true) — это политика раннего завершения, основанная на средних показателях основных метрик, сообщаемых выполнениями. Эта политика вычисляет среднее выполнение всех обучающих запусков и завершает работу с основными значениями метрик, которые хуже медианы средних значений.

Она принимает следующие параметры конфигурации:
* `evaluation_interval`: частота применения политики (необязательный параметр).
* `delay_evaluation`: задерживает первую оценку политики для определенного количества интервалов (необязательный параметр).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

В этом примере политика раннего завершения применяется в каждом интервале, начиная с оценочного интервала 5. Выполнение прерывается в интервале 5, если его лучшая основная метрика хуже медианы скользящего среднего по интервалам 1:5 во всех учебных запусках.

### <a name="truncation-selection-policy"></a>Политика выбора усечения

[Выбор усечения](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py&preserve-view=true) отменяет процент наименьших выполнений в каждом интервале оценки. Выполняется сравнение с использованием основной метрики. 

Она принимает следующие параметры конфигурации:

* `truncation_percentage`: процент прогонов с наименьшей эффективностью, которые будут завершены в каждом интервале оценки. Целочисленное значение от 1 до 99.
* `evaluation_interval`(необязательно) частота применения политики.
* `delay_evaluation`: (необязательно) откладывает первую оценку политики на указанное число интервалов


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

В этом примере политика раннего завершения применяется в каждом интервале, начиная с оценочного интервала 5. Выполнение завершается с интервалом 5, если его производительность в интервале 5 превышает 20% производительности всех запусков с интервалом 5.

### <a name="no-termination-policy-default"></a>Политика завершения отсутствует (по умолчанию)

Если политика не указана, служба настройки параметров может разрешить выполнение всех обучающих запусков до завершения.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Выбор политики раннего завершения

* Для консервативной политики, которая обеспечивает экономию без завершения планирования заданий, рассмотрим политику среднего преостановки с `evaluation_interval` 1 и `delay_evaluation` 5. Это консервативные настройки, которые могут обеспечить экономию приблизительно 25–35 % без потерь по основной метрике (на основе наших оценочных данных).
* Для более агрессивной экономии используйте политику бандит с меньшим допустимым параметром резерва или с более крупным процентом усечения.

## <a name="allocate-resources"></a>Выделение ресурсов

Контролируйте бюджет ресурсов, указав максимальное количество обучающих запусков.

* `max_total_runs`: Максимальное число запусков для обучения. Должно быть целым числом от 1 до 1000.
* `max_duration_minutes`(необязательно) максимальная длительность (в минутах) эксперимента настройки параметров. Выполняется после отмены этой длительности.

>[!NOTE] 
>Если указаны одновременно параметры `max_total_runs` и `max_duration_minutes`, эксперимент по настройке гиперпараметров прекращается при достижении первого из двух пороговых значений.

Кроме того, укажите максимальное количество одновременно выполняемых учебных запусков при настройке гиперпараметров.

* `max_concurrent_runs`: (необязательно) максимальное число запусков, которые могут выполняться одновременно. Если не указано, все запуски запускаются параллельно. Если указано, должно быть целым числом от 1 до 100.

>[!NOTE] 
>Количество параллельных прогонов зависит от ресурсов, доступных в заданном целевом объекте вычисления. Убедитесь, что целевой объект вычислений содержит доступные ресурсы для требуемого параллелизма.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Этот код настраивает эксперимент настройки параметров, чтобы использовать максимум 20 запусков, одновременно выполняющих четыре конфигурации.

## <a name="configure-experiment"></a>Настройка эксперимента

Чтобы [настроить эксперимент настройки параметров](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py&preserve-view=true) , укажите следующие параметры.
* Область поиска определенного параметра
* Политика раннего завершения
* Основная метрика
* Параметры выделения ресурсов
* скриптрунконфиг `src`

Скриптрунконфиг — это обучающий сценарий, который будет выполняться с примерами параметров. Он определяет ресурсы на задание (один или несколько узлов) и целевой объект вычислений для использования.

> [!NOTE]
>Целевой объект вычислений, указанный в, `src` должен иметь достаточно ресурсов для удовлетворения вашего уровня параллелизма. Дополнительные сведения о Скриптрунконфиг см. в статье [Настройка обучающих запусков](how-to-set-up-training-targets.md).

Конфигурация эксперимента по настройке гиперпараметров:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Отправка эксперимента

Определив конфигурацию настройки параметров, [отправьте эксперимент](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Горячий запуск эксперимента по настройке параметров (необязательно)

Для поиска лучших значений параметров в модели можно использовать итеративный процесс. Вы можете повторно использовать знания из пяти предыдущих запусков, чтобы ускорить настройку параметров.


Горячий запуск обрабатывается по-разному в зависимости от метода выборки:
- **Выборка Байеса**: пробные версии из предыдущего запуска используются в качестве предыдущих знаний для выбора новых примеров и для улучшения основной метрики.
- **Случайная выборка** или **выборка в сетке**. для определения плохо выполняемых запусков с ранним прекращением используются знания из предыдущих запусков. 

Укажите список родительских запусков, из которых требуется горячий запуск.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

При отмене эксперимента по настройке параметров можно возобновить выполнение обучающих запусков с последней контрольной точки. Однако сценарий обучения должен поддерживать логику контрольных точек.

Обучающий запуск должен использовать одну и ту же конфигурацию параметров и подключить выходные папки. Обучающий скрипт должен принять `resume-from` аргумент, который содержит файлы контрольной точки или модели, из которых будет возобновлена работа по обучению. Вы можете возобновить отдельные запуски обучения, используя следующий фрагмент кода:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Вы можете настроить эксперимент по настройке параметров в качестве горячего запуска из предыдущего эксперимента или возобновления отдельных запусков обучения с помощью необязательных параметров `resume_from` и `resume_child_runs` в конфигурации.

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Визуализация эксперимента

Используйте мини-приложение [записной книжки](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true) для визуализации хода выполнения обучения. Следующий фрагмент кода визуализирует все запуски по настройке гиперпараметров в одном отображении, создаваемом в записной книжке Jupyter:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Этот код отображает таблицу со сведениями об учебных запусках для каждой из конфигураций гиперпараметров.

![Таблица настройки гиперпараметров](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Вы также можете визуализировать эффективность каждого прогона в ходе обучения. 

![График настройки гиперпараметров](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Можно визуально вычислить корреляцию между производительностью и значениями отдельных параметров, используя график параллельных координат. 

[![параллельные координаты для настройки гиперпараметров](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Вы также можете визуализировать все запуски настройки параметров на веб-портале Azure. Дополнительные сведения о том, как просмотреть эксперименты на портале, см. в разделе [мониторинг экспериментов](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Поиск наиболее эффективной модели

После завершения настройки параметров, выполнив настройку, найдите наиболее подходящую конфигурацию и значения параметров.

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Пример записной книжки
См. Дополнительные сведения в записных книжках в этой папке:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия
* [Руководство по отслеживанию эксперимента](how-to-track-experiments.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
