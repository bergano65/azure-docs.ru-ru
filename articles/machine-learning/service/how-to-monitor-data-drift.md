---
title: Определить смещение данных (Предварительная версия) на развертывание AKS
titleSuffix: Azure Machine Learning service
description: Определение смещения данных в службе Azure Kubernetes развертывания моделей в службе машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806024"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Определить смещение данных (Предварительная версия) на моделях, развернутых в Azure Kubernetes Service (AKS)

В этой статье вы узнаете, как отслеживать данные о смещении между набора данных для обучения и вывод данных развернутой модели. В рамках машинного обучения обученных моделей машинного обучения могут возникнуть снижение прогнозирования производительности из-за смещение. В службе машинного обучения Azure вы можете отслеживать данные о смещении, а служба может отправлять оповещение по электронной почте для вас при обнаружении смещения.

## <a name="what-is-data-drift"></a>Что такое смещение данных?

Смещение данных происходит, когда данные в модель в рабочей среде отличается от данных, используемых для обучения модели. Это одна из основных причин где точности модели падение с течением времени, таким образом наблюдение за данными о смещении помогает обнаруживать проблемы производительности модели. 

## <a name="what-can-i-monitor"></a>Что можно отслеживать?

С помощью службы машинного обучения Azure можно отслеживать входные данные для модели, развернутой в AKS и сравнить эти данные в обучающем наборе данных для модели. С регулярными интервалами, вывод данных является [моментальных снимков и профилировать](how-to-explore-prepare-data.md), затем вычисляемых на основе набора базовых показателей для создания анализ данных о смещении: 

+ Измеряет величина смещения данных, называется коэффициент смещения.
+ Меры данные переместятся вклад функцией, о том, какие функции вызвало данные о смещении.
+ Меры расстояния метрики. В настоящее время вычисляются банком Dresdner и расстояния энергии.
+ Измеряет дистрибутивов функций. В настоящее время оценки плотности ядра и гистограммы.
+ Отправьте оповещения, данные переместятся по электронной почте.

> [!Note]
> Эта служба находится на (Предварительная версия) и ограниченные в параметры конфигурации. См. в нашем [документации по API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) и [заметки о выпуске](azure-machine-learning-release-notes.md) подробные сведения и обновления. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Как выполняется мониторинг данных о смещении в службе машинного обучения Azure

С помощью службы машинного обучения Azure, данные о смещении отслеживается с помощью наборов данных или развертывания. Для отслеживания данных о смещении, указан набор данных базовых показателей — обычно набор данных для обучения модели. Второй набор данных — обычно модель входные данные, собранные из развертывания - тестируется на базовый набор данных. Оба наборы данных представляют собой [профилировать](how-to-explore-prepare-data.md#explore-with-summary-statistics) и службе мониторинга отклонения входных данных для данных. Чтобы определить разницу между двумя наборами данных обучается модель машинного обучения. Эффективность модели преобразуется в смещение коэффициента, который измеряет величина смещения между двумя наборами данных. С помощью [модели interpretability](machine-learning-interpretability-explainability.md), функций, способствующих коэффициент смещения вычисляются. Из набора данных профиля отслеживается статистические сведения о каждой функции. 

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас ее нет, создайте бесплатную учетную запись перед началом работы. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

- Должны быть установлены рабочая область службы "Машинное обучение Azure" и пакет SDK Машинного обучения Azure для Python. Следуйте инструкциям по [созданию рабочей области Службы машинного обучения Azure](setup-create-workspace.md#sdk), чтобы выполнить следующее:

    - создать среду Miniconda;
    - установить пакет SDK для Машинного обучения Azure для Python.
    - Создание рабочей области
    - Запись в файл конфигурации рабочей области (aml_config/config.json).

- Установите смещение данных пакета SDK, используя следующую команду:

    ```shell
    pip install azureml-contrib-datadrift
    ```

- Создание [набора данных](how-to-create-register-datasets.md) из вашей модели данных для обучения.

- Укажите набор данных для обучения при [регистрации](concept-model-management-and-deployment.md) модели. В следующем примере показано использование `datasets` параметр, чтобы указать набор данных для обучения:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Включение сбора данных модели](how-to-enable-data-collection.md) для сбора данных из модели развертывания AKS и подтверждения данных собираются в `modeldata` контейнер больших двоичных объектов.

## <a name="configure-data-drift"></a>Настроить данные о смещении
Чтобы настроить данные о смещении для своего эксперимента, импортируйте зависимости, как показано в следующем примере Python. 

В этом примере демонстрируется настройка [ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) объекта:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Запуске DataDriftDetector

С помощью `DataDriftDetector` объект настроен, вы можете отправить [данные о смещении запуска](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) в указанный день для модели. В процессе выполнения, включите DataDriftDetector предупреждений, задав `drift_threshold` параметра. Если [datadrift_coefficient](#metrics) превышает заданный `drift_threshold`, отправляется сообщение электронной почты.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Визуализировать метрики о смещении

<a name="metrics"></a>

После отправки вашего DataDriftDetector запуска, имеется возможность см. в разделе о смещении метрики, которые сохраняются в каждой итерации выполнения задачи смещение данных:


|Метрика|Описание|
--|--|
wasserstein_distance|Определенные для распространения одномерный числовые статистические расстояние.|
energy_distance|Определенные для распространения одномерный числовые статистические расстояние.|
datadrift_coefficient|Точно так же, как коэффициент корреляции Мэтью вычисляется, но эти выходные данные — вещественное число от 0 до 1. В контексте смещение 0 указывает без смещения, а 1 указывает максимальное смещение.|
datadrift_contribution|Возможность важность функциональные возможности для отклонения.|

Чтобы просмотреть метрики о смещении несколькими способами:

* Используйте мини-приложение Jupyter.
* Используйте [ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) функции на любом `datadrift` выполнения объекта.
* Просмотреть метрики на портале Azure в модели

В следующем примере Python показано, как отобразить соответствующие данные о смещении метрик. Возвращаемый метрики можно использовать для создания собственных визуализаций:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Обнаружено машинным обучением Azure смещение данных см. в разделе](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Расписание данные о смещении сканирования 

При включении данные о смещении обнаружения DataDriftDetector выполняется с частотой, указанной, по расписанию. Если datadrift_coefficient достигает заданного `drift_threshold`, отправляется сообщение электронной почты с каждого запланированного выполнения. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Конфигурация обнаружения смещение данных можно увидеть на странице сведений о модели на портале Azure.

![Портал Azure Config смещения данных](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Просмотр результатов в пользовательском Интерфейсе рабочей области машинного Обучения Azure

Чтобы просмотреть результаты в пользовательском Интерфейсе рабочей области машинного Обучения Azure, перейдите на страницу «модель». На вкладке "Сведения" модели показана конфигурация данные о смещении. На вкладке «Данные о смещении. (Предварительная версия)» теперь доступна визуализации метрик данные о смещении. 

![Портал Azure, данные о смещении](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>Получения оповещений о смещении

Задать коэффициент смещения порог предупреждения и указав адрес электронной почты [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) , будут автоматически отправляться оповещение по электронной почте каждый раз, когда коэффициент смещение превышает пороговое значение. 

Чтобы настроить пользовательские оповещения и действия, все данные о смещении метрики хранятся в [Application Insights](how-to-enable-app-insights.md) ресурс, который был создан вместе с рабочей области службы машинного обучения Azure. Перейдите по ссылке в электронном оповещении в запрос Application Insights.

![Оповещение по электронной почте о смещении данных](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Переобучить модель после смещения

Когда данные о смещении негативно влияет на производительность развернутую модель, пришло время для повторного обучения модели. Следующие [ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) метод дает начальное представление различия между наборами данных для обучения старые и новые. 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

На основе выходных данных предыдущего примера кода, может потребоваться повторного обучения модели. Для этого выполните следующие действия.

* Проанализируйте собранные данные и Подготовка данных для обучения новой модели.
* Разбейте его на данных обучения и тестирования.
* Обучение модели, снова, используя новые данные.
* Оценка эффективности модели вновь созданным.
* Развертывание новой модели, если производительности лучше, чем производственную модель.

## <a name="next-steps"></a>Следующие шаги

* Полный пример использования смещение данных, см. в разделе [данных машинного Обучения Azure переместятся записной книжки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Эта записная книжка Jupyter демонстрируется использование [открытого набора данных Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) для обучения модели для прогнозирования погоды, развернуть его в AKS и отслеживать данные о смещении. 

* Значительно просим свои вопросы, замечания или предложения при перемещении данных смещение к общие доступности. Используйте кнопку обратной связи продукта ниже! 
