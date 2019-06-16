---
title: Как использовать MLflow со службой машинного обучения Azure
titleSuffix: Azure Machine Learning service
description: Узнайте, как для ведения журнала метрик и артефактов с помощью библиотеки MLflow для службы машинного обучения Azure
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 934e9b705ab5f399d29f24c915b4c60a3b06138b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082577"
---
# <a name="how-to-use-mlflow-with-azure-machine-learning-service-preview"></a>Как использовать MLflow со службой машинного обучения Azure (Предварительная версия)

В этой статье демонстрируется использование ведения журнала API и отслеживания URI MLflow в совокупности называются MLflow отслеживания, со службой машинного обучения Azure для отслеживания и регистрации метрики эксперимента и артефактов в вашей [машинного обучения Azure рабочей области службы](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace). Если вы уже используете MLflow отслеживания для собственных экспериментов, рабочая область предоставляет централизованное, безопасная и масштабируемая расположение для хранения метрик обучения и модели.

[MLflow](https://www.mlflow.org) является библиотекой с открытым исходным кодом для управления жизненным циклом эксперименты машинного обучения. [Отслеживание MLFlow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) входит в состав MLflow, которое регистрирует и отслеживает обучения запустить метрики и артефакты модели ли эксперименты запускаются локально, на виртуальной машине или на удаленном вычислительный кластер.
![mlflow со схемой обучения машины azure](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-service-clients"></a>Сравнить клиентов службы MLflow и машинного обучения Azure

 Ниже таблице представлены сведения о различных клиентских компьютеров, которые могут использовать службы машинного обучения Azure и их возможности соответствующей функции.

 Отслеживание MLflow предоставляет ведение журнала с метриками и артефакта хранилища, которые доступны только в противном случае через [пакета SDK Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | MLflow отслеживания | Машинное обучение Azure <br> Пакет SDK для Python |  Машинное обучение Azure <br> Интерфейс командной строки | Портал Azure|
|-|-|-|-|-|
| Управление рабочей областью |   | ✓ |  ✓ | ✓  |
| Использование хранилища данных  |   | ✓ |  ✓ |    |
| Журнал показателей      | ✓ | ✓ |    |    |
| Отправить артефакты | ✓ | ✓ |    |    |
| Просмотр метрик     | ✓ | ✓ | ✓  | ✓ |
| Управление вычислительными ресурсами   |   | ✓ | ✓  | ✓ |
| Развертывание моделей    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>Технические условия

* [Установите MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Установка пакета SDK Azure Machine Learning Python на локальном компьютере и создание рабочей области Azure машинного обучения](setup-create-workspace.md#sdk). Пакет SDK предоставляет возможность подключения к MLflow для доступа к рабочей области.

## <a name="track-local-runs"></a>Отслеживать локального выполнения

Установка `azureml-contrib-run` пакета для использования MLflow отслеживания с помощью машинного обучения Azure на эксперименты локально запустим в записной книжке Jupyter или редактор кода.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Пространство имен azureml.contrib часто изменяются, так как мы работаем для улучшения службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.

Импорт `mlflow` и [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) классы для доступа к MLflow отслеживания URI и настройка рабочей области.

В следующем коде `get_mlflow_tracking_uri()` метод назначает уникальный отслеживания URI-адрес в рабочую область, `ws`, и `set_tracking_uri()` указывает MLflow, отслеживания URI к этому адресу.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>URI отслеживания является допустимым до одного часа или меньше. Если перезапустить сценарий через некоторое время простоя, используйте get_mlflow_tracking_uri API, чтобы получить новый URI.

Задайте имя эксперимента MLflow с `set_experiment()` и начать учиться работы `start_run()`. Затем с помощью `log_metric()` активировать ведение журнала MLflow API и начинает регистрировать действия, обучение запустить метрики.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Отслеживание удаленных запусков

Удаленном запуске позволяют обучения моделей на более мощные вычислительные ресурсы, такие как виртуальные машины с поддержкой процессоров GPU или кластеры вычислений для машинного обучения. См. в разделе [настроить целевые объекты вычисления для обучения модели](how-to-set-up-training-targets.md) Дополнительные сведения о разных вариантах вычислений.

Настройка вычислительных ресурсов и обучающих запусках среды с [ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) класса. Включить `mlflow` и `azure-contrib-run` pip пакетов в среде [ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) раздел. Затем постройте [ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) с вашей удаленных вычислений в качестве целевого объекта вычислений.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

В скрипте обучения импортировать `mlflow` MLflow, API ведения журнала и начать ведение журнала выполнения метрики.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

С помощью этого вычислительные ресурсы и обучающих запусках конфигурации, использовать `Experiment.submit("train.py")` метод для отправки запуска. Это автоматически задает MLflow, отслеживания URI и направляет ведение журнала MLflow в рабочую область.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Просмотр метрик и артефакты в вашей рабочей области

Метрики и артефакты из MLflow ведения журнала хранятся в рабочей области на [портала Azure](https://portal.azure.com). Для их просмотра любое время, перейдите в рабочую область и найдите эксперимента по имени.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не планируете использовать метрики журнала и артефакты в вашей рабочей области, удалять их по отдельности сейчас недоступна. Вместо этого удалите группу ресурсов, который содержит учетную запись хранения и рабочей области, чтобы вас не взималась плата за:

1. На портале Azure выберите **Группы ресурсов** в левой части окна.

   ![Удаление ресурсов на портале Azure](media/how-to-use-mlflow/delete-resources.png)

1. В списке выберите созданную группу ресурсов.

1. Выберите **Удалить группу ресурсов**.

1. Введите имя группы ресурсов. Теперь щелкните **Удалить**.

## <a name="example-notebooks"></a>Примеры записных книжек

[MLflow с записными книжками машинного Обучения Azure](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/mlflow) понятия в этой статье.

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание модели](how-to-deploy-and-where.md).