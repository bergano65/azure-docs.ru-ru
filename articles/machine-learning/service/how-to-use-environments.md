---
title: Создание, использование и управление средами для обучения и развертывания моделей
titleSuffix: Azure Machine Learning service
description: Создание сред для обучения и развертывания модели и управление ими. Управление пакетами Python и другими параметрами для среды.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: seodec18
ms.openlocfilehash: e506259b980c0aaf0300c0bb0a1aa0803171643a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098955"
---
# <a name="create-and-manage-environments-for-training-and-deployment"></a>Создание сред для обучения и развертывания и управление ими

В этой статье вы узнаете, как создавать [среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) машинное обучение Azure и управлять ими, чтобы вы могли отслеживанию и воспроизведение зависимостей программного обеспечения проектов по мере их развития.

Управление зависимостями программного обеспечения — это распространенная задача для разработчиков. Вы хотите обеспечить воспроизводимость сборок, не требуя настройки программного обеспечения вручную. С учетом решений для локальной разработки, например PIP и Conda, класс сред служб Машинное обучение Azure предоставляет решение как для локальной, так и для распределенной разработки облачных приложений.

В примерах этой статьи показано, как:

* Создание среды и указание зависимостей пакета
* Получение и обновление сред
* Использование среды для обучения
* Использовать среду для развертывания веб-службы

## <a name="what-are-environments"></a>Что такое среды

В средах указываются пакеты Python, переменные среды и параметры программного обеспечения для сценариев обучения и оценки, а также время выполнения (Python, Spark или DOCKER). Это управляемые и версии сущностей в рабочей области Машинное обучение Azure, которые позволяют воспроизводимые, отслеживаемые и переносимые рабочие процессы машинного обучения в различных целевых объектах вычислений.

Объект среды можно использовать в локальных средах вычислений для разработки сценария обучения, повторного использования этой же среды на Машинное обучение Azure вычислений для обучения модели и даже для развертывания модели в этой же среде.

Ниже показано, что один и тот же объект среды может использоваться как в конфигурации запуска для обучения, так и в конфигурации для развертывания веб-служб.

![Схема окружения в рабочем процессе машинного обучения](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>Типы сред

Среды можно разделить на две категории: **управляемое пользователем** и **управляемое системой**.

В среде, управляемой пользователем, вы несете ответственность за настройку среды и установку каждого пакета, который необходим вашему обучающему сценарию на целевом объекте вычислений. Conda не будет проверять вашу среду или устанавливать что-либо за вас. 

Среды, управляемые системой, используются, когда требуется [Conda](https://conda.io/docs/) управлять средой Python и зависимостями скриптов. По умолчанию служба использует этот тип окружения из-за его полезности в удаленных целевых объектах вычислений, которые не настраиваются вручную.

## <a name="prerequisites"></a>Предварительные требования

* [Установленный](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)пакет SDK для машинное обучение Azure для Python.
* [Рабочая область службы машинное обучение Azure](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Создание среды

Существует несколько способов создания среды для экспериментов.

### <a name="instantiate-an-environment-object"></a>Создание экземпляра объекта среды

Чтобы вручную создать среду, импортируйте класс среды из пакета SDK и создайте экземпляр объекта среды с помощью следующего кода.

```python
from azureml.core import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Файлы спецификации Conda и PIP

Можно также создать среду из спецификации Conda или файла требований PIP.
Используйте метод [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) или [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) и укажите в аргументе метода имя среды и путь к нужному файлу.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Существующая среда Conda

При наличии существующей среды Conda на локальном компьютере служба предлагает решение для создания объекта среды из него. Таким образом можно повторно использовать локальную интерактивную среду на удаленных запусках.

Следующий пример создает объект среды из существующей среды `mycondaenv` Conda с помощью метода [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Автоматическое создание сред

Автоматическое создание среды путем отправки обучающего запуска с помощью метода Submit (). При отправке обучающего запуска создание новой среды может занять несколько минут в зависимости от размера необходимых зависимостей. 

Если вы не укажете среду в конфигурации запуска перед отправкой выполнения, будет создана среда по умолчанию.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

Аналогично, при использовании [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) объекта для обучения можно отправить экземпляр средства оценки непосредственно в качестве запуска без указания среды, `Estimator` поскольку объект уже инкапсулирует среду и целевой объект вычислений.


## <a name="add-packages-to-an-environment"></a>Добавление пакетов в среду

Добавление пакетов в среду с помощью Conda, PIP или частных колесных файлов. Укажите каждую зависимость пакета с помощью [класса кондадепенденци](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)и добавьте ее в писонсектион окружения.

### <a name="conda-and-pip-packages"></a>Пакеты Conda и PIP

Если пакет доступен в репозитории пакетов Conda, рекомендуется использовать Conda с пошаговой установкой. Причина заключается в том, что пакеты Conda обычно поставляются с предварительно созданными двоичными файлами, которые делают установку более надежной.

В следующем примере в `scikit-learn`среду добавляются, в частности `pillow` , версия 0.21.3 и пакет `myenv` , с [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) методами и [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) соответственно.

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Файлы частных колес

Можно использовать частные файлы колесика PIP, предварительно отгружая их в хранилище рабочей области с помощью статического [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) метода, записывая URL-адрес хранилища и передавая ему URL-адрес `add_pip_package()` метода.

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

Среда автоматически регистрируется в рабочей области при отправке выполнения или развертывании веб-службы. Можно также вручную зарегистрировать среду с помощью метода [Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Эта операция делает среду в сущности, которая будет относиться и контролироваться в облаке, и может совместно использоваться пользователями рабочей области.

В следующем примере среда `myenv`зарегистрируется в `ws`рабочей области.

```python
myenv.register(workspace=ws)
```

При первом использовании в процессе обучения или развертывания среда регистрируется в рабочей области, создается и развертывается на целевом объекте вычислений. Среды кэшируются службой. Повторное использование кэшированной среды занимает гораздо меньше времени, чем использование новой службы или обновленной.

### <a name="get-existing-environments"></a>Получение существующих сред

Класс Environment предоставляет методы, позволяющие извлекать существующие среды в рабочей области по имени, списку или по конкретному запуску обучения. Для устранения неполадок или проведения аудита воспроизводимость

#### <a name="view-list-of-environments"></a>Просмотреть список сред

Просмотрите окружения в рабочей области со [списком ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-), а затем выберите один из них для повторного использования.

```python
from azureml.core import Environment
list("workspace_name")
```

#### <a name="get-environment-by-name"></a>Получить окружение по имени

Кроме того, можно получить конкретную среду по имени и версии.
В следующем коде метод [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) используется для получения версии `1` `ws` среды `myenv` в рабочей области.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Специальная среда для обучения

Чтобы получить среду, используемую для определенного запуска после завершения обучения, используйте метод [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)в классе Run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Обновление существующей среды

При внесении изменений в существующую среду, например при добавлении пакета Python, создается новая версия среды при отправке выполнения, развертывании модели или регистрации среды вручную. Управление версиями позволяет просматривать изменения в среде с течением времени.

Чтобы обновить версию пакета Python существующей среды, укажите точный номер версии для этого пакета. В противном случае Машинное обучение Azure будет повторно использовать существующую среду с версиями пакета при создании среды.

### <a name="debug-the-image-build"></a>Отладка сборки образа

В этом примере используется метод [Build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) для создания среды вручную в качестве образа DOCKER и мониторинга выходных журналов из сборки образа с помощью [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). После этого созданный образ появится в реестре контейнеров Azure Workspace, который полезен для отладки.

```python
from azureml.core import Image
build = env.build()
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>DOCKER и среды

 [Доккерсектион](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) класса машинное обучение Azure `Environments` позволяет настраивать и контролировать сведения о гостевой операционной системе, в которой выполняется обучающий запуск.

Когда вы `enable` закрепляете, служба создает образ DOCKER и создает среду Python с вашими спецификациями в контейнере DOCKER. Это обеспечивает дополнительную изоляцию и воспроизводимость для учебных запусков.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

После сборки образ DOCKER появится в реестре контейнеров Azure, который связан с рабочей областью по умолчанию.  Имя репозитория имеет форму *azureml/azureml_\<\>UUID*. Часть уникального идентификатора (*уууид*) соответствует хэшу, вычисленному на основе конфигурации среды. Это позволяет службе определить, существует ли уже образ, соответствующий данной среде, для повторного использования.

Кроме того, служба автоматически использует один из [базовых образов](https://github.com/Azure/AzureML-Containers)на основе Ubuntu Linux и устанавливает указанные пакеты Python. Базовый образ имеет версии ЦП и GPU, и вы можете указать образ GPU, задав `gpu_support=True`.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"

# Specify GPU image
myenv.docker.gpu_support=True
```

> [!NOTE]
> При указании `environment.python.user_managed_dependencies=False` при использовании пользовательского образа DOCKER служба создает среду Conda в образе и выполняет запуск в этой среде вместо использования библиотек Python, которые могли быть установлены на базовом образе. Задайте для параметра `True` значение, чтобы использовать свои установленные пакеты.

## <a name="using-environments-for-training"></a>Использование сред для обучения

Чтобы отправить обучающий запуск, необходимо объединить среду, [целевой объект вычислений](concept-compute-target.md) и обучающий скрипт Python в конфигурацию запуска. Объект-оболочка, используемый для отправки запусков.

При отправке обучающего запуска создание новой среды может занять несколько минут в зависимости от размера необходимых зависимостей. Окружения кэшируются службой, поэтому при условии, что определение окружения остается неизменным, полная настройка времени будет произведена только один раз.

Ниже приведен пример запуска локального скрипта, где в качестве объекта-оболочки следует использовать [скриптрунконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) .

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.compute_target = "local"

# Attach environment to run config
runconfig.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Чтобы отключить журнал выполнения или запустить моментальные снимки, используйте параметр в `ScriptRunConfig.run_config.history`разделе.

Если вы не укажете среду в конфигурации запуска, служба создаст среду по умолчанию при отправке выполнения.

### <a name="train-with-an-estimator"></a>Обучение с оценщиком

При использовании средства [оценки](how-to-train-ml-models.md) для обучения можно просто отправить экземпляр средства оценки напрямую, так как он уже инкапсулирует среду и целевой объект вычислений.

В следующем примере используется оценщик для обучения с одним узлом с помощью удаленного вычислений для модели scikit-Train и предполагается ранее созданный целевой объект `compute_target` вычислений и `ds`объект хранилища данных.

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

## <a name="using-environments-for-web-service-deployment"></a>Использование сред для развертывания веб-служб

При развертывании модели в качестве веб-службы можно использовать среды. Это позволяет воспроизводимый, подключенный рабочий процесс, позволяющий обучить, тестировать и развертывать модель с помощью одних и тех же библиотек для обучения и вычислений.

Чтобы развернуть веб-службу, объедините среду, вычисление вычисления, Скрипт оценки и зарегистрированную модель в объекте развертывания и [разверните ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-). Дополнительные сведения о [развертывании веб-служб](how-to-deploy-and-where.md).

В этом примере предположим, что вы выполнили обучающий запуск для развертывания этой модели в экземпляре контейнера Azure (ACI). При создании веб-службы файлы модели и оценки монтируются в образ, и в образ добавляется Машинное обучение Azure стек ссылок.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Примеры записных книжек

В этом [примере Записная книжка](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) расширяет концепции и методы, продемонстрированные в этой статье.

## <a name="next-steps"></a>Следующие шаги

* [Учебник. по обучению модели классификации изображений с помощью Службы машинного обучения Azure](tutorial-train-models-with-aml.md). В нем используется управляемый целевой объект вычислений для обучения модели.
* После обучения модели узнайте о [способах и расположениях развертывания моделей](how-to-deploy-and-where.md).
* Просмотрите ссылку на пакет SDK для [класса среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .
