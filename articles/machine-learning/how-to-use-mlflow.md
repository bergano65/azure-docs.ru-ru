---
title: Отслеживание экспериментов машинного обучения с помощью MLflow
titleSuffix: Azure Machine Learning
description: Настройте MLflow для Машинного обучения Azure, чтобы вести журнал метрик и артефактов из моделей машинного обучения, созданных в кластерах Databricks, локальной среде или среде виртуальных машин.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779124"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Отслеживание метрик моделей с помощью MLflow и Машинного обучения Azure (предварительная версия)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье показано, как включить универсальный код ресурса (URI) отслеживания MLflow и API ведения журнала (также называемые [отслеживанием MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)), чтобы связать Машинное обучение Azure с экспериментами MLflow. Это позволит осуществлять мониторинг и ведение журнала метрик и артефактов экспериментов в [рабочей области Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Если вы уже используете отслеживание MLflow для экспериментов, то эта рабочая область обеспечит централизованное, безопасное и масштабируемое расположение для хранения метрик и моделей обучения.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) — это библиотека с открытым кодом для управления жизненным циклом экспериментов машинного обучения. Отслеживание MLflow — это компонент MLflow, который осуществляет мониторинг и ведение журнала метрик и артефактов обучения независимо от среды эксперимента. Это может быть локальный компьютер, удаленный целевой объект вычислений, виртуальная машина или кластер Azure Databricks. 

На следующей схеме показано, как с помощью отслеживания MLflow можно следить за метриками выполнения эксперимента и сохранять артефакты модели в рабочей области Машинного обучения Azure.

![Схема взаимодействия MLflow с Машинным обучением Azure](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Сведения в этом документе предназначены главным образом для специалистов по обработке и анализу данных и разработчиков, желающих отслеживать процесс обучения модели. Если вы являетесь администратором, который заинтересован в наблюдении из Машинного обучения Azure за использованием ресурсов и событиями, такими как квоты, завершенные обучающие запуски или завершенные развертывания моделей, ознакомьтесь с разделом [Мониторинг Машинного обучения Azure](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Сравнение клиентов MLflow и Машинного обучения Azure

 В таблице ниже приведены общие сведения о различных клиентах, которые могут использовать Машинное обучение Azure, а также описываются их возможности.

 Отслеживание MLflow предлагает функции ведения журнала метрик и хранилища артефактов, которые также доступны только при использовании [пакета SDK Python для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Отслеживание&nbsp;MLflow <!--& Deployment--> | Пакет SDK Python для Машинного обучения Azure |  Интерфейс командной строки службы "Машинное обучение Azure" | Студия машинного обучения Azure.|
|---|---|---|---|---|
| Управление рабочей областью |   | ✓ | ✓ | ✓ |
| Использование хранилищ данных  |   | ✓ | ✓ | |
| Ведение журнала метрик      | ✓ | ✓ |   | |
| Передача артефактов | ✓ | ✓ |   | |
| Просмотр метрик     | ✓ | ✓ | ✓ | ✓ |
| Управление вычислительными ресурсами   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Предварительные требования

* [Установите MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Установите пакет SDK для Машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) на локальном компьютере. Этот пакет SDK обеспечивает подключение MLflow к вашей рабочей области.
* [Создайте рабочую область Машинного обучения Azure](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Отслеживание локальных выполнений

Интеграция отслеживания MLflow с Машинным обучением Azure позволяет хранить данные журнала метрик и артефактов локальных выполнений в рабочей области Машинного обучения Azure.

Установите пакет `azureml-mlflow`, чтобы использовать отслеживание MLflow с Машинным обучением Azure для экспериментов, выполняемых локально в Jupyter Notebook или редакторе кода.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Пространство имен azureml.contrib часто изменяется, так как мы работаем над улучшением службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.

Импортируйте классы `mlflow` и [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py), чтобы получить доступ к универсальному коду ресурса (URI) отслеживания MLflow и настроить рабочую область.

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

Удаленное выполнение позволяет обучать модели на более мощных компьютерах, таких как виртуальные машины с поддержкой GPU или кластеры Вычислительной среды Машинного обучения. Чтобы узнать больше о различных вариантах вычислений, ознакомьтесь с разделом [Настройка целевых объектов вычислений для обучения моделей](how-to-set-up-training-targets.md).

Настройте среду выполнения вычислений и обучения с помощью класса [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Включите пакеты PIP `mlflow` и `azureml-mlflow` в раздел среды [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py). Затем создайте [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py), указав удаленный объект вычислений в качестве целевого объекта вычислений.

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

При данной конфигурации выполнения вычислений и обучения для отправки выполнения используется метод `Experiment.submit('train.py')`. Этот метод автоматически задает URI отслеживания MLflow и направляет данные ведения журнала из MLflow в рабочую область.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Отслеживание выполнений Azure Databricks

Интеграция отслеживания MLflow с Машинным обучением Azure позволяет хранить данные журнала метрик и артефактов выполнений Azure Databricks в рабочей области Машинного обучения Azure.

Чтобы выполнять эксперименты MLflow с помощью Azure Databricks, сначала необходимо создать [рабочую область и кластер Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). В кластере необходимо установить библиотеку *azureml-mlflow* из PyPi, чтобы обеспечить доступ кластера к необходимым функциям и классам.

В нем можно импортировать записную книжку эксперимента, подключить ее к кластеру Azure Databricks и выполнить эксперимент. 

### <a name="install-libraries"></a>Установка библиотек

Чтобы установить библиотеки в кластере, перейдите на вкладку **Библиотеки** и щелкните **Установить новое**.

 ![Схема взаимодействия MLflow с Машинным обучением Azure](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

В поле **Пакет** введите azureml-mlflow и нажмите кнопку "Установить". Повторите этот шаг, чтобы установить другие дополнительные пакеты в кластер для своего эксперимента, если это требуется.

 ![Схема взаимодействия MLflow с Машинным обучением Azure](./media/how-to-use-mlflow/install-libraries.png)

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Связывание рабочих областей Azure Databricks и Машинного обучения Azure

На [портале Azure](https://ms.portal.azure.com) можно связать рабочую область Azure Databricks (ADB) с новой или существующей рабочей областью Машинного обучения Azure. Для этого перейдите в рабочую область ADB и нажмите кнопку **Link Azure Machine Learning workspace** (Связать с рабочей областью Машинного обучения Azure), расположенную в правом нижнем углу. Связывание рабочих областей позволяет отслеживать данные экспериментов в рабочей области Машинного обучения Azure. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Связывание отслеживания MLflow с рабочей областью

После создания экземпляра рабочей области задайте URI отслеживания MLflow. Это позволит связать отслеживание MLflow с рабочей областью Машинного обучения Azure. После связывания все эксперименты будут передаваться в управляемую службу отслеживания Машинного обучения Azure.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Непосредственная настройка отслеживания MLflow в записной книжке

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Импортируйте MLflow в сценарий обучения, чтобы использовать интерфейсы API ведения журнала MLflow, и начните вести журнал метрик выполнения. В следующем примере записывается метрика потерь эпохи. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Автоматическая настройка отслеживания MLflow

Вместо того, чтобы вручную задавать URI отслеживания в каждом последующем сеансе эксперимента записной книжки в кластерах, делайте это автоматически с помощью [сценария инициализации кластера отслеживания для Машинного обучения Azure](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

При правильной настройке вы сможете видеть данные отслеживания MLflow в REST API и всех клиентах Машинного обучения Azure, а также в Azure Databricks через пользовательский интерфейс MLflow или с помощью клиента MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Просмотр метрик и артефактов в рабочей области

Метрики и артефакты из журнала MLflow хранятся в рабочей области. Чтобы просмотреть их в любое время, перейдите в рабочую область и найдите эксперимент по имени в [Студии машинного обучения Azure](https://ml.azure.com).  Либо выполните приведенный ниже код. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Примеры записных книжек

В примере использования [MLflow с записными книжками Машинного обучения Azure](https://aka.ms/azureml-mlflow-examples) демонстрируются и поясняются основные понятия, представленные в этой статье.

## <a name="next-steps"></a>Дальнейшие действия
* [Управляйте своими моделями](concept-model-management-and-deployment.md).
* Отслеживайте рабочие модели для выявления [смещения данных](how-to-monitor-data-drift.md).
