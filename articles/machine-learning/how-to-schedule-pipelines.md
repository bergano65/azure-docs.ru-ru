---
title: Расписание конвейеров машинного обучения Azure
titleSuffix: Azure Machine Learning
description: Запланируйте конвейеры машинного обучения Azure с помощью SDK Для Python. Запланированные конвейеры позволяют автоматизировать рутинные, трудоемкие задачи, такие как обработка данных, обучение и мониторинг.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: fed411ea171274513308ec3efa68da80e4d25f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116759"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Расписание конвейеров машинного обучения с помощью Azure Machine Learning SDK для Python

В этой статье вы узнаете, как запрограммировать запланировать конвейер для запуска на Azure. Вы можете создать расписание, основанное на прошедшем времени или на изменениях файловой системы. Расписание на основе времени может быть использовано для выполнения рутинных задач, таких как мониторинг дрейфа данных. Расписание на основе изменений может использоваться для реагирования на нерегулярные или непредсказуемые изменения, такие как загрузка новых данных или редактирование старых данных. Научившись создавать расписания, вы узнаете, как их извлекать и деактивировать.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки На Azure, создайте [бесплатную учетную запись](https://aka.ms/AMLFree).

* Среда Python, в которой установлен SDK Для Python, лазурный машинный учебный SDK. Для получения дополнительной информации [см. Создание и управление многоразовыми средами для обучения и развертывания с помощью машинного обучения Azure.](how-to-use-environments.md)

* Рабочее пространство машинного обучения с опубликованным конвейером. Можно использовать встроенный в [создание и запуск конвейеров машинного обучения с помощью Azure Machine Learning SDK.](how-to-create-your-first-pipeline.md)

## <a name="initialize-the-workspace--get-data"></a>Инициализация рабочей области & получения данных

Чтобы запланировать конвейер, вам понадобится ссылка на рабочее пространство, идентификатор опубликованного конвейера и название эксперимента, в котором вы хотите создать расписание. Вы можете получить эти значения со следующим кодом:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Создание расписания

Чтобы запустить конвейер на постоянной основе, вы создадите расписание. Ассоциирует `Schedule` конвейер, эксперимент и спусковой крючок. Триггер может быть`ScheduleRecurrence` либо тем, который описывает ожидание между пробегами, либо путем Datastore, который определяет каталог для просмотра изменений. В любом случае вам понадобится идентификатор конвейера и название эксперимента, в котором будет создано расписание.

В верхней части файла `Schedule` python `ScheduleRecurrence` импортируйте и классы:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Создание расписания на основе времени

Конструктор `ScheduleRecurrence` имеет необходимый `frequency` аргумент, который должен быть одним из следующих строк: "Минута", "Час", "День", "Неделя", или "Месяц". Он также требует целый `interval` аргумент с указанием, сколько `frequency` единиц должно проходить между началом расписания. Дополнительные аргументы позволяют быть более конкретными о времени начала, как подробно описано в [Расписание Повторяющиеся документы SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Создайте `Schedule` запуск каждые 15 минут:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Создание графика на основе изменений

Трубопроводы, вызванные изменениями файлов, могут быть более эффективными, чем графики, основанные на времени. Например, при изменении файла или при добавлении нового файла в каталог данных может потребоваться этап предварительной обработки. Вы можете отслеживать любые изменения в хранилище данных или изменения в определенном каталоге в хранилище данных. Если вы отслеживаете определенный каталог, изменения в подкаталогах этого каталога _не_ запускают запуск.

Чтобы создать реактивную `Schedule`файл, необходимо `datastore` установить параметр в вызове [на Schedule.create.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-) Чтобы следить за папкой, установите `path_on_datastore` аргумент.

Аргумент `polling_interval` позволяет указать в течение нескольких минут частоту проверки хранилища данных на наличие изменений.

Если конвейер был построен с помощью [Паразамля конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) `data_path_parameter_name` [DataPath,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) можно установить эту переменную на имя измененного файла, установив аргумент.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Дополнительные аргументы при создании расписания

В дополнение к аргументам, рассмотренным `status` ранее, можно установить аргумент `"Disabled"` для создания неактивного графика. Наконец, `continue_on_step_failure` позволяет пройти Boolean, который будет переопределять поведение сбоя трубопровода по умолчанию.

## <a name="view-your-scheduled-pipelines"></a>Просмотр запланированных конвейеров

В веб-браузере перейдите на Azure Machine Learning. Из раздела **Endpoints** навигационной панели выберите **конечные точки трубопровода.** Это приведет вас к списку конвейеров, опубликованных в рабочем пространстве.

![Страница трубопроводов AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

На этой странице вы можете увидеть краткую информацию обо всех конвейерах в рабочем пространстве: имена, описания, статус и так далее. Просверлите, нажав в конвейере. На полученной странице, Есть более подробную информацию о вашем конвейере, и вы можете просверлить в отдельных работает.

## <a name="deactivate-the-pipeline"></a>Отключение трубопровода

Если у `Pipeline` вас есть, что опубликовано, но не запланировано, вы можете отключить его с:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Если конвейер запланирован, сначала необходимо отменить расписание. Извлеките идентификатор расписания с портала или запустив:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Если у `schedule_id` вас есть вы хотите отключить, запустить:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Если вы `Schedule.list(ws)` затем запустить снова, вы должны получить пустой список.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы использовали SDK для изучения машин Azure для Python для планирования конвейера двумя различными способами. Одно расписание повторяется в зависимости от прошедшего времени часовых часов. Другое расписание выполняется, если файл `Datastore` изменен в указанном или в каталоге в этом хранилище. Вы видели, как использовать портал для изучения трубопровода и отдельных запусков. Наконец, вы узнали, как отключить график, так что конвейер перестанет работать.

Дополнительные сведения см. в разделе:

> [!div class="nextstepaction"]
> [Использование конвейеров Машинного обучения Azure для пакетной оценки](tutorial-pipeline-batch-scoring-classification.md)

* Подробнее о [конвейерах](concept-ml-pipelines.md)
* Узнайте больше об [изучении машинного обучения Azure с Jupyter](samples-notebooks.md)

