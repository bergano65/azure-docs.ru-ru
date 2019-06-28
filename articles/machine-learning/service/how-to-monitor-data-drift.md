---
title: Как определить смещение данных (Предварительная версия) на развертывание AKS
titleSuffix: Azure Machine Learning service
description: Сведения о способах поиска данных о смещении в службе Azure Kubernetes развертывания моделей в службе машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: e4deeab28fb643ff32624ba9dd16574e621f508c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333269"
---
# <a name="how-to-detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Как определить смещение данных (Предварительная версия) на моделях, развернутых в службе Azure Kubernetes
В этой статье вы узнаете, как отслеживать [данные о смещении](concept-data-drift.md) между обучающих наборов данных и вывод данных развернутой модели. 

Смещение данных является одним из основных причин, где точности модели падение с течением времени. Это происходит, когда данные в модель в рабочей среде отличается от данных, используемых для обучения модели. Службы машинного обучения Azure можно отслеживать данные о смещении, с помощью средства обнаружения данных о смещении. Если обнаруживается смещение, служба может отправлять оповещение для вас.  

> [!Note]
> Эта служба находится на (Предварительная версия) и ограниченные в параметры конфигурации. См. в нашем [документации по API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) и [заметки о выпуске](azure-machine-learning-release-notes.md) подробные сведения и обновления. 

С помощью службы машинного обучения Azure можно отслеживать входные данные для модели, развернутой в AKS и сравнить эти данные в обучающем наборе данных для модели. С регулярными интервалами, вывод данных является [моментальных снимков и профилировать](how-to-explore-prepare-data.md), затем вычисляемых на основе набора базовых показателей для создания анализ данных о смещении: 

+ Измеряет величина смещения данных, называется коэффициент смещения.
+ Меры данные переместятся вклад функцией, о том, какие функции вызвало данные о смещении.
+ Меры расстояния метрики. В настоящее время вычисляются банком Dresdner и расстояния энергии.
+ Измеряет дистрибутивов функций. В настоящее время оценки плотности ядра и гистограммы.
+ Отправьте оповещения, данные переместятся по электронной почте.

Дополнительные сведения о том, как эти показатели вычисляются, см. в разделе [данные о смещении концепция](concept-data-drift.md) статьи.

## <a name="prerequisites"></a>Технические условия

- Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

- Должны быть установлены рабочая область службы "Машинное обучение Azure" и пакет SDK Машинного обучения Azure для Python. Дополнительные сведения о получении этих необходимых компонентов см. в документе [Настройка среды разработки](how-to-configure-environment.md).

- [Настройте среду](how-to-configure-environment.md), а затем установить смещение данных пакета SDK, используя следующую команду:

    ```
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

- Настройка [сборщика данных модели](how-to-enable-data-collection.md) для сбора данных из модели развертывания AKS и подтверждения данных собираются в `modeldata` контейнер больших двоичных объектов.

## <a name="import-dependencies"></a>Импорт зависимостей 
Импортируйте зависимости, используемые в этом руководстве:

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>Настроить данные о смещении 

В следующем примере Python демонстрируется настройка `DataDriftDetector` объекта:

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

Дополнительные сведения см. в разделе [DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) ссылки.

## <a name="submit-a-datadriftdetector-run"></a>Запуске DataDriftDetector

С помощью DataDriftDetector настроен, вы можете отправить [данные о смещении запуска](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) в указанный день для модели. 

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

## <a name="get-data-drift-analysis-results"></a>Получить данные о смещении результатов анализа

В следующем примере Python показано, как отобразить соответствующие данные о смещении метрик. Возвращаемый метрики можно использовать для создания собственных визуализаций:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Показать смещения данных](media/how-to-monitor-data-drift/drift_show.png)

Дополнительные сведения о метрики, которые вычисляются, см. в разделе [данные о смещении концепция](concept-data-drift.md) статьи.

## <a name="schedule-data-drift-detection"></a>Расписание данные о смещении обнаружения 

Включение расписания смещение данных выполняет DataDriftDetector, запустите с указанной частотой. Если коэффициент смещение превышает заданное пороговое значение, отправляется сообщение электронной почты. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Конфигурация обнаружения смещение данных можно увидеть на странице сведений о модели на портале Azure.

![Портал Azure Config смещения данных](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Просмотр результатов в пользовательском Интерфейсе рабочей области машинного Обучения Azure

Чтобы просмотреть результаты в пользовательском Интерфейсе рабочей области машинного Обучения Azure, перейдите на страницу «модель». На вкладке "Сведения" модели показана конфигурация данные о смещении. На вкладке «Данные о смещении. (Предварительная версия)» теперь доступна визуализации метрик данные о смещении. 

![Портал Azure, данные о смещении](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="setting-up-alerts"></a>Настройка оповещений 

Задать коэффициент смещения порог предупреждения и предоставляя адрес электронной почты [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) оповещение по электронной почте отправляется в том случае, если коэффициент смещение превышает пороговое значение. Все данные о смещении метрики хранятся в ресурс app insights, связанный с рабочей области службы машинного обучения Azure позволяет настроить настраиваемых оповещений и действия. Перейдите по ссылке в оповещения по электронной почте для запросов аналитики приложений.

![Оповещение по электронной почте о смещении данных](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>Дальнейшие действия

* Полный пример использования смещение данных, см. в разделе [данных машинного Обучения Azure переместятся записной книжки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Эта записная книжка Jupyter демонстрируется использование [открытого набора данных Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) для обучения модели для прогнозирования погоды, развернуть его в AKS и отслеживать данные о смещении. 

* Значительно просим свои вопросы, замечания или предложения при перемещении данных смещение к общие доступности. Используйте кнопку обратной связи продукта ниже! 
