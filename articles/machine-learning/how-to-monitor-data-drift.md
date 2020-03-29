---
title: Обнаружение дрейфа данных о развертываниях AKS
titleSuffix: Azure Machine Learning
description: Обнаружение дрейфа данных (предварительный просмотр) в службе Azure Kubernetes, развернутой в модели Машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537010"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Обнаружение дрейфа данных (предварительный просмотр) на моделях, развернутых в службе Azure Kubernetes (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

В этой статье вы узнаете, как контролировать дрейф данных между набором обучающих данных и данными выводов развернутой модели. В контексте машинного обучения обученные модели машинного обучения могут испытывать снижение производительности прогнозирования из-за дрейфа. С помощью Azure Machine Learning вы можете отслеживать дрейф данных, и служба может отправить вам предупреждение по электронной почте при обнаружении дрейфа.

## <a name="what-is-data-drift"></a>Что такое дрейф данных?

В контексте машинного обучения дрейф данных представляет собой изменение входных данных модели, что приводит к снижению производительности модели. Это одна из главных причин, когда точность модели ухудшается с течением времени, таким образом, мониторинг дрейфа данных помогает обнаружить проблемы с производительностью модели. 

## <a name="what-can-i-monitor"></a>Что я могу контролировать?

С помощью Машинного обучения Azure можно отслеживать входные данные в модель, развернутую на AKS, и сравнивать эти данные с набором обучающих данных для модели. Через регулярные промежутки времени данные выводов [моментально и профилированные,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)затем вычисляются на основе базового набора данных для получения анализа дрейфа данных, который: 

+ Измеряет величину дрейфа данных, называется коэффициент дрейфа.
+ Измеряет вклад дрейфа данных по функциям, указывая, какие функции вызвали дрейф данных.
+ Измеряет показатели расстояния. В настоящее время вычисляются Вассерштейн и Энергетическая дистанция.
+ Измеряет распределение функций. В настоящее время оценка плотности ядра и гистограммы.
+ Отправка оповещений на дрейф данных по электронной почте.

> [!Note]
> Эта услуга находится в (предварительный просмотр) и ограничена в вариантах конфигурации. Пожалуйста, ознакомьтесь с нашими [API Документация](https://docs.microsoft.com/python/api/azureml-datadrift/) и [релиз-примечания](azure-machine-learning-release-notes.md) для получения подробной информации и обновлений. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Как отслеживается дрейф данных в Azure Machine Learning

С помощью машинного обучения Azure дрейф данных контролируется с помощью наборов данных или развертывания. Для мониторинга дрейфа данных указан базовый набор данных - обычно набор данных для подготовки для модели. Второй набор данных - обычно модель входных данных, собранных в результате развертывания, - тестируется на основе базового набора данных. Оба набора данных профилируются и вносятся в службу мониторинга дрейфа данных. Модель машинного обучения обучается обнаружению различий между двумя наборами данных. Производительность модели преобразуется в коэффициент дрейфа, который измеряет величину дрейфа между двумя наборами данных. С помощью [интерпретации модели](how-to-machine-learning-interpretability.md)вычисляются функции, способствующие коэффициенту дрейфа. Из профиля набора данных отслеживается статистическая информация о каждой функции. 

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас его нет, создайте бесплатную учетную запись, прежде чем начать. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

- Установленный пакет SDK службы "Машинное обучение Azure" для Python. Следуйте инструкциям из статьи об [установке пакета SDK службы "Машинное обучение Azure" для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), чтобы выполнить следующее:

    - создать среду Miniconda;
    - установить пакет SDK для Машинного обучения Azure для Python.

- [Рабочее пространство машинного обучения Azure](how-to-manage-workspace.md).

- [Файл конфигурации](how-to-configure-environment.md#workspace)рабочей области .

- Установите данные дрейфа SDK с помощью следующей команды:

    ```shell
    pip install azureml-datadrift
    ```

- Создайте [набор данных](how-to-create-register-datasets.md) из обучаемых данных модели.

- Укажите набор данных обучения при [регистрации](concept-model-management-and-deployment.md) модели. В следующем примере `datasets` показано использование параметра для указания набора обучающих данных:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Включить сбор данных модели](how-to-enable-data-collection.md) для сбора данных от развертывания модели АКС и подтверждения данных, собираемых в контейнере `modeldata` с каплями.

## <a name="configure-data-drift"></a>Настройка дрейфа данных
Чтобы настроить дрейф данных для эксперимента, импортируют зависимости, как видно из следующего примера Python. 

Этот пример демонстрирует настройку [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) объекта:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Отправить запуск DataDriftDetector

При `DataDriftDetector` настройке объекта можно отправить [пробег дрейфа данных](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) в заданную дату для модели. В рамках выполнения, включить DataDriftDetector оповещения, `drift_threshold` установив параметр. Если [datadrift_coefficient](#visualize-drift-metrics) выше данного, `drift_threshold`отправляется электронное письмо.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Визуализация метрик дрейфа

<a name="metrics"></a>

После отправки запуска DataDriftDetector вы сможете увидеть метрики дрейфа, сохраненные в каждой итерации запуска для задачи дрейфа данных:


|Метрика|Описание|
--|--|
wasserstein_distance|Статистическое расстояние, определяемое для одномерного численного распределения.|
energy_distance|Статистическое расстояние, определяемое для одномерного численного распределения.|
datadrift_coefficient|Рассчитано аналогично как коэффициент корреляции Матфея, но этот выход представляет собой реальное число от 0 до 1. В контексте дрейфа 0 указывает отсутствие дрейфа, а 1 — максимальный дрейф.|
datadrift_contribution|Характеристика особенностей функций, способствующих дрейфу.|

Существует несколько способов просмотра показателей дрейфа:

* `RunDetails`Используйте [виджет Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Используйте [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) функцию `datadrift` на любом объекте запуска.
* Просмотр метрик из раздела **Модели** рабочего пространства в [студии Машинного обучения Azure.](https://ml.azure.com)

Следующий пример Python демонстрирует, как построить соответствующие метрики дрейфа данных. Возвратные метрики можно использовать для создания пользовательских визуализаций:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Посмотреть дрейф данных, обнаруженный в Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Расписание сканирования дрейфа данных 

При включании обнаружения дрейфа данных dataDriftDetector работает на указанной, запланированной частоте. Если datadrift_coefficient достигает `drift_threshold`данного, отправляется электронное письмо с каждым запланированным запуском. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Конфигурация детектора дрейфа данных можно увидеть под **программой «Модели»** во вкладке **«Детали»** в рабочей области [в студии Машинного обучения Azure.](https://ml.azure.com)

[![Студия машинного обучения Azure Data Drift](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Просмотр результатов в студии машинного обучения Azure

Чтобы просмотреть результаты в рабочей области в [студии Машинного обучения Azure,](https://ml.azure.com)перейдите на страницу модели. На вкладке детали модели отображается конфигурация дрейфа данных. Вкладка **дрейфа данных** теперь доступна, визуализируя метрики дрейфа данных. 

[![Студия машинного обучения Azure Data Drift](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Получение предупреждений о дрейфе

Устанавливая порог предупреждения о дрейфовом коэффициенте и предоставляя адрес электронной почты, оповещение по электронной почте [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) автоматически отправляется всякий раз, когда коэффициент дрейфа превышает порог. 

Для настройки пользовательских оповещений и действий все показатели дрейфа данных хранятся в ресурсе [Application Insights,](how-to-enable-app-insights.md) созданном вместе с рабочим пространством Azure Machine Learning. Вы можете перейти по ссылке в оповещении по электронной почте в запрос "Исследования приложений".

![Данные Дрифт Email оповещения](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Переподготовка модели после дрейфа

Когда дрейф данных негативно влияет на производительность развернутой модели, пришло время переучить модель. Для этого приступаем к следующим шагам.

* Изучите собранные данные и подготовьте данные для обучения новой модели.
* Разделите его на данные поезда/теста.
* Поезд модели снова с помощью новых данных.
* Оцените производительность недавно созданной модели.
* Развертывание новой модели, если производительность лучше, чем производственная модель.

## <a name="next-steps"></a>Дальнейшие действия

* Полный пример использования дрейфа данных см. [Azure ML data drift notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb) Этот ноутбук Jupyter демонстрирует использование [набора открытых данных Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) для обучения модели прогнозированию погоды, развертывания ее в AKS и мониторинга дрейфа данных. 

* Обнаружение дрейфа данных с [помощью мониторов набора данных.](how-to-monitor-datasets.md)

* Мы были бы очень признательны за ваши вопросы, комментарии или предложения по мере продвижения дрейфа данных в сторону общей доступности. Используйте кнопку обратной связи продукта ниже! 
