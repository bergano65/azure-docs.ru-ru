---
title: Создание многоразовых сред ML
titleSuffix: Azure Machine Learning
description: Создание и управление средами для обучения и развертывания моделей. Управление пакетами Python и другими настройками для среды.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 3b1fc5dc427a8a9a1987b0ef916b99edb25e292a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063980"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Повторное использование сред для обучения и развертывания с помощью машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье узнайте, как создавать и управлять [средами](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)машинного обучения Azure. Используйте среды для отслеживания и воспроизведения программных зависимостей ваших проектов по мере их развития.

Управление зависимостью от программного обеспечения является общей задачей для разработчиков. Вы хотите, чтобы сборки воспроизводимы без обширной ручной конфигурации программного обеспечения. Класс Машинного `Environment` обучения Azure учитывает локальные решения для разработки, такие как pip и Conda, и предоставляет решение как для локальной, так и для распределенной разработки облаков.

Примеры в этой статье показывают, как:

* Создайте среду и укажите зависимости пакетов.
* Извлекайте и обновляйте среды.
* Используйте среду для обучения.
* Используйте среду для развертывания веб-службы.

Для обзора на высоком уровне того, как работают среды в Машинном обучении Azure, [см.](concept-environments.md)

## <a name="prerequisites"></a>Предварительные требования

* [SDK для Python машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Рабочее пространство машинного обучения Azure](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Создание среды

В следующих разделах рассматриваются несколько способов создания среды для экспериментов.

### <a name="use-a-curated-environment"></a>Использование кураторская среда

Вы можете выбрать одну из кураторских сред для начала: 

* Среда _AzureML-Minimal_ содержит минимальный набор пакетов, позволяющих отслеживать запуск и загрузку активов. Вы можете использовать его в качестве отправной точки для вашей собственной среды.

* Среда _AzureML-Tutorial_ содержит общие пакеты науки о данных. Эти пакеты включают Scikit-Learn, Панды, Matplotlib, и больший набор лазурных sdk пакетов.

Кураторские среды опираются на кэшированные изображения Docker. Эта поддержка снижает стоимость подготовки к запуску.

Используйте `Environment.get` метод для выбора одной из кураторских сред:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Вы можете перечислить кураторские среды и их пакеты, используя следующий код:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Не запускайте свое собственное имя среды с префиксом _AzureML._ Эта приставка зарезервирована для кураторских сред.

### <a name="instantiate-an-environment-object"></a>Мгновенное изображение объекта среды

Чтобы вручную создать среду, `Environment` импортируйте класс из SDK. Затем используйте следующий код для мгновенного обнаружения объекта среды.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Используйте файлы спецификаций Conda и pip

Вы также можете создать среду из спецификации Conda или файла требований к пипсу. Используйте [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) метод [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) или метод. В аргументе метода включите имя среды и нужный путь файла.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Использование существующих сред Conda

Если на локальном компьютере есть существующая среда Conda, вы можете использовать службу для создания объекта среды. Используя эту стратегию, вы можете повторно использовать локальную интерактивную среду на удаленных запусках.

Следующий код создает объект среды из `mycondaenv`существующей среды Conda. Он использует [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) метод.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Создание сред ы автоматически

Автоматически создавайте среду, отправив обучающий пробег. Отправить пробег с `submit()` помощью метода. При отправке учебного запуска, строительство новой среды может занять несколько минут. Продолжительность сборки зависит от размера требуемых зависимостей. 

Если перед отправкой запуска не указано среду в конфигурации выполнения, для вас создается среда по умолчанию.

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

Аналогичным образом, если [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) вы используете объект для обучения, вы можете непосредственно представить экземпляр оценщика в качестве запуска без указания среды. Объект `Estimator` уже инкапсулирует среду и цель вычислений.

## <a name="add-packages-to-an-environment"></a>Добавление пакетов в среду

Добавляйте пакеты в среду, используя файлы Conda, pip или частные файлы колес. Укажите каждую зависимость [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) пакета с помощью класса. Добавьте его в `PythonSection`окружающую среду.

### <a name="conda-and-pip-packages"></a>Конда и пип-пакеты

Если пакет доступен в репозитории пакета Conda, мы рекомендуем использовать установку Conda, а не установку пипса. Пакеты Conda обычно поставляются с заранее построенными бинарными файлами, которые делают установку более надежной.

Следующий пример добавляет к окружающей среде. Он добавляет версию 0.21.3 `scikit-learn`. Он также `pillow` добавляет `myenv`пакет, . В примере [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) используется [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) метод и метод, соответственно.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Если вы используете то же определение среды для другого запуска, служба машинного обучения Azure повторно использует кэшированное изображение среды. Например, ```numpy```при создании среды с незакрепленной зависимостью пакетов эта среда будет продолжать использовать версию пакета, _установленную во время создания среды._ Кроме того, любая будущая среда с соответствующим определением будет продолжать использовать старую версию. Для получения дополнительной информации смотрите [здание среды, кэширование и повторное использование.](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)

### <a name="private-wheel-files"></a>Частные файлы колес

Вы можете использовать частные файлы колес пип, сначала загрузив их в хранилище рабочего пространства. Вы загружаете [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) их с помощью статического метода. Затем вы захватываете URL-адрес хранилища `add_pip_package()` и передаем URL-адрес методу.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Управление средами

Управляйте средами, чтобы можно было обновлять, отслеживать и повторно использовать их в вычислительных целях и с другими пользователями рабочего пространства.

### <a name="register-environments"></a>Регистрация сред

Среда автоматически регистрируется в рабочем пространстве при отправке запуска или развертывании веб-службы. Вы также можете вручную зарегистрировать [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) среду с помощью метода. Эта операция превращает среду в объект, отслеживаемый и версируемый в облаке. Сущность может быть совместнораспределена между пользователями рабочего пространства.

Следующий код регистрирует `myenv` среду `ws` в рабочей области.

```python
myenv.register(workspace=ws)
```

При первом использовании среды при обучении или развертывании она регистрируется в рабочем пространстве. Затем он построен и развернут на вычислительной цели. Служба кэширует среды. Повторное использование кэшированной среды занимает гораздо меньше времени, чем использование новой службы или обновленной.

### <a name="get-existing-environments"></a>Получить существующие среды

Класс `Environment` предлагает методы, которые позволяют получить существующие среды в рабочей области. Вы можете получить среды по имени, как список, или по определенному учебному запуску. Эта информация полезна для устранения неполадок, аудита и воспроизводства.

#### <a name="view-a-list-of-environments"></a>Просмотр списка сред

Просмотр сред в рабочей области [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) с помощью класса. Затем выберите среду для повторного использования.

#### <a name="get-an-environment-by-name"></a>Получить среду по имени

Вы также можете получить определенную среду по имени и версии. Следующий код [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) использует метод для `1` получения `myenv` версии `ws` среды на рабочем пространстве.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Поезд для конкретной среды

Чтобы получить среду, которая использовалась для определенного запуска [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) после завершения `Run` обучения, используйте метод в классе.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Обновление существующей среды

Скажем, вы измените существующую среду, например, путем добавления пакета Python. Затем создается новая версия среды при отправке запуска, развертывае модели или ручной регистрации среды. Версия позволяет просматривать изменения среды с течением времени.

Чтобы обновить версию пакета Python в существующей среде, укажите номер версии для этого пакета. Если вы не используете точное число версий, то Azure Machine Learning будет повторно использовать существующую среду с исходными версиями пакетов.

### <a name="debug-the-image-build"></a>Отображение сборки изображения

В следующем примере [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) используется метод для ручного создания среды в качестве изображения Docker. Он отслеживает вывод журналы из сборки [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)изображения с помощью . Затем построенное изображение отображается в экземпляре реестра контейнеров Azure в рабочей области. Эта информация полезна для отладки.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Включить Докер

 Класс [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) Azure Machine `Environment` Learning позволяет тонко настроить и управлять гостевой операционной системой, на которой вы проводите обучение.

При включании Docker служба создает изображение Docker. Он также создает среду Python, которая использует ваши спецификации в контейнере Docker. Эта функциональность обеспечивает дополнительную изоляцию и воспроизводимость для ваших учебных запусков.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

По умолчанию новое изображение Docker отображается в реестре контейнеров, связанных с рабочим пространством.  Имя репозитория имеет форму *\<azureml/azureml_\>uuid*. Уникальный идентификатор *(uuid*) часть имени соответствует хэшу, вычисляемому из конфигурации среды. Эта корреспонденция позволяет службе определить, существует ли изображение для данной среды для повторного использования.

Кроме того, служба автоматически использует одно из [базовых изображений](https://github.com/Azure/AzureML-Containers)на основе Ubuntu Linux. Он устанавливает указанные пакеты Python. Базовое изображение имеет версии процессора и GPU версии. Azure Machine Learning автоматически определяет, какую версию использовать.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Вы также можете указать пользовательский Dockerfile. Проще всего начать с одного из базовых изображений Azure Machine Learning с помощью команды Docker, ```FROM``` а затем добавить свои собственные пользовательские шаги. Используйте этот подход, если вам нужно установить пакеты, не связанные с Python, в качестве зависимостей.

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

### <a name="use-user-managed-dependencies"></a>Используйте пользовательские зависимости

В некоторых ситуациях пользовательское базовое изображение может уже содержать среду Python с пакетами, которые вы хотите использовать.

По умолчанию служба машинного обучения Azure создаст среду Conda с указанными вами зависимостями и будет выполнять запуск в этой среде вместо использования библиотек Python, установленных на базовом изображении. 

Чтобы использовать собственные установленные пакеты, установите параметр. `Environment.python.user_managed_dependencies = True` Убедитесь, что базовое изображение содержит переводчика Python и содержит пакеты, необходимые для вашего сценария обучения.

Например, для запуска в базовой среде Miniconda с установленным пакетом NumPy сначала укажите Dockerfile с шагом для установки пакета. Затем установите пользовательские зависимости на `True`. 

Можно также указать путь к определенному переводчику Python `Environment.python.interpreter_path` в изображении, установив переменную.

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

Чтобы отправить обучающий пробег, необходимо объединить среду, [вычислить цель](concept-compute-target.md)и обучающий сценарий Python в конфигурацию выполнения. Эта конфигурация представляет собой объект обертки, который используется для отправки трасс.

При отправке учебного запуска создание новой среды может занять несколько минут. Продолжительность зависит от размера требуемых зависимостей. Среды кэшируются службой. Так что до тех пор, пока определение среды остается неизменным, вы несете полное время установки только один раз.

Следующий пример выполнения локального сценария [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) показывает, где вы будете использовать в качестве объекта обертки.

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
> Чтобы отключить историю выполнения или моментальные снимки, используйте параметр под `ScriptRunConfig.run_config.history`.

Если вы не указали среду в конфигурации запуска, то служба создает среду по умолчанию при отправке запуска.

### <a name="use-an-estimator-for-training"></a>Используйте оценщика для обучения

Если вы используете [оценщика](how-to-train-ml-models.md) для обучения, то вы можете представить экземпляр оценщика непосредственно. Он уже инкапсулирует среду и цель вычислений.

В следующем коде используется оценщик для обучения одного узла. Он работает на удаленном `scikit-learn` вычисления для модели. Он предполагает, что вы ранее создали `compute_target`объект вычисления, и `ds`объект хранилища данных, .

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

## <a name="use-environments-for-web-service-deployment"></a>Использование сред для развертывания веб-службы

Среды можно использовать при развертывании модели в качестве веб-службы. Эта возможность позволяет воспроизводиться, подключенный рабочий процесс. В этом рабочем процессе можно обучить, протестировать и развернуть модель, используя одни и те же библиотеки как в обучающих вычислениях, так и в выводных вычислениях.

Чтобы развернуть веб-службу, объедините среду, вычислить вывод, скрипт скоринга и зарегистрированную модель в объекте развертывания. [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Для получения дополнительной [How and where to deploy models](how-to-deploy-and-where.md)информации см.

В этом примере предположим, что вы завершили обучение. Теперь необходимо развернуть эту модель в экземплярах контейнеров Azure. При создании веб-службы на изображении устанавливаются файлы модели и скоринга, а в изображение добавляется стек выводов Azure Machine Learning.

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

Этот [пример ноутбука](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) расширяет концепции и методы, продемонстрированы в этой статье.

[Развертывание модели с помощью пользовательского базового изображения Docker](how-to-deploy-custom-docker-image.md) демонстрирует, как развернуть модель с помощью пользовательского базового изображения Docker.

В [этом примере —смодели](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) Spark показано, как развертывать модель Spark в виде веб-сервиса.

## <a name="create-and-manage-environments-with-the-cli"></a>Создание и управление средами с помощью CLI

[CLI Машинного обучения Azure](reference-azure-machine-learning-cli.md) отражает большую часть функциональности Python SDK. Вы можете использовать его для создания и управления средами. Команды, которые мы обсуждаем в этом разделе, демонстрируют основную функциональность.

Следующая команда подмостки файлы для определения среды по умолчанию в указанном каталоге. Эти файлы являются файлами JSON. Они работают как соответствующий класс в SDK. Вы можете использовать файлы для создания новых сред, которые имеют пользовательские настройки. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Запустите следующую команду для регистрации среды из указанного каталога.

```azurecli-interactive
az ml environment register -d myenvdir
```

Выполнить следующую команду, чтобы перечислить все зарегистрированные среды.

```azurecli-interactive
az ml environment list
```

Загрузите зарегистрированную среду, используя следующую команду.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Дальнейшие действия

* Для использования управляемой вычислительной цели для обучения модели [см.](tutorial-train-models-with-aml.md)
* После того как у вас есть обученная модель, узнайте, [как и где развертывать модели.](how-to-deploy-and-where.md)
* Просмотр [ `Environment` ссылки класса SDK.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)
* Для получения дополнительной информации о концепциях и методах, описанных в этой статье, [см.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)
