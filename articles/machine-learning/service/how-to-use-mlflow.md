---
title: Использование Млфлов с
titleSuffix: Azure Machine Learning
description: Настройте Млфлов с Машинное обучение Azure для записи метрик & артефактов и развертывания моделей из модуля данных, локальной среды или среды виртуальной машины.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: d98e45d3ef77fea6b64efef10c20ecce3787b14c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489334"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>Мониторинг метрик и развертывание моделей с помощью Млфлов и Машинное обучение Azure (Предварительная версия)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье показано, как включить универсальный код ресурса (URI) отслеживания Млфлов и API ведения журнала, который называется [отслеживанием млфлов](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)с машинное обучение Azure. Это позволит вам:

+ Отследите и записываюте метрики и артефакты экспериментов в [рабочую область машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Если вы уже используете отслеживание Млфлов для экспериментов, Рабочая область предоставляет централизованное, безопасное и масштабируемое расположение для хранения ваших обучающих метрик и моделей.

+ Развертывание экспериментов Млфлов в качестве веб-службы Машинное обучение Azure. Развертывание в качестве веб-службы позволяет применять функции мониторинга Машинное обучение Azure и распознавания отклонения данных в рабочих моделях. 

[Млфлов](https://www.mlflow.org) — это библиотека с открытым исходным кодом для управления жизненным циклом экспериментов машинного обучения. Отслеживание Млфлов — это компонент Млфлов, который записывает и отслеживает метрики и артефакты обучающего запуска, независимо от среды эксперимента — локально, на виртуальной машине, в удаленном кластере, даже на Azure Databricks.

На следующей схеме показано, что с отслеживанием Млфлов можно предпринять любые эксперименты, если они находятся на удаленном целевом объекте вычислений на виртуальной машине, локально на компьютере или в кластере Azure Databricks, и отслеживать артефакты его выполнения и модели хранения. в рабочей области Машинное обучение Azure.

![млфлов со схемой машинного обучения Azure](media/how-to-use-mlflow/mlflow-diagram-track.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Сравнение клиентов Млфлов и Машинное обучение Azure

 В таблице ниже приведены общие сведения о различных клиентах, которые могут использовать Машинное обучение Azure, а также соответствующие возможности функций.

 Отслеживание Млфлов предлагает функциональные возможности ведения журнала метрик и хранилища артефактов, которые доступны только в других случаях через [пакет SDK для машинное обучение Azure Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Развертывание & отслеживания Млфлов | Пакет SDK для Машинное обучение Azure Python |  Интерфейс командной строки службы "Машинное обучение Azure" | Студия машинного обучения Azure.|
|---|---|---|---|---|
| Управление рабочей областью |   | ✓ | ✓ | ✓ |
| Использование хранилищ данных  |   | ✓ | ✓ | |
| Метрики журнала      | ✓ | ✓ |   | |
| Отправить артефакты | ✓ | ✓ |   | |
| Просмотр метрик     | ✓ | ✓ | ✓ | ✓ |
| Управление вычислительными ресурсами   |   | ✓ | ✓ | ✓ |
| Развертывание моделей    | ✓ | ✓ | ✓ | ✓ |
|Мониторинг производительности модели||✓|  |   |
| Определение смещения данных |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Технические условия

* [Установите Млфлов.](https://mlflow.org/docs/latest/quickstart.html)
* [Установка пакета sdk машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) на локальном компьютере. пакет SDK обеспечивает подключение для млфлов к рабочей области.
* [Создайте Рабочая область машинного обучения Azure](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Следите за локальными запусками

Отслеживание Млфлов с помощью Машинное обучение Azure позволяет хранить зарегистрированные метрики и артефакты из локальных запусков в рабочей области Машинное обучение Azure.

Установите пакет `azureml-contrib-run`, чтобы использовать отслеживание Млфлов с Машинное обучение Azure в экспериментах, локально выполняемых в Jupyter Notebook или редакторе кода.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Пространство имен azureml. от участников сообщества часто меняется, так как мы работаем над улучшением службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.

Импортируйте классы `mlflow` и [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) , чтобы получить доступ к универсальному коду ресурса (URI) отслеживания млфлов и настроить рабочую область.

В следующем коде метод `get_mlflow_tracking_uri()` присваивает рабочему пространству уникальный адрес URI отслеживания, `ws`и `set_tracking_uri()` указывает универсальный код ресурса (URI) отслеживания Млфлов для этого адреса.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>URI отслеживания действителен до часа или меньше. Если вы перезапустите сценарий после некоторого времени простоя, используйте API get_mlflow_tracking_uri для получения нового URI.

Задайте имя эксперимента Млфлов с помощью `set_experiment()` и запустите обучающий запуск с `start_run()`. Затем используйте `log_metric()`, чтобы активировать API ведения журнала Млфлов и начать запись в журнал метрик выполнения обучения.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Отслеживание удаленных запусков

Отслеживание Млфлов с помощью Машинное обучение Azure позволяет хранить зарегистрированные метрики и артефакты из удаленных запусков в рабочей области Машинное обучение Azure.

Удаленные запуски позволяют обучать модели с помощью более мощных вычислений, таких как виртуальные машины с поддержкой GPU или кластеры Вычислительная среда Машинного обучения. Дополнительные сведения о различных параметрах вычислений см. в разделе [Настройка целевых объектов вычислений для обучения модели](how-to-set-up-training-targets.md) .

Настройте среду выполнения вычислений и обучения с помощью класса [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Включите `mlflow` и `azure-contrib-run` пакеты PIP в раздел [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) окружения. Затем конструирует [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) с удаленным вычислением в качестве целевого объекта вычислений.

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

В скрипте обучения импортируйте `mlflow` для использования API-интерфейсов ведения журнала Млфлов и начните регистрировать метрики выполнения.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

С помощью этой конфигурации выполнения вычислений и обучения используйте метод `Experiment.submit('train.py')` для отправки выполнения. Это автоматически настраивает универсальный код ресурса (URI) отслеживания Млфлов и направляет ведение журнала из Млфлов в рабочую область.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Отслеживание выполнений Azure Databricks

Отслеживание Млфлов с помощью Машинное обучение Azure позволяет хранить зарегистрированные метрики и артефакты из модулях данных, выполняемых в рабочей области Машинное обучение Azure.

Чтобы запустить эксперименты Млфлов с Azure Databricks, необходимо сначала создать [Azure Databricks рабочую область и кластер](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

В кластере не забудьте установить библиотеку *azureml-млфлов* из PyPi, чтобы предоставить кластеру доступ к необходимым функциям и классам.
Здесь импортируйте записную книжку эксперимента, подключите к ней кластер и запустите свой эксперимент. 

### <a name="install-libraries"></a>Установка библиотек

Чтобы установить библиотеки в кластере, перейдите на вкладку **библиотеки** и щелкните **установить новый** .

 ![млфлов со схемой машинного обучения Azure](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

В поле **пакет** введите azureml-млфлов и нажмите кнопку установить. Повторите этот шаг, чтобы установить другие дополнительные пакеты в кластер для вашего эксперимента.

 ![млфлов со схемой машинного обучения Azure](media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Настройка записной книжки и рабочей области

После настройки кластера импортируйте записную книжку эксперимента, откройте ее и подключите к ней кластер.

Следующий код должен находиться в записной книжке эксперимента. Это позволит получить сведения о подписке Azure для создания экземпляра рабочей области. Предполагается, что у вас есть группа ресурсов и Рабочая область Машинное обучение Azure. в противном случае ее можно [создать](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Подключение рабочих областей Azure Databricks и Машинное обучение Azure

На [портал Azure](https://ms.portal.azure.com)можно связать рабочую область Azure DATABRICKS (ADB) с новой или существующей рабочей областью машинное обучение Azure. Для этого перейдите в рабочую область ADB и нажмите кнопку **Link машинное обучение Azure Workspace (связать с рабочей областью** ) в правом нижнем углу. Связывание рабочих областей позволяет отслеживанию данных экспериментов в рабочей области Машинное обучение Azure. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Связывание отслеживания Млфлов с рабочей областью

После создания экземпляра рабочей области задайте универсальный код ресурса (URI) для отслеживания Млфлов. При этом отслеживание Млфлов связывается с рабочей областью Машинное обучение Azure. После этого все эксперименты будут находиться в управляемой службе отслеживания Машинное обучение Azure.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Непосредственная настройка отслеживания Млфлов в записной книжке

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

В сценарии обучения импортируйте млфлов для использования API-интерфейсов ведения журнала Млфлов и начните регистрировать метрики выполнения. В следующем примере записывается метрика потери эпохи. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Автоматическая настройка отслеживания Млфлов

Вместо того, чтобы вручную задавать URI отслеживания в каждом последующем сеансе работы с записными книжками в кластерах, сделайте это автоматически, используя этот [машинное обучение Azure скрипт инициализации кластера отслеживания](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

При правильной настройке данные отслеживания Млфлов можно просматривать в REST APIх и всех клиентах Машинное обучение Azure, а также в Azure Databricks через пользовательский интерфейс Млфлов или с помощью клиента Млфлов.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Просмотр метрик и артефактов в рабочей области

Метрики и артефакты из журнала Млфлов хранятся в рабочей области. Чтобы просмотреть их в любое время, перейдите в рабочую область и найдите эксперимент по имени в рабочей области в [машинное обучение Azure Studio](https://ml.azure.com).  Или выполните приведенный ниже код. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Развертывание моделей Млфлов в качестве веб-службы

Развертывание экспериментов Млфлов в качестве веб-службы Машинное обучение Azure позволяет использовать возможности Машинное обучение Azure управления моделями и функции обнаружения и смещения данных, а также применять их к рабочим моделям.

На следующей схеме показано, что с помощью API развертывания Млфлов можно развернуть существующие модели Млфлов в качестве веб-службы Машинное обучение Azure, несмотря на их платформы — PyTorch, Tensorflow, scikit-учиться, ONNX и т. д., а также управлять производственными моделями в Рабочая область.

![млфлов со схемой машинного обучения Azure](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Регистрация модели

Перед развертыванием убедитесь, что модель сохранена, чтобы можно было ссылаться на нее и ее расположение для развертывания. В скрипте обучения должен быть указан код, аналогичный следующему методу [млфлов. sklearn. log _model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) , который сохраняет модель в заданном каталоге Outputs. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Включите параметр `conda_env`, чтобы передать представление словаря зависимостей и среды, в которых должна выполняться эта модель.

### <a name="retrieve-model-from-previous-run"></a>Получение модели из предыдущего запуска

Чтобы получить нужный запуск, требуется идентификатор запуска и путь в журнале выполнения, где была сохранена модель. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Создание образа DOCKER

Функция `mlflow.azureml.build_image()` создает образ DOCKER из сохраненной модели с учетом платформы. Он автоматически создает зависящий от платформы код оболочки и определяет зависимости пакетов. Укажите путь модели, рабочую область, идентификатор запуска и другие параметры.

Следующий код создает образ DOCKER с помощью *запуска:/< Run. id >/МОДЕЛ* в качестве пути model_uri для эксперимента Scikit-учиться.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
Создание образа DOCKER может занять несколько минут. 

### <a name="deploy-the-docker-image"></a>Развертывание образа DOCKER 

После создания образа используйте пакет SDK для Машинное обучение Azure, чтобы развернуть образ как веб-службу.

Сначала укажите конфигурацию развертывания. Экземпляр контейнера Azure (ACI) подходит для быстрого развертывания в режиме разработки и тестирования, а служба Azure Kubernetes (AKS) подходит для масштабируемых рабочих развертываний.

#### <a name="deploy-to-aci"></a>Развертывание в ACI

Настройте конфигурацию развертывания с помощью метода [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Кроме того, можно добавить теги и описания, которые помогут отследить веб-службу.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Затем разверните образ с помощью метода [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) пакета SDK машинное обучение Azure. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Развертывание в AKS

Чтобы выполнить развертывание в AKS, необходимо создать кластер AKS и перенести образ DOCKER, который требуется развернуть. Для этого примера перенесите ранее созданный образ из развертывания ACI.

Чтобы получить образ из предыдущего ACI развертывания, используйте класс [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) . 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Создание AKS вычислений. Создание кластера может занять 20-25 мин.

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
Настройте конфигурацию развертывания с помощью метода [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Кроме того, можно добавить теги и описания, которые помогут отследить веб-службу.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Затем разверните образ с помощью метода [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) пакета SDK машинное обучение Azure. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

Развертывание службы может занять несколько минут.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не планируете использовать зарегистрированные метрики и артефакты в рабочей области, возможность удалить их по отдельности в данный момент недоступна. Вместо этого удалите группу ресурсов, содержащую учетную запись хранения и рабочую область, чтобы не взиматься плата.

1. На портале Azure выберите **Группы ресурсов** в левой части окна.

   ![Удаление ресурсов на портале Azure](media/how-to-use-mlflow/delete-resources.png)

1. В списке выберите созданную группу ресурсов.

1. Выберите **Удалить группу ресурсов**.

1. Введите имя группы ресурсов. Теперь щелкните **Удалить**.


## <a name="example-notebooks"></a>Примеры записных книжек

[Млфлов с записными книжками машинного обучения Azure](https://aka.ms/azureml-mlflow-examples) демонстрируют и расширяют концепции, представленные в этой статье.

## <a name="next-steps"></a>Дальнейшие действия
* [Управляйте моделями](concept-model-management-and-deployment.md).
* Мониторинг производственных моделей для [смещения данных](how-to-monitor-data-drift.md).
