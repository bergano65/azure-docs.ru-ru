---
title: Обнаружение смещения данных (Предварительная версия) при развертываниях AKS
titleSuffix: Azure Machine Learning
description: Обнаружение смещения данных в развернутых моделях службы Kubernetes Azure в Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 24b1c22d9badc58360b0355bb4ef89017db27f06
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796740"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Обнаружение смещения данных (Предварительная версия) в моделях, развернутых в службе Kubernetes Azure (AKS)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Из этой статьи вы узнаете, как отслеживать разницу данных между набором данных для обучения и данными о выводе в развернутой модели. В контексте машинного обучения обученные модели машинного обучения могут испытывать снижение производительности предсказания из-за смещения. С помощью Машинное обучение Azure можно отслеживать смещение данных, и служба может отправлять Вам оповещение по электронной почте при обнаружении отклонения.

## <a name="what-is-data-drift"></a>Что такое смещение данных?

В контексте машинного обучения смещение данных — это изменение входных данных модели, которое приводит к снижению производительности модели. Это одна из основных причин снижения точности модели с течением времени. Таким образом, отслеживание смещения данных помогает выявить проблемы с производительностью модели. 

## <a name="what-can-i-monitor"></a>Что можно отслеживать?

С помощью Машинное обучение Azure можно отслеживать входные данные для модели, развернутой на AKS, и сравнивать их с набором данных для обучения модели. С регулярными интервалами данные о выводе являются [моментальными снимками и профилированием](how-to-explore-prepare-data.md), а затем рассчитываются по базовому набору данных, чтобы получить анализ расравномерного пересчета, который: 

+ Измеряет величину смещения данных, называемую коэффициентом смещения.
+ Измеряет вклад данных по функциям, уведомляя о том, какие компоненты привели к смещению данных.
+ Измеряет метрики расстояний. В настоящее время вычисляются Dresdner и расстояние энергии.
+ Измеряет распределение компонентов. В настоящее время оценка плотности ядра и гистограммы.
+ Отправка оповещений о смещении данных по электронной почте.

> [!Note]
> Эта служба находится в (Предварительная версия) и ограничена параметрами конфигурации. Дополнительные сведения и обновления см. в [документации по API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) и [заметках о выпуске](azure-machine-learning-release-notes.md). 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Как отслеживается смещение данных в Машинное обучение Azure

С помощью Машинное обучение Azure отклонения данных отслеживаются с помощью наборов данных или развертываний. Для отслеживания смещения данных можно указать базовый набор данных — обычно это набор обучающих данных для модели. Второй набор данных — обычно входные данные модели, собранные из развертывания, тестируются на основе базового набора данных. Оба набора данных являются профилированием и входными данными для службы отслеживания смещения данных. Модель машинного обучения обучена для обнаружения различий между двумя наборами данных. Производительность модели преобразуется в коэффициент смещения, который измеряет величину смещения между двумя наборами данных. Используя [интерпретируемость модели](how-to-machine-learning-interpretability.md), вычисляются функции, влияющие на коэффициент смещения. В профиле набора данных сохранится статистическая информация о каждом компоненте. 

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас ее нет, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня.

- Установленный пакет SDK службы "Машинное обучение Azure" для Python. Следуйте инструкциям из статьи об [установке пакета SDK службы "Машинное обучение Azure" для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), чтобы выполнить следующее:

    - создать среду Miniconda;
    - установить пакет SDK для Машинного обучения Azure для Python.

- [Рабочая область машинное обучение Azure](how-to-manage-workspace.md).

- [Файл конфигурации](how-to-configure-environment.md#workspace)рабочей области.

- Установите пакет SDK для смещения данных с помощью следующей команды:

    ```shell
    pip install azureml-datadrift
    ```

- Создайте [набор данных](how-to-create-register-datasets.md) на основе обучающих данных модели.

- Укажите набор данных для обучения при [регистрации](concept-model-management-and-deployment.md) модели. В следующем примере демонстрируется использование параметра `datasets` для указания набора данных для обучения.

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Включите сбор данных модели](how-to-enable-data-collection.md) для сбора данных из AKS развертывания модели и подтверждения данных, собираемых в контейнере `modeldata` больших двоичных объектов.

## <a name="configure-data-drift"></a>Настройка смещения данных
Чтобы настроить смещение данных для эксперимента, импортируйте зависимости, как показано в следующем примере Python. 

В этом примере демонстрируется настройка объекта [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) .

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Отправка Датадрифтдетектор выполнения

После настройки объекта `DataDriftDetector` можно отправить [смещение данных](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) в указанную дату для модели. В ходе выполнения включите Датадрифтдетектор Alerts, задав параметр `drift_threshold`. Если [datadrift_coefficient](#metrics) превышает заданное `drift_threshold`, отправляется сообщение электронной почты.

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

## <a name="visualize-drift-metrics"></a>Визуализация метрик смещения

<a name="metrics"></a>

После отправки выполнения Датадрифтдетектор вы сможете увидеть метрики смещения, сохраненные в каждой итерации выполнения для задачи «смещение данных».


|Метрика|Description (Описание)|
--|--|
wasserstein_distance|Статистическое расстояние, определенное для одномерного числового распределения.|
energy_distance|Статистическое расстояние, определенное для одномерного числового распределения.|
datadrift_coefficient|Вычисляется аналогично коэффициенту корреляции Мэтью, но этот результат является вещественным числом в диапазоне от 0 до 1. В контексте смещения значение 0 означает отсутствие смещения, а 1 — Максимальное смещение.|
datadrift_contribution|Важность признаков компонентов, которые вносят вклад в отклонения.|

Существует несколько способов просмотра метрик смещения.

* Используйте мини-приложение `RunDetails`[Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Используйте функцию [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) для любого `datadrift`ного объекта Run.
* Просмотрите метрики в разделе **модели** рабочей области в [машинное обучение Azure Studio](https://ml.azure.com).

В следующем примере Python показано, как построить диаграмму релевантных метрик смещения данных. Возвращенные метрики можно использовать для создания пользовательских визуализаций:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![См. статью о смещении данных, обнаруженном Машинное обучение Azure](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Планирование сканирования смещения данных 

Когда вы включаете обнаружение расхождения данных, Датадрифтдетектор выполняется по заданной запланированной частоте. Если datadrift_coefficient достигает указанного `drift_threshold`, то при каждом запланированном выполнении отправляется сообщение электронной почты. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Конфигурация средства обнаружения смещения данных доступна в разделе **модели** на вкладке **сведения** в рабочей области в [машинное обучение Azure Studio](https://ml.azure.com).

[Смещение данных ![Машинное обучение Azure Studio](media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Просмотр результатов в Машинное обучение Azure Studio

Чтобы просмотреть результаты в рабочей области в [машинное обучение Azure Studio](https://ml.azure.com), перейдите на страницу модель. На вкладке сведения модели отображается конфигурация смещения данных. Вкладка « **смещение данных** » теперь доступна для визуализации метрик смещения данных. 

[Смещение данных ![Машинное обучение Azure Studio](media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Получение оповещений о смещении

Установив пороговое значение коэффициента отклонения и указав адрес электронной почты, [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) оповещение по электронной почте автоматически отправляется, когда коэффициент смещения превышает пороговое значение. 

Чтобы настроить настраиваемые оповещения и действия, все метрики смещения данных хранятся в [Application Insights](how-to-enable-app-insights.md) ресурсе, который был создан вместе с рабочей областью машинное обучение Azure. Можно перейти по ссылке в оповещении по электронной почте на Application Insights запрос.

![Оповещение о смещении данных по электронной почте](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Переобучение модели после отклонения

Когда смещение данных отрицательно влияет на производительность развернутой модели, пора переучить модель. Для этого перейдите к следующим шагам.

* Изучите собранные данные и подготавливайте данные для обучения новой модели.
* Разделите его на данные для обучения и тестирования.
* Обучение модели с помощью новых данных.
* Оцените производительность вновь созданной модели.
* Развертывайте новую модель, если производительность выше, чем в рабочей модели.

## <a name="next-steps"></a>Дальнейшие действия

* Полный пример использования смещения данных см. в [записной книжке "смещение данных машинного обучения Azure](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb)". В этом Jupyter Notebook демонстрируется использование [открытого набора данных Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) для обучения модели для прогнозирования погоды, ее развертывания в AKS и отслеживания смещения данных. 

* Обнаружение смещения данных с помощью [мониторов наборов данных](how-to-monitor-datasets.md).

* Мы очень ценим ваши вопросы, комментарии или предложения по мере того, как смещение данных перемещается в общую доступность. Используйте кнопку отзыв о продукте ниже. 
