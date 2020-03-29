---
title: Отладить и устранить неполадки конвейеров машинного обучения в Application Insights
titleSuffix: Azure Machine Learning
description: Добавьте журналирование в конвейеры обучения и пакетного скоринга и просмотреданных результатов в Application Insights.
services: machine-learning
author: aburek
ms.author: anrode
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 85dcd9ef98deb2ea0117f2db280e49c4a57bf00f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776304"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Отладить и устранить неполадки конвейеров машинного обучения в Application Insights
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Библиотека python [OpenCensus](https://opencensus.io/quickstart/python/) может использоваться для маршрутизании журналов в Application Insights из ваших скриптов. Агрегирование журналов из конвейера работает в одном месте позволяет создавать запросы и диагностировать проблемы. Использование Application Insights позволит отслеживать журналы с течением времени и сравнивать журналы конвейеров между пробегами.

Наличие журналов в некогда емместе обеспечит историю исключений и сообщений об ошибках. Поскольку Application Insights интегрируется с Azure Alerts, можно также создавать оповещения на основе запросов Application Insights.

## <a name="prerequisites"></a>Предварительные требования

* Выполните последующие действия по созданию рабочего пространства [Azure Machine Learning](./how-to-manage-workspace.md) и [созданию первого конвейера](./how-to-create-your-first-pipeline.md)
* [Настройте среду разработки](./how-to-configure-environment.md) для установки пакета SDK для Машинного обучения Azure.
* Установите пакет [экспортеров OpenCensus Azure Monitor](https://pypi.org/project/opencensus-ext-azure/) на месте:
  ```python
  pip install opencensus-ext-azure
  ```
* Создайте [экземпляр Application Insights](../azure-monitor/app/opencensus-python.md) (этот документ также содержит информацию о получении строки соединения для ресурса)

## <a name="getting-started"></a>Начало работы

Данный раздел представляет собой введение, специфичное для использования OpenCensus из конвейера машинного обучения Azure. Подробную информацию о [экспортерах OpenCensus Azure Monitor можно](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) узнать на примере OpenCensus

Добавьте PythonScriptStep в свой конвейер Azure ML. Наверстируйте [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) с помощью зависимости от opencensus-ext-azure. Настройка `APPLICATIONINSIGHTS_CONNECTION_STRING` переменной среды.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Создайте файл с именем `sample_step.py`. Импортируйте класс AzureLogHandler для маршрутизаторов в Application Insights. Также необходимо импортировать библиотеку Python Logging.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Затем добавьте AzureLogHandler в регистратор python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Регистрация с пользовательскими размерами
 
По умолчанию журналы, переадресованные в Application Insights, не будут иметь достаточного контекста для отслеживания выполнения или эксперимента. Для того чтобы журналы были пригодны для диагностики проблем, необходимы дополнительные поля. 

Чтобы добавить эти поля, пользовательские размеры могут быть добавлены, чтобы обеспечить контекст для сообщения журнала. Одним из примеров является, когда кто-то хочет просматривать журналы на нескольких этапах в одном и том же запуске конвейера.

Пользовательские размеры составляют словарь ключевых значений (хранится как строка, строка) пар. Словарь затем отправляется в Application Insights и отображается в качестве столбца в результатах запроса. Его индивидуальные размеры могут быть использованы в качестве [параметров запроса.](#additional-helpful-queries)

### <a name="helpful-context-to-include"></a>Полезный контекст для включения

| Поле                          | Рассуждения/Пример                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Могут ли журналы запросов для тех, у кого parent_run_id, чтобы видеть журналы с течением времени для всех шагов, вместо того, чтобы погрузиться в каждый отдельный шаг                                        |
| step_id                        | Могут ли журналы запросов для тех, у кого step_id, чтобы увидеть, где возникла проблема с узким охватом только для индивидуального шага                                                        |
| step_name                      | Можно заставить журналы запросов видеть производительность шага с течением времени. Также помогает найти step_id для недавних пробежек без погружения в портал UI                                          |
| experiment_name                | Можно задать запрос в журналах, чтобы увидеть производительность эксперимента с течением времени. Также помогает найти parent_run_id или step_id для недавних пробежек без погружения в uI портала                   |
| run_url                 | Может предоставить ссылку непосредственно на бег для расследования. |

**Другие полезные поля**

Эти поля могут потребовать дополнительных приборов кода и не предусмотрены контекстом выполнения.

| Поле                   | Рассуждения/Пример                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | При развертывании CI/CD это поле может соотнести журналы с код-версией, которая предоставила логику шага и конвейера. Эта ссылка может помочь в дальнейшем диагностировать проблемы или идентифицировать модели с определенными чертами (значения журнала/метрики) |
| run_type                       | Может различать различные типы моделей, или обучение против скоринга работает |

### <a name="creating-a-custom-dimensions-dictionary"></a>Создание словаря пользовательских измерений

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python регистрации соображений

OpenCensus AzureLogHandler используется для маршрутизации журналов Python в Application Insights. В результате следует учитывать нюансы регистрации Python. При создании регистратора он имеет уровень журнала по умолчанию и будет отображать журналы, превышаюющие или равные этому уровню. Хорошей ссылкой для использования Python лесозаготовительные функции [является журнал Cookbook](https://docs.python.org/3/howto/logging-cookbook.html).

Переменная `APPLICATIONINSIGHTS_CONNECTION_STRING` среды необходима для библиотеки OpenCensus. Мы рекомендуем установить эту переменную среды вместо того, чтобы передавать ее в качестве параметра конвейера, чтобы избежать прохождения по струнам простого соединения.

## <a name="querying-logs-in-application-insights"></a>Запрос журналов в приложениях Исследования

В журналах, направляемых в Application Insights, будут отображаться под "следами" или "исключениями". Обязательно отрегулируйте свое временное окно, чтобы включить ваш запуск конвейера.

![Результат запроса на анализ приложений](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

В результате в Application Insights будут отображаться сообщение и уровень входа, траектория файла и номер строки кода. Он также будет показывать любые пользовательские размеры включены. На этом изображении словарь customDimensions показывает пары ключей/значений из предыдущего [образца кода.](#creating-a-custom-dimensions-dictionary)

### <a name="additional-helpful-queries"></a>Дополнительные полезные запросы

Некоторые из приведенных ниже запросов используют 'customDimensions.Level'. Эти уровни серьезности соответствуют уровню, с тем, с чем изначально был отправлен журнал Python. Для получения дополнительной информации о запросе смотрите [запросы журнала Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)

| Вариант использования                                                               | Запрос                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Результаты журнала для конкретного пользовательского измерения, например "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Результаты регистрации для всех учебных запусков за последние 7 дней                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Результаты журнала с серьезностьюLevel Ошибка за последние 7 дней              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Подсчет результатов журнала с серьезностьюLevel Ошибка за последние 7 дней     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Next Steps

После того, как журналы есть в экземпляре Application Insights, их можно использовать для [установки предупреждений Azure Monitor](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) на основе результатов запроса.

Вы также можете добавить результаты запросов в [панель мониторинга Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) для получения дополнительных сведений.