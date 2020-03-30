---
title: Отслеживание MLflow для экспериментов ML
titleSuffix: Azure Machine Learning
description: Настройка MLflow с помощью Azure Machine Learning для регистрации метрик и артефактов из моделей ML, созданных в кластерах Databricks, локальной среде или среде VM.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983704"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Метрики трековых моделей с помощью MLflow и машинного обучения Azure (предварительный просмотр)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье показано, как включить API отслеживания MLflow и журналирование, известное как [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)для подключения экспериментов MLflow и машинного обучения Azure. Это позволяет отслеживать и регистрировать метрики и артефакты в [рабочем пространстве Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces) Если вы уже используете Отслеживание MLflow для экспериментов, рабочее пространство обеспечивает централизованное, безопасное и масштабируемое местоположение для хранения обучающих метрик и моделей.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) — это библиотека с открытым исходным кодом для управления жизненным циклом экспериментов по машинного обучению. ОТСЛЕЖИВАНИе MLFlow является компонентом MLflow, который регистрирует и отслеживает ваши метрики обучения и артефакты моделей, независимо от среды эксперимента - локально на вашем компьютере, на удаленной вычислительной цели, виртуальной машине или кластере Azure Databricks. 

Следующая диаграмма иллюстрирует, что с помощью Отслеживания MLflow вы отслеживаете метрики выполнения эксперимента и храните артефакты моделей в рабочем пространстве Azure Machine Learning.

![mlflow с лазурной диаграммой машинного обучения](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Информация в этом документе в первую очередь предназначена для ученых и разработчиков данных, которые хотят контролировать процесс обучения модели. Если вы являетесь администратором, заинтересованным в мониторинге использования ресурсов и событий из Azure Machine Learning, таких как квоты, завершенные обучающие заезды или завершенные развертывания моделей, [см.](monitor-azure-machine-learning.md)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Сравните клиентов MLflow и Azure Machine Learning

 В приведенной ниже таблице кратко излагаются различные клиенты, которые могут использовать машинное обучение Azure, и их соответствующие функциональные возможности.

 Отслеживание MLflow предлагает функции регистрации метрики и хранения артефактов, которые доступны только в противном случае через [SDK Azure Machine Learning Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


| | Отслеживание&nbsp;MLflow <!--& Deployment--> | Лазурное машинное обучение Python SDK |  Интерфейс командной строки службы "Машинное обучение Azure" | Студия машинного обучения Azure.|
|---|---|---|---|---|
| Управление рабочей областью |   | ✓ | ✓ | ✓ |
| Использование хранилив данных  |   | ✓ | ✓ | |
| Метрики журнала      | ✓ | ✓ |   | |
| Загрузка артефактов | ✓ | ✓ |   | |
| Просмотр метрик     | ✓ | ✓ | ✓ | ✓ |
| Управление вычислительными ресурсами   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Предварительные требования

* [Установите MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Установите SDK машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) на локальном компьютере, SDK обеспечивает подключение MLflow для доступа к рабочему пространству.
* [Создайте рабочее пространство для обучения машин Azure.](how-to-manage-workspace.md)

## <a name="track-local-runs"></a>Отслеживайте локальные трассы

Отслеживание MLflow с помощью Azure Machine Learning позволяет хранить зарегистрированные метрики и артефакты из локального хранилища в рабочем пространстве Azure Machine Learning.

Установите `azureml-mlflow` пакет для использования Отслеживания MLflow с помощью машинного обучения Azure на своих экспериментах, локально запущенных в блокноте Jupyter или редакторе кода.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Пространство имен azureml.contrib часто меняется по мере улучшения сервиса. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.

Импортируйте `mlflow` [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) и классы для доступа к URI отслеживания MLflow и настройки рабочего пространства.

В следующем коде `get_mlflow_tracking_uri()` метод назначает уникальный адрес отслеживания URI `ws`рабочей области и `set_tracking_uri()` указывает на этот адрес mLflow, отслеживающий URI.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Отслеживание URI действителен до часа или меньше. Если вы перезапустите сценарий после некоторого простоя, используйте API get_mlflow_tracking_uri, чтобы получить новый URI.

Установите название эксперимента MLflow и `set_experiment()` начните тренировочный забег с. `start_run()` Затем `log_metric()` используйте для активации API регистрации MLflow и начала регистрации метрик учебного запуска.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Отслеживание удаленных запусков

Отслеживание MLflow с помощью Azure Machine Learning позволяет хранить зарегистрированные метрики и артефакты из удаленных компьютеров в рабочем пространстве Azure Machine Learning.

Дистанционные запуски позволяют обучать модели более мощным вычислениям, таким как виртуальные машины с поддержкой графического процессора или кластеры Machine Learning Compute. Смотрите [Настройка вычислительных целей для обучения модели,](how-to-set-up-training-targets.md) чтобы узнать о различных вариантах вычислений.

Нанастройка среды вычислений и [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) обучения с помощью класса. Включите `mlflow` и `azureml-mlflow` пипс [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) пакеты в разделе среды. Затем [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) постройте с помощью удаленного вычисления в качестве вычислительной цели.

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

В скрипте обучения импортируйте `mlflow` для использования AIS для регистрации MLflow и начните журналировать метрики выполнения.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

При такой конфигурации выполнения вычислений `Experiment.submit('train.py')` и обучения используйте метод для отправки запуска. Этот метод автоматически устанавливает отслеживание MLflow URI и направляет журнал из MLflow в рабочее пространство.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Отслеживайте запуски Данных Azure

Отслеживание MLflow с помощью Azure Machine Learning позволяет хранить зарегистрированные метрики и артефакты из ваших базисных databricks в рабочем пространстве Azure Machine Learning.

Для запуска экспериментов mlflow с Azure Databricks необходимо сначала создать [рабочее пространство и кластер Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) В вашем кластере обязательно установите библиотеку *лазурно-млпотоков* из PyPi, чтобы обеспечить кластер доступ к необходимым функциям и классам.

Отсюда импортируйте свой блокнот эксперимента, прикрепите его к кластеру Azure Databricks и запустите эксперимент. 

### <a name="install-libraries"></a>Установка библиотек

Чтобы установить библиотеки в кластере, перейдите на вкладку **Библиотеки** и нажмите **«Установка новая»**

 ![mlflow с лазурной диаграммой машинного обучения](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

В поле **пакета** введите лазурный поток, а затем нажмите установить. Повторите этот шаг по мере необходимости для установки других дополнительных пакетов в кластер для эксперимента.

 ![mlflow с лазурной диаграммой машинного обучения](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Настройка ноутбука и рабочего пространства

Как только кластер настроен, импортируйте блокнот эксперимента, откройте его и прикрепите к нему кластер.

Следующий код должен быть в блокноте эксперимента. Этот код получает подробную информацию о вашей подписке Azure, чтобы мгновенно вневить рабочее пространство. Этот код предполагает, что у вас есть существующая группа ресурсов и рабочее пространство Azure Machine Learning, в противном случае вы можете [создать их.](how-to-manage-workspace.md) 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Соедините рабочие места Azure Databricks и Azure Machine Learning

На [портале Azure](https://ms.portal.azure.com)можно связать рабочее пространство Azure Databricks (ADB) с новым или существующим рабочим пространством Для машинНого обучения Azure. Для этого перейдите в рабочее пространство ADB и выберите кнопку **Рабочего пространства машинного обучения Link Azure** в правом нижнем углу. Связь рабочих областей позволяет отслеживать данные эксперимента в рабочей области Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Ссылка MLflow отслеживания в рабочее пространство

После мгновенного мгновенного настройки рабочего пространства установите URI отслеживания MLflow. Поступая таким образом, вы связываете отслеживание MLflow с рабочим пространством машинного обучения Azure. После ссылок все ваши эксперименты будут всещены в управляемую службу отслеживания машин Azure Machine Learning.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Непосредственно установите отслеживание MLflow в вашем блокноте

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

В скрипте обучения импортируйте mlflow для использования AIS для регистрации MLflow и начните журналировать метрики выполнения. Следующий пример регистрирует метрику потери эпохи. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Автоматизация настройки отслеживания MLflow

Вместо того, чтобы вручную устанавливать URI слежения в каждом последующем сеансе тетради эксперимента в кластерах, сделайте это автоматически, используя этот [скрипт Azure Machine Learning Tracking Cluster Init.](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)

При правильной настройке вы можете видеть данные отслеживания MLflow в API Rest Azure Machine Learning и всех клиентах, а также в Azure Databricks через пользовательский интерфейс MLflow или с помощью клиента MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Просмотр метрик и артефактов в рабочей области

Метрики и артефакты из журналов MLflow хранятся в рабочем пространстве. Чтобы просмотреть их в любое время, перейдите в рабочее пространство и найдите эксперимент по имени в рабочей области в [студии Azure Machine Learning.](https://ml.azure.com)  Или запустите ниже код. 

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

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

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

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
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

[MLflow с ноутбуками Azure ML](https://aka.ms/azureml-mlflow-examples) демонстрируют и расширяют концепции, представленные в этой статье.

## <a name="next-steps"></a>Дальнейшие действия
* [Управление моделями](concept-model-management-and-deployment.md).
* Мониторинг производственных моделей для [дрейфа данных.](how-to-monitor-data-drift.md)
