---
title: Создание повторно используемых сред машинного обучения
titleSuffix: Azure Machine Learning
description: Создание сред для обучения и развертывания модели и управление ими. Управление пакетами Python и другими параметрами для среды.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536358"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Повторное использование сред для обучения и развертывания с помощью Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как создавать [среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)машинное обучение Azure и управлять ими. Используйте среды для мониторинга и воспроизведения зависимостей программного обеспечения проектов по мере их развития.

Управление зависимостями программного обеспечения — это распространенная задача для разработчиков. Необходимо обеспечить воспроизводимость сборок без обширной ручной настройки программного обеспечения. Машинное обучение Azure `Environment` класса учетные записи для локальных решений разработки, таких как PIP и Conda, и предоставляют решение как для локальной, так и для распределенной разработки облачных приложений.

В примерах этой статьи показано, как:

* Создайте среду и укажите зависимости пакетов.
* Получение и обновление сред.
* Используйте среду для обучения.
* Используйте среду для развертывания веб-службы.

Общий обзор работы сред в Машинное обучение Azure см. в статье [что такое среды машинного обучения](concept-environments.md).

## <a name="prerequisites"></a>Предварительные условия

* [Пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Создание среды

В следующих разделах рассматриваются различные способы создания среды для экспериментов.

### <a name="use-a-curated-environment"></a>Использование проверенной среды

Вы можете выбрать одну из проверенных сред, чтобы начать с: 

* Среда _AzureML_ имеет минимальный набор пакетов, позволяющих отслеживать выполнение и отправку ресурсов. Его можно использовать в качестве отправной точки для собственной среды.

* Среда _AzureML-Tutorial_ содержит общие пакеты обработки и анализа данных. К этим пакетам относятся Scikit-учиться, Pandas, Matplotlib и более широкий набор пакетов azureml-SDK.

Проверенные среды поддерживаются кэшированными образами DOCKER. Это сокращает затраты на подготовку к запуску.

Используйте `Environment.get` метод, чтобы выбрать одну из проверенных сред:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Список проверенных сред и их пакетов можно вывести с помощью следующего кода:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Не начинайте свое имя среды с префиксом _AzureML_ . Этот префикс зарезервирован для проверенных сред.

### <a name="instantiate-an-environment-object"></a>Создание экземпляра объекта среды

Чтобы вручную создать среду, импортируйте `Environment` класс из пакета SDK. Затем используйте следующий код для создания экземпляра объекта среды.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Использование файлов спецификации Conda и PIP

Можно также создать среду из спецификации Conda или файла требований PIP. Используйте [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) метод или [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) метод. В аргументе метода укажите имя среды и путь к нужному файлу.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Использование существующих сред Conda

Если на локальном компьютере имеется среда Conda, то для создания объекта среды можно использовать службу. С помощью этой стратегии можно повторно использовать локальную интерактивную среду на удаленных запусках.

Следующий код создает объект среды из существующей среды `mycondaenv`Conda. Он использует [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) метод.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Автоматическое создание сред

Автоматическое создание среды путем отправки обучающего запуска. Отправьте запуск с помощью `submit()` метода. При отправке обучающего запуска создание новой среды может занять несколько минут. Длительность сборки зависит от размера необходимых зависимостей. 

Если вы не укажете среду в конфигурации запуска перед отправкой выполнения, среда по умолчанию будет создана.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Аналогично, при использовании [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) объекта для обучения можно напрямую отправить экземпляр средства оценки в качестве запуска без указания среды. `Estimator` Объект уже инкапсулирует среду и целевой объект вычислений.

## <a name="add-packages-to-an-environment"></a>Добавление пакетов в среду

Добавление пакетов в среду с помощью Conda, PIP или частных колесных файлов. Укажите зависимость каждого пакета с помощью [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) класса. Добавьте его в среду `PythonSection`.

### <a name="conda-and-pip-packages"></a>Пакеты Conda и PIP

Если пакет доступен в репозитории пакетов Conda, рекомендуется использовать установку Conda, а не установку PIP. Пакеты Conda обычно поставляются с предварительно созданными двоичными файлами, которые делают установку более надежной.

В следующем примере добавляется в среду. Он добавляет версию 1.17.0 `numpy`. Он также добавляет `pillow` пакет, `myenv`. В примере используется [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) метод и [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) метод соответственно.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Если вы используете одно и то же определение среды для другого запуска, служба Машинное обучение Azure повторно использует кэшированное изображение среды. Например ```numpy```, при создании среды с незакрепленной зависимостью пакета эта среда будет использовать версию пакета, установленную _во время создания среды_. Кроме того, любая будущая среда с соответствующим определением будет использовать старую версию. Дополнительные сведения см. в статье [Создание среды, кэширование и повторное использование](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-wheel-files"></a>Файлы частных колес

Вы можете использовать частные файлы колесика PIP, предварительно отгружая их в хранилище рабочей области. Их можно передать с помощью статического [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) метода. Затем вы записываете URL-адрес хранилища и передаете ему URL-адрес `add_pip_package()` метода.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Управление средами

Управление средами для обновления, мониторинга и повторного использования в целевых объектах вычислений и других пользователей рабочей области.

### <a name="register-environments"></a>Регистрация сред

Среда автоматически регистрируется в рабочей области при отправке выполнения или развертывании веб-службы. Можно также вручную зарегистрировать среду с помощью [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) метода. Эта операция делает среду в сущности, которая будет отслеживанием и управлением версиями в облаке. Сущность может совместно использоваться пользователями рабочей области.

Следующий код регистрирует `myenv` среду в `ws` рабочей области.

```python
myenv.register(workspace=ws)
```

При первом использовании среды в ходе обучения или развертывании она регистрируется в рабочей области. Затем он создается и разворачивается на целевом объекте вычислений. Служба кэширует окружения. Повторное использование кэшированной среды занимает гораздо меньше времени, чем использование новой службы или обновленной.

### <a name="get-existing-environments"></a>Получение существующих сред

`Environment` Класс предлагает методы, позволяющие извлекать существующие среды в рабочей области. Среды можно извлекать по имени, списку или по определенному запуску обучения. Эти сведения полезны для устранения неполадок, аудита и воспроизводимость.

#### <a name="view-a-list-of-environments"></a>Просмотр списка сред

Просмотрите окружения в рабочей области с помощью [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) класса. Затем выберите среду для повторного использования.

#### <a name="get-an-environment-by-name"></a>Получение окружения по имени

Кроме того, можно получить конкретную среду по имени и версии. [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) В следующем коде метод используется для получения `1` версии `myenv` среды в `ws` рабочей области.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Обучение среды выполнения

Чтобы получить среду, которая использовалась для определенного запуска после завершения обучения, используйте [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) метод в `Run` классе.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Обновление существующей среды

Предположим, вы изменили существующую среду, например, добавив пакет Python. После отправки выполнения, развертывания модели или регистрации среды вручную создается новая версия среды. Управление версиями позволяет просматривать изменения среды с течением времени.

Чтобы обновить версию пакета Python в существующей среде, укажите номер версии для этого пакета. Если вы не используете точный номер версии, Машинное обучение Azure будет повторно использовать существующую среду с исходными версиями пакета.

### <a name="debug-the-image-build"></a>Отладка сборки образа

В следующем примере [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) метод используется для ручного создания среды в качестве образа DOCKER. Он отслеживает выходные журналы из сборки образа с помощью [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). Затем созданный образ появится в экземпляре реестра контейнеров Azure рабочей области. Эти сведения полезны для отладки.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Включение DOCKER

 [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) Класс машинное обучение Azure `Environment` позволяет точно настраивать и контролировать операционную систему на виртуальной машине, в которой выполняется обучение.

При включении DOCKER служба создает образ DOCKER. Он также создает среду Python, которая использует ваши спецификации в контейнере DOCKER. Эта функция обеспечивает дополнительную изоляцию и воспроизводимость для учебных запусков.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

По умолчанию недавно созданный образ DOCKER отображается в реестре контейнеров, связанном с рабочей областью.  Имя репозитория имеет форму *azureml/azureml_\<\>UUID*. Уникальный идентификатор (*UUID*) имени соответствует хэшу, вычисленному на основе конфигурации среды. Эта корреспонденция позволяет службе определить, существует ли уже образ для данной среды для повторного использования.

Кроме того, служба автоматически использует один из [базовых образов](https://github.com/Azure/AzureML-Containers)на основе Ubuntu Linux. Он устанавливает указанные пакеты Python. Базовый образ содержит версии ЦП и GPU. Машинное обучение Azure автоматически определяет, какую версию следует использовать.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Можно также указать пользовательский Dockerfile. Проще всего начать с одного из Машинное обучение Azure базовых образов с помощью ```FROM``` команды DOCKER, а затем добавить пользовательские шаги. Используйте этот подход, если необходимо установить пакеты, отличные от Python, как зависимости.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Использование управляемых пользователем зависимостей

В некоторых ситуациях пользовательский базовый образ может уже содержать среду Python с пакетами, которые вы хотите использовать.

По умолчанию Машинное обучение Azure служба будет создавать среду Conda с указанными зависимостями и будет выполнять выполнение в этой среде вместо использования библиотек Python, установленных на базовом образе. 

Чтобы использовать свои установленные пакеты, задайте параметр `Environment.python.user_managed_dependencies = True`. Убедитесь, что базовый образ содержит интерпретатор Python и содержит пакеты, необходимые для вашего сценария обучения.

Например, чтобы запустить в базовой среде Miniconda с установленным пакетом NumPy, сначала укажите Dockerfile с шагом для установки пакета. Затем установите зависимости, управляемые пользователем, в `True`значение. 

Можно также указать путь к конкретному интерпретатору Python внутри изображения, задав `Environment.python.interpreter_path` переменную.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Использование сред для обучения

Чтобы отправить обучающий запуск, необходимо объединить среду, [целевой объект вычислений](concept-compute-target.md)и обучающий скрипт Python в конфигурацию запуска. Эта конфигурация является объектом-оболочкой, используемой для отправки запусков.

При отправке обучающего запуска создание новой среды может занять несколько минут. Длительность зависит от размера необходимых зависимостей. Среды кэшируются службой. Так как определение среды остается неизменным, полное время настройки задается только один раз.

В следующем примере выполнения локального скрипта показано, где можно [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) использовать в качестве объекта-оболочки.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Чтобы отключить журнал выполнения или выполнить моментальные снимки, используйте параметр в разделе `ScriptRunConfig.run_config.history`.

Если вы не укажете среду в конфигурации запуска, служба создает среду по умолчанию при отправке выполнения.

### <a name="use-an-estimator-for-training"></a>Использование средства оценки для обучения

При использовании средства [оценки](how-to-train-ml-models.md) для обучения можно отправить экземпляр средства оценки напрямую. Он уже инкапсулирует среду и целевой объект вычислений.

В следующем коде используется оценщик для одноузлового выполнения обучения. Он выполняется на удаленном вычислении для `scikit-learn` модели. Предполагается, что ранее был создан целевой объект `compute_target`вычислений, и объект хранилища данных. `ds`

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Использование сред для развертывания веб-служб

Среды можно использовать при развертывании модели в качестве веб-службы. Эта возможность позволяет воспроизводимый подключенный рабочий процесс. В этом рабочем процессе можно обучить, протестировать и развернуть модель с помощью тех же самых библиотек, что и в обучающем вычислении, и в вычислениях.

Чтобы развернуть веб-службу, объедините среду, вычисление вычислений, Скрипт оценки и зарегистрированную модель в объекте развертывания [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Дополнительные сведения см. [в разделе как и где развертываются модели](how-to-deploy-and-where.md).

В этом примере предполагается, что вы завершили обучающий запуск. Теперь вы хотите развернуть эту модель в службе "экземпляры контейнеров Azure". При создании веб-службы файлы модели и оценки подключаются к образу, а на изображение добавляется Машинное обучение Azure стек вывода.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Примеры записных книжек

В этом [примере Записная книжка](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) расширяет концепции и методы, продемонстрированные в этой статье.

[Развертывание модели с помощью пользовательского базового образа DOCKER](how-to-deploy-custom-docker-image.md) демонстрирует развертывание модели с помощью пользовательского базового образа DOCKER.

В этом [примере записной книжки](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) показано, как развернуть модель Spark в качестве веб-службы.

## <a name="create-and-manage-environments-with-the-cli"></a>Создание сред и управление ими с помощью интерфейса командной строки

[Машинное обучение Azure CLI](reference-azure-machine-learning-cli.md) отражает большую часть функциональных возможностей пакета SDK для Python. Его можно использовать для создания сред и управления ими. Команды, обсуждаемые в этом разделе, демонстрируют базовые функциональные возможности.

Следующая команда формирует шаблоны файлов для определения среды по умолчанию в указанном каталоге. Эти файлы являются файлами JSON. Они работают аналогично соответствующему классу в пакете SDK. Эти файлы можно использовать для создания новых сред с пользовательскими параметрами. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Выполните следующую команду, чтобы зарегистрировать среду из указанного каталога.

```azurecli-interactive
az ml environment register -d myenvdir
```

Выполните следующую команду, чтобы вывести список всех зарегистрированных сред.

```azurecli-interactive
az ml environment list
```

Скачайте зарегистрированную среду с помощью следующей команды.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Дальнейшие шаги

* Сведения об использовании управляемого целевого объекта вычислений для обучения модели см. в разделе [учебник. Обучение модели](tutorial-train-models-with-aml.md).
* После создания обученной модели Узнайте, [как и где развертываются модели](how-to-deploy-and-where.md).
* Просмотрите [ `Environment` ссылку на пакет SDK для класса](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Дополнительные сведения о концепциях и методах, описанных в этой статье, см. в [примере записной книжки](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
