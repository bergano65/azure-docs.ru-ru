---
title: Настройка функции ведения журналов для Службы машинного обучения
titleSuffix: Azure Machine Learning service
description: Узнайте, как настроить ведение журнала в Службе машинного обучения Azure с использованием стандартного пакета ведения журнала для Python и с помощью собственных функций пакета SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 02/20/2019
ms.openlocfilehash: 08e83cdcadabdcf7234d0bbd0fb7e6d103c8369c
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447537"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Настройка функции ведения журналов для Службы машинного обучения

Пакет SDK Python для Машинного обучения Azure позволяет настроить ведение журнала, используя стандартный пакет ведения журнала для Python или собственные функции пакета SDK. Можно вести журналы как локально, так и в рабочей области на портале. Журналы предоставляют разработчикам сведения о состоянии приложения в реальном времени и могут помочь в диагностике ошибок или предупреждений. Из этой статьи вы узнаете:

> [!div class="checklist"]
> * как включить ведение журнала для обучаемых моделей и целевых объектов вычислений;
> * Создание образа.
> * как включить ведение журнала для развернутых моделей:
> * как задать параметры `logging` в Python.

Выполните инструкции в [этом руководстве](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), чтобы установить пакет SDK и [начать работу](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python), создав рабочее пространство на портале Azure.

## <a name="training-models-and-compute-target-logging"></a>Ведение журнала для обучаемых моделей и целевых объектов вычислений

Есть несколько способов настроить ведение журнала для процесса обучения моделей. Представленные здесь примеры иллюстрируют несколько распространенных шаблонов разработки. Вы можете без труда настроить сохранение данных об обучении в облачную рабочую область, используя функцию `start_logging` для класса `Experiment`.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

В справочной [документации](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) для класса `Run` вы найдете сведения о дополнительных функциях ведения журнала.

Чтобы сохранять сведения о состоянии приложения в ходе обучения в локальный журнал, используйте параметр `show_output`. Подробное ведение журналов позволяет получить сведения об обучении, а также об удаленных ресурсах и целевых объектах вычислений. С помощью приведенного ниже кода можно настроить сохранение данных в журнал при отправке эксперимента.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Этот же параметр вы можете указать в функции `wait_for_completion` для финального выполнения.

```python
run.wait_for_completion(show_output=True)
```

Кроме того, пакет SDK поддерживает стандартный пакет ведения журнала Python для некоторых сценариев обучения. В следующем примере для объекта `AutoMLConfig` включается уровень ведения журнала `INFO`.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task = 'regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Кроме того, вы можете использовать параметр `show_output` при создании постоянного целевого объекта вычислений. Укажите этот параметр в функции `wait_for_completion`, чтобы настроить ведение журнала во время создания целевого объекта вычислений.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Ведение журнала при создании образа

Настроив ведение журнала при создании образа, вы сможете получить сведения обо всех ошибках, возникающих во время сборки. Задайте параметр `show_output` для функции `wait_for_deployment()`.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                            image=image,
                                            name="example-image",
                                            workspace=ws)

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Ведение журнала для развернутых моделей

Чтобы получить журналы из ранее развернутой веб-службы, загрузите службу и вызовите функцию `get_logs()`. Эти журналы могут содержать подробные сведения об ошибках, возникающих во время развертывания.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Кроме того, вы можете сохранять пользовательскую трассировку стека веб-службы, включив Application Insights. Это позволит отслеживать время запроса и отклика, частоту сбоев и исключения. Вызовите функцию `update()` для существующей веб-службы, чтобы включить Application Insights.

```python
service.update(enable_app_insights=True)
```

В [этом руководстве](how-to-enable-app-insights.md#enable-and-disable-in-the-portal) вы найдете дополнительные сведения о работе с Application Insights на портале Azure.

## <a name="python-native-logging-settings"></a>Параметры собственного ведения журнала Python

Некоторые журналы, создаваемые пакетом SDK, могут содержать сообщения об ошибках с предложением установить уровень ведения журнала DEBUG. Чтобы изменить уровень ведения журнала, добавьте в скрипт приведенный ниже код.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```