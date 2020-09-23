---
title: Отслеживание экспериментов машинного обучения с помощью MLflow
titleSuffix: Azure Machine Learning
description: Настройте Млфлов с Машинное обучение Azure для записи метрик и артефактов из моделей машинного обучения и развертывания моделей машинного обучения в качестве веб-службы.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 116faae1bc0a93ce2007fcf809a8c96475289036
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897179"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Мониторинг метрик модели и развертывание моделей машинного обучения с помощью Млфлов и Машинное обучение Azure (Предварительная версия)

В этой статье показано, как включить универсальный код ресурса (URI) отслеживания MLflow и API ведения журнала (также называемые [отслеживанием MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)), чтобы связать Машинное обучение Azure с экспериментами MLflow. 

С Машинное обучение Azure собственной поддержкой для Млфлов можно,

+ Мониторинг и запись метрик и артефактов экспериментов в [рабочей области машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Если вы уже используете отслеживание MLflow для экспериментов, то эта рабочая область обеспечит централизованное, безопасное и масштабируемое расположение для хранения метрик и моделей обучения.

+ Отправка заданий обучения с помощью проектов Млфлов с поддержкой Машинное обучение Azure серверной части (Предварительная версия). Задания можно отправлять локально с отслеживанием Машинное обучение Azure или переносить запуски в облако, например с помощью [машинное обучение Azure вычислений](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute).

+ Отслеживание моделей и управление ими в Млфлов и в реестре моделей Машинное обучение Azure.

+ Развертывание экспериментов Млфлов в качестве веб-службы Машинное обучение Azure. Развертывание в качестве веб-службы позволяет применять функции мониторинга Машинное обучение Azure и распознавания отклонения данных в рабочих моделях. 

[MLflow](https://www.mlflow.org) — это библиотека с открытым кодом для управления жизненным циклом экспериментов машинного обучения. Отслеживание MLflow — это компонент MLflow, который осуществляет мониторинг и ведение журнала метрик и артефактов обучения независимо от среды эксперимента. Это может быть локальный компьютер, удаленный целевой объект вычислений, виртуальная машина или кластер Azure Databricks. 

>[!NOTE]
> Как библиотека с открытым исходным кодом, Млфлов часто меняются. Таким образом, функции, предоставляемые через Машинное обучение Azure и интеграцию с Млфлов, следует рассматривать как предварительную версию, а не полностью поддерживать корпорацией Майкрософт.

На следующей схеме показано, как с помощью отслеживания MLflow можно следить за метриками выполнения эксперимента и сохранять артефакты модели в рабочей области Машинного обучения Azure.

![Схема взаимодействия MLflow с Машинным обучением Azure](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Сведения в этом документе предназначены главным образом для специалистов по обработке и анализу данных и разработчиков, желающих отслеживать процесс обучения модели. Если вы являетесь администратором, который заинтересован в наблюдении из Машинного обучения Azure за использованием ресурсов и событиями, такими как квоты, завершенные обучающие запуски или завершенные развертывания моделей, ознакомьтесь с разделом [Мониторинг Машинного обучения Azure](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Сравнение клиентов MLflow и Машинного обучения Azure

 В таблице ниже приведены общие сведения о различных клиентах, которые могут использовать Машинное обучение Azure, а также описываются их возможности.

 Отслеживание MLflow предлагает функции ведения журнала метрик и хранилища артефактов, которые также доступны только при использовании [пакета SDK Python для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

| Функция | Развертывание & отслеживания Млфлов | Пакет SDK Python для Машинного обучения Azure |  Интерфейс командной строки службы "Машинное обучение Azure" | Студия машинного обучения Azure.|
|---|---|---|---|---|
| Управление рабочей областью |   | ✓ | ✓ | ✓ |
| Использование хранилищ данных  |   | ✓ | ✓ | |
| Ведение журнала метрик      | ✓ | ✓ |   | |
| Передача артефактов | ✓ | ✓ |   | |
| Просмотр метрик     | ✓ | ✓ | ✓ | ✓ |
| Управление вычислительными ресурсами   |   | ✓ | ✓ | ✓ |
| Развертывание моделей    | ✓ | ✓ | ✓ | ✓ |
|Мониторинг производительности модели||✓|  |   |
| Определение смещения данных |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Предварительные требования

* [Установите MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Установите пакет SDK для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) на локальном компьютере. Этот пакет SDK обеспечивает подключение MLflow к вашей рабочей области.
* [Создайте рабочую область Машинного обучения Azure](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Отслеживание локальных выполнений

Интеграция отслеживания MLflow с Машинным обучением Azure позволяет хранить данные журнала метрик и артефактов локальных выполнений в рабочей области Машинного обучения Azure.

Установите пакет `azureml-mlflow`, чтобы использовать отслеживание MLflow с Машинным обучением Azure для экспериментов, выполняемых локально в Jupyter Notebook или редакторе кода.

```shell
pip install azureml-mlflow
```

Импортируйте классы `mlflow` и [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py&preserve-view=true), чтобы получить доступ к универсальному коду ресурса (URI) отслеживания MLflow и настроить рабочую область.

В следующем коде метод `get_mlflow_tracking_uri()` присваивает рабочему пространству `ws` уникальный адрес URI отслеживания, а метод `set_tracking_uri()` указывает этот адрес в качестве URI отслеживания MLflow.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>URI отслеживания действителен до часа. Если вы перезапустили сценарий после некоторого времени простоя, используйте API get_mlflow_tracking_uri, чтобы получить новый универсальный код ресурса (URI).

Задайте имя эксперимента MLflow с помощью `set_experiment()` и запустите обучение с помощью `start_run()`. Затем используйте `log_metric()`, чтобы активировать API ведения журнала MLflow и начать запись в журнал метрик выполнения обучения.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Отслеживание удаленных выполнений

Интеграция отслеживания MLflow с Машинным обучением Azure позволяет хранить данные журнала метрик и артефактов удаленных выполнений в рабочей области Машинного обучения Azure.

Удаленное выполнение позволяет обучать модели на более мощных компьютерах, таких как виртуальные машины с поддержкой GPU или кластеры Вычислительной среды Машинного обучения. Дополнительные сведения о различных параметрах вычислений см. в статье [использование целевых объектов вычислений для обучения модели](how-to-set-up-training-targets.md) .

Настройте среду выполнения вычислений и обучения с помощью класса [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true). Включите пакеты PIP `mlflow` и `azureml-mlflow` в раздел среды [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true). Затем создайте [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true), указав удаленный объект вычислений в качестве целевого объекта вычислений.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Импортируйте `mlflow` в сценарий обучения, чтобы использовать интерфейсы API ведения журнала MLflow, и начните вести журнал метрик выполнения.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

При данной конфигурации выполнения вычислений и обучения для отправки выполнения используется метод `Experiment.submit()`. Этот метод автоматически задает URI отслеживания MLflow и направляет данные ведения журнала из MLflow в рабочую область.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Обучение с помощью проектов Млфлов

[Проекты млфлов](https://mlflow.org/docs/latest/projects.html) позволяют организовать и описать код, чтобы позволить другим специалистам по обработке и анализу данных (или автоматизированным средствам) запустить его. Проекты Млфлов с Машинное обучение Azure позволяют отслеживанить и управлять обучающими запусками в рабочей области. 

В этом примере показано, как отправлять проекты Млфлов локально с отслеживанием Машинное обучение Azure.

Установите `azureml-mlflow` пакет, чтобы использовать отслеживание млфлов с машинное обучение Azure в локальных экспериментах. Эксперименты могут выполняться через записную книжку Jupyter или редактор кода.

```shell
pip install azureml-mlflow
```

Импортируйте классы `mlflow` и [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py), чтобы получить доступ к универсальному коду ресурса (URI) отслеживания MLflow и настроить рабочую область.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Задайте имя эксперимента MLflow с помощью `set_experiment()` и запустите обучение с помощью `start_run()`. Затем используйте, `log_metric()` чтобы активировать API ведения журнала млфлов и начать запись в журнал метрик выполнения обучения.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Создайте объект конфигурации серверной части для хранения необходимой информации для интеграции, такой как, целевой объект вычислений и используемый тип управляемой среды.

```python
backend_config = {"USE_CONDA": False}
```
Добавьте `azureml-mlflow` пакет в качестве зависимости PIP к файлу конфигурации среды, чтобы отслеживанию метрик и ключевых артефактов в рабочей области. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Отправьте локальный запуск и убедитесь, что задан параметр `backend = "azureml" ` . С помощью этого параметра можно отправлять запуски локально и получать дополнительную поддержку автоматического отслеживания вывода, файлов журналов, моментальных снимков и ошибок печати в рабочей области. 

Просматривайте свои запуски и метрики в [машинное обучение Azure Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="track-azure-databricks-runs"></a>Отслеживание выполнений Azure Databricks

Отслеживание Млфлов с помощью Машинное обучение Azure позволяет хранить зарегистрированные метрики и артефакты из Azure Databricks выполняется во всех трех следующих областях: 

* Рабочая область службы "Машинное обучение Azure"
* Создайте рабочую область Azure Databricks.
* MLflow

Чтобы выполнять эксперименты MLflow с помощью Azure Databricks, сначала необходимо создать [рабочую область и кластер Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). В кластере установите библиотеку *azureml-млфлов* из PyPi, чтобы предоставить кластеру доступ к необходимым функциям и классам.

> [!NOTE]
> `azureml.core`Пакет включает `azureml-mlflow` . Если вы уже установили `azureml.core` , `azureml-mlflow` Шаг установки можно пропустить. 

В нем можно импортировать записную книжку эксперимента, подключить ее к кластеру Azure Databricks и выполнить эксперимент. 

### <a name="install-libraries"></a>Установка библиотек

Чтобы установить библиотеки в кластере, перейдите на вкладку **Библиотеки** и щелкните **Установить новое**.

 ![млфлов с модулями Azure](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

В поле **Пакет** введите azureml-mlflow и нажмите кнопку "Установить". Повторите этот шаг, чтобы установить другие дополнительные пакеты в кластер для своего эксперимента, если это требуется.

 ![Библиотека млфлов для установки базы данных Azure](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Настройка записной книжки и рабочей области

После настройки кластера импортируйте записную книжку эксперимента, откройте ее и подключите к ней кластер.

В записной книжке эксперимента должен быть приведенный ниже код. Этот код получает сведения о подписке Azure для создания экземпляра рабочей области. В этом коде предполагается, что у вас уже есть группа ресурсов и рабочая область Машинного обучения Azure. В противном случае можно [создать их](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Связывание рабочих областей Azure Databricks и Машинного обучения Azure

На [портале Azure](https://ms.portal.azure.com) можно связать рабочую область Azure Databricks (ADB) с новой или существующей рабочей областью Машинного обучения Azure. Для этого перейдите в рабочую область ADB и нажмите кнопку **Link Azure Machine Learning workspace** (Связать с рабочей областью Машинного обучения Azure), расположенную в правом нижнем углу. Связывание рабочих областей позволяет отслеживать данные экспериментов в рабочей области Машинного обучения Azure. 

### <a name="mlflow-tracking-in-your-workspaces"></a>Отслеживание Млфлов в рабочих областях

После создания экземпляра рабочей области отслеживание Млфлов автоматически задается для отслеживания во всех следующих местах:

* Связанная Рабочая область Машинное обучение Azure.
* Исходная Рабочая область ADB. 
* Млфлов. 

Все ваши эксперименты будут находиться в управляемой службе отслеживания Машинное обучение Azure.

#### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Настройка отслеживания Млфлов для отслеживания только в рабочей области Машинное обучение Azure

Если вы предпочитаете управлять отслеживаемыми экспериментами в централизованном расположении, можно настроить отслеживание Млфлов для отслеживания **только** в рабочей области машинное обучение Azure. 


```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Импортируйте `mlflow` в сценарий обучения, чтобы использовать интерфейсы API ведения журнала MLflow, и начните вести журнал метрик выполнения. В следующем примере записывается метрика потерь эпохи. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Просмотр метрик и артефактов в рабочей области

Метрики и артефакты из журнала MLflow хранятся в рабочей области. Чтобы просмотреть их в любое время, перейдите в рабочую область и найдите эксперимент по имени в [Студии машинного обучения Azure](https://ml.azure.com).  Либо выполните приведенный ниже код. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>Модели (управление) 

Зарегистрируйте и отследите модели с помощью [реестра машинное обучение Azure Model](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) , который поддерживает реестр модели млфлов. Машинное обучение Azure модели согласовываются со схемой модели Млфлов, что упрощает экспорт и импорт этих моделей в разных рабочих процессах. Млфлов связанные метаданные, такие как, ID запуска, также помечаются зарегистрированной моделью для отслеживания. Пользователи могут отправлять обучающие запуски, регистрировать и развертывать модели, созданные при выполнении Млфлов. 

Если вы хотите развернуть и зарегистрировать готовую к работе модель в течение одного шага, см. статью [развертывание и регистрация моделей млфлов](#deploy-and-register-mlflow-models).

Чтобы зарегистрировать и просмотреть модель из выполнения, выполните следующие действия.

1. После завершения выполнения вызовите `register_model()` метод.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Просмотрите зарегистрированную модель в рабочей области с помощью [машинное обучение Azure Studio](overview-what-is-machine-learning-studio.md).

    В следующем примере Зарегистрированная модель `my-model` содержит млфлов отслеживание метаданных. 

    ![Registered-млфлов-Model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Перейдите на вкладку **артефакты** , чтобы просмотреть все файлы модели, которые совпадают со схемой модели млфлов (conda. YAML, млмодел, Model. PKL).

    ![модель-схема](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Выберите Млмодел, чтобы просмотреть файл Млмодел, созданный при выполнении.

    ![Млмодел-Schema](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>Развертывание и регистрация моделей Млфлов 

Развертывание экспериментов Млфлов в качестве веб-службы Машинное обучение Azure позволяет использовать и применять функции Машинное обучение Azure управления моделями и отклонения данных к рабочим моделям.

Для этого необходимо

1. регистрация модели;
1. Определите конфигурацию развертывания, которая будет использоваться для вашего сценария.

    1. [Экземпляр контейнера Azure (ACI)](#deploy-to-aci) подходит для быстрой разработки и тестирования.
    1. [Служба Azure Kubernetes Service (AKS)](#deploy-to-aks) подходит для масштабируемых рабочих развертываний.

На следующей схеме показано, что с помощью API развертывания Млфлов можно развернуть существующие модели Млфлов в качестве веб-службы Машинное обучение Azure, несмотря на их платформы — PyTorch, Tensorflow, scikit-учиться, ONNX и т. д., а также управлять производственными моделями в рабочей области.

![ Развертывание моделей млфлов с помощью службы "машинное обучение Azure"](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>Развертывание в ACI

Настройте конфигурацию развертывания с помощью метода [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Кроме того, можно добавить теги и описания, которые помогут отследить веб-службу.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Затем зарегистрируйте и разверните модель за один шаг с помощью метода [развертывания](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) пакета SDK машинное обучение Azure. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>Развертывание в AKS

Чтобы выполнить развертывание в AKS, сначала создайте кластер AKS. Создайте кластер AKS с помощью метода [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Создание нового кластера может занять 20-25 минут.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Настройте конфигурацию развертывания с помощью метода [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Кроме того, можно добавить теги и описания, которые помогут отследить веб-службу.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Затем зарегистрируйте и разверните модель в одном шаге с помощью Машинное обучение Azure пакета SDK [Deploy ()] (затем зарегистрируйте и разверните модель с помощью метода [развертывания](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) пакета SDK машинное обучение Azure. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Развертывание службы может занять несколько минут.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не планируете использовать зарегистрированные метрики и артефакты в рабочей области, возможность удалить их по отдельности в данный момент недоступна. Вместо этого удалите группу ресурсов, содержащую учетную запись хранения и рабочую область, чтобы не взиматься плата.

1. На портале Azure выберите **Группы ресурсов** в левой части окна.

   ![Удаление ресурсов на портале Azure](./media/how-to-use-mlflow/delete-resources.png)

1. В списке выберите созданную группу ресурсов.

1. Выберите **Удалить группу ресурсов**.

1. Введите имя группы ресурсов. Теперь щелкните **Удалить**.

## <a name="example-notebooks"></a>Примеры записных книжек

В примере использования [MLflow с записными книжками Машинного обучения Azure](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) демонстрируются и поясняются основные понятия, представленные в этой статье.

## <a name="next-steps"></a>Дальнейшие действия

* [Управляйте своими моделями](concept-model-management-and-deployment.md).
* Отслеживайте рабочие модели для выявления [смещения данных](how-to-monitor-data-drift.md).
