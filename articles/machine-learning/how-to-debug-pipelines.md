---
title: Отладка & устранение неполадок конвейеров машинного обучения
titleSuffix: Azure Machine Learning
description: Отладка конвейеров Машинное обучение Azure в Python. Изучите распространенные ловушки для разработки конвейеров и советы по отладке сценариев до и во время удаленного выполнения. Узнайте, как использовать Visual Studio Code для интерактивной отладки конвейеров машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 4f0eb6aa92dd8999baed6868a159c86d5e7bd0c8
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594659"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Отладка и устранение неполадок в конвейерах машинного обучения
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как выполнять отладку и устранение неполадок в [конвейерах машинного обучения](concept-ml-pipelines.md) в [машинное обучение Azure SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) и [конструкторе машинное обучение Azure (Предварительная версия)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Сведения приведены в следующих руководствах:

* Отладка с помощью пакета SDK для Машинное обучение Azure
* Отладка с помощью конструктора Машинное обучение Azure
* Отладка с использованием Application Insights
* Отладка в интерактивном режиме с помощью Visual Studio Code (VS Code) и Инструменты Python для Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Отладка и устранение неполадок в пакете SDK для Машинное обучение Azure
В следующих разделах представлен обзор распространенных ловушек при построении конвейеров, а также различные стратегии отладки кода, выполняемого в конвейере. При возникновении проблем с получением ожидаемого запуска конвейера используйте следующие советы.

### <a name="testing-scripts-locally"></a>Локальное тестирование скриптов

Одной из наиболее распространенных сбоев в конвейере является то, что присоединенный скрипт (скрипт очистки данных, Скрипт оценки и т. д.) не выполняется должным образом или содержит ошибки времени выполнения в удаленном контексте вычислений, которые трудно отладить в рабочей области в Машинное обучение Azure Studio. 

Сами по себе конвейеры не могут выполняться локально, но выполнение сценариев на локальном компьютере позволяет выполнять отладку быстрее, поскольку не нужно ждать процесса сборки вычислений и среды. Для этого требуется выполнить некоторые действия по разработке:

* Если данные находятся в облачном хранилище данных, необходимо скачать данные и сделать их доступными для скрипта. Использование небольшого примера данных — хороший способ сократить время выполнения и получить отзыв о поведении сценария.
* При попытке имитировать промежуточный шаг конвейера может потребоваться вручную создать типы объектов, которые определенный сценарий ожидает на предыдущем шаге.
* Также потребуется определить собственную среду и реплицировать зависимости, определенные в удаленной среде вычислений.

После установки сценария для запуска в локальной среде гораздо проще выполнять задачи отладки, такие как:

* Присоединение пользовательской конфигурации отладки
* Приостановка выполнения и проверка состояния объекта
* Перехват типов или логических ошибок, которые не будут предоставляться до времени выполнения

> [!TIP] 
> Убедившись в том, что сценарий выполняется должным образом, на следующем шаге выполняется сценарий в одношаговом конвейере, прежде чем пытаться запустить его в конвейере с несколькими шагами.

### <a name="debugging-scripts-from-remote-context"></a>Отладка скриптов из удаленного контекста

Локальное тестирование скриптов — это отличный способ отладки основных фрагментов кода и сложной логики, прежде чем приступить к созданию конвейера, но в какой-то момент вам, скорее всего, придется отлаживать сценарии во время выполнения собственно конвейера, особенно при диагностике поведения, возникающего во время взаимодействия между этапами конвейера. Мы рекомендуем свободно использовать `print()` инструкции в сценариях шагов, чтобы видеть состояние объекта и ожидаемые значения во время удаленного выполнения, аналогично отладке кода JavaScript.

Файл `70_driver_log.txt` журнала содержит: 

* Все печатные инструкции во время выполнения сценария
* Трассировка стека для скрипта 

Чтобы найти этот и другие файлы журнала на портале, сначала щелкните Запуск конвейера в рабочей области.

![Страница списка запусков конвейера](./media/how-to-debug-pipelines/pipelinerun-01.png)

Перейдите на страницу сведений о выполнении конвейера.

![Страница сведений о запуске конвейера](./media/how-to-debug-pipelines/pipelinerun-02.png)

Щелкните модуль для конкретного шага. Перейдите на вкладку **журналы** . Другие журналы включают сведения о процессе сборки образа среды и сценарии подготовки к выполнению.

![Вкладка журнала страницы сведений о запуске конвейера](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Запуски для *опубликованных конвейеров* можно найти на вкладке " **конечные точки** " в рабочей области. Запуски для *неопубликованных конвейеров* можно найти в **экспериментах** или **конвейерах**.

### <a name="troubleshooting-tips"></a>Советы по устранению неполадок

В следующей таблице приведены распространенные проблемы во время разработки конвейера с потенциальными решениями.

| Проблема | Возможное решение |
|--|--|
| Не удалось передать данные в `PipelineData` каталог | Убедитесь, что в скрипте создан каталог, который соответствует расположению выходных данных шага в конвейере. В большинстве случаев входной аргумент определяет выходной каталог, а затем создает каталог явным образом. Используйте `os.makedirs(args.output_dir, exist_ok=True)` для создания выходного каталога. См. [руководство](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) по примеру сценария оценки, в котором показан этот шаблон разработки. |
| Ошибки зависимостей | Если вы разработали и тестировали сценарии локально, но нашли проблемы с зависимостями при выполнении на удаленном вычислении в конвейере, убедитесь, что зависимости и версии среды вычислений соответствуют вашей тестовой среде. (См. раздел [Создание среды, кэширование и повторное использование](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Неоднозначные ошибки с целевыми объектами вычислений | Удаление и повторное создание целевых объектов вычислений может решить определенные проблемы с целевыми объектами вычислений. |
| Конвейер не использует повторно шаги | Повторное использование шага включено по умолчанию, но убедитесь, что вы не отключили его на этапе конвейера. Если повторное использование отключено `allow_reuse` , параметр на шаге будет установлен в `False`значение. |
| Конвейер перезапускается без необходимости | Чтобы обеспечить повторный запуск шагов только при изменении базовых данных или скриптов, установите соединение между каталогами для каждого шага. Если один и тот же исходный каталог используется для нескольких шагов, может возникнуть ненужное повторное использование. Используйте `source_directory` параметр для объекта шага конвейера, чтобы указать на свой изолированный каталог для этого шага, и убедитесь, что вы не `source_directory` используете один и тот же путь для нескольких шагов. |

### <a name="logging-options-and-behavior"></a>Параметры ведения журнала и поведение

В таблице ниже приведены сведения о различных параметрах отладки для конвейеров. Он не является исчерпывающим списком, так как существуют другие варианты, кроме только Машинное обучение Azure, Python и Опенценсус, показанных здесь.

| Библиотека                    | Type   | Пример                                                          | Назначение                                  | Ресурсы                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| пакет SDK для Машинного обучения Azure; | Метрика | `run.log(name, val)`                                             | Пользовательский интерфейс портала Машинное обучение Azure             | [Как отвести эксперименты](how-to-track-experiments.md#available-metrics-to-track)<br>[класс azureml. Core. Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Печать и ведение журнала Python    | Журнал    | `print(val)`<br>`logging.info(message)`                          | Журналы драйверов, конструктор Машинное обучение Azure | [Как отвести эксперименты](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Ведение журнала Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Опенценсус Python          | Журнал    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights трассировки                | [Отладка конвейеров в Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Агенты OpenCensus Azure Monitor Exporter](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Cookbook ведения журнала Python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Пример параметров ведения журнала

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Отладка и устранение неполадок в конструкторе Машинное обучение Azure (Предварительная версия)

В этом разделе приводятся общие сведения об устранении неполадок конвейеров в конструкторе. Для конвейеров, созданных в конструкторе, файл **70_driver_log** можно найти либо на странице Создание, либо на странице сведения о выполнении конвейера.

### <a name="get-logs-from-the-authoring-page"></a>Получение журналов со страницы "Создание и Настройка"

При отправке выполнения конвейера и остаться на странице Создание и настройка можно найти файлы журналов, созданные для каждого модуля по мере завершения выполнения каждого модуля.

1. Выберите модуль, который завершил выполнение на холсте разработки.
1. На правой панели модуля перейдите на вкладку **выходные данные + журналы** .
1. Разверните правую панель и выберите **70_driver_log. txt** , чтобы просмотреть файл в браузере. Кроме того, можно скачать журналы локально.

    ![Развернутая область вывода в конструкторе](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Получение журналов из запусков конвейера

Файлы журналов для конкретных запусков можно также найти на странице сведений о запуске конвейера, которую можно найти в разделе **конвейеры** или **эксперименты** в студии.

1. Выберите Запуск конвейера, созданный в конструкторе.

    ![Страница выполнения конвейера](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Выберите модуль в области просмотра.
1. На правой панели модуля перейдите на вкладку **выходные данные + журналы** .
1. Разверните правую панель, чтобы просмотреть файл **70_driver_log. txt** в браузере, или выберите файл, чтобы скачать журналы локально.

> [!IMPORTANT]
> Чтобы обновить конвейер со страницы сведения о выполнении конвейера, необходимо **клонировать** выполнение конвейера в новый черновик конвейера. Запуск конвейера — это моментальный снимок конвейера. Он аналогичен файлу журнала и не может быть изменен. 

## <a name="debug-and-troubleshoot-in-application-insights"></a>Отладка и устранение неполадок в Application Insights
Дополнительные сведения об использовании библиотеки Опенценсус Python таким образом см. в разделе [Отладка и устранение неполадок конвейеров машинного обучения в Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Отладка и устранение неполадок в Visual Studio Code

В некоторых случаях может потребоваться интерактивно отлаживать код Python, используемый в конвейере машинного обучения. С помощью Visual Studio Code (VS Code) и Инструменты Python для Visual Studio (PTVSD) можно присоединяться к коду, как он выполняется в среде обучения.

### <a name="prerequisites"></a>Предварительные условия

* __Рабочая область машинное обучение Azure__ , настроенная для использования __виртуальной сети Azure__.
* __Конвейер машинное обучение Azure__ , использующий скрипты Python в рамках этапов конвейера. Например, Писонскриптстеп.
* Машинное обучение Azureного кластерного кластера, который находится __в виртуальной сети__ и __используется конвейером для обучения__.
* __Среда разработки__ , которая находится __в виртуальной сети__. Среда разработки может быть одной из следующих:

    * Виртуальная машина Azure в виртуальной сети
    * Вычислительный экземпляр виртуальной машины записной книжки в виртуальной сети
    * Клиентский компьютер, подключенный к виртуальной сети через виртуальную частную сеть (VPN).

Дополнительные сведения об использовании виртуальной сети Azure с Машинное обучение Azure см. в статье [Защита заданий экспериментирования и вывода машинного обучения Azure в виртуальной сети Azure](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Принцип работы

Этапы конвейера машинного обучения запускают скрипты Python. Эти скрипты изменяются для выполнения следующих действий.
    
1. Регистрировать IP-адрес узла, на котором они выполняются. Используйте IP-адрес для подключения отладчика к сценарию.

2. Запустите компонент отладки PTVSD и дождитесь подключения отладчика.

3. В среде разработки вы отслеживаете журналы, созданные процессом обучения, чтобы найти IP-адрес, на котором выполняется сценарий.

4. Вы указываете VS Code IP-адреса для подключения отладчика к с помощью `launch.json` файла.

5. Вы подключаете отладчик и интерактивно пройдите по сценарию.

### <a name="configure-python-scripts"></a>Настройка скриптов Python

Чтобы включить отладку, внесите следующие изменения в скрипты Python, используемые шагами в конвейере ML:

1. Добавьте следующие операторы импорта:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Добавьте следующие аргументы. Эти аргументы позволяют включить отладчик по мере необходимости и задать время ожидания для подключения отладчика:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Добавьте следующие инструкции. Эти инструкции загружают текущий контекст выполнения, чтобы можно было зарегистрировать IP-адрес узла, на котором выполняется код:

    ```python
    global run
    run = Run.get_context()
    ```

1. Добавьте `if` инструкцию, запускающую PTVSD, и дождитесь присоединения отладчика. Если отладчик не подключается до истечения времени ожидания, сценарий продолжится в нормальном режиме.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

В следующем примере кода Python показан базовый `train.py` файл, позволяющий выполнять отладку:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Настройка конвейера машинного обучения

Чтобы предоставить пакеты Python, необходимые для запуска PTVSD и получения контекста выполнения, создайте среду и задайте `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`. Измените версию пакета SDK, чтобы она соответствовала используемой. В следующем фрагменте кода показано, как создать среду.

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

В разделе [Настройка скриптов Python](#configure-python-scripts) в скрипты, используемые этапами КОНВЕЙЕРа машинного обучения, были добавлены два новых аргумента. В следующем фрагменте кода показано, как использовать эти аргументы, чтобы включить отладку для компонента и установить время ожидания. Здесь также показано, как использовать созданную ранее среду, задав `runconfig=run_config`следующие параметры.

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

При выполнении конвейера каждый шаг создает дочерний запуск. Если включена отладка, то измененный сценарий записывает в журнал сведения, аналогичные следующему `70_driver_log.txt` тексту в для дочернего запуска:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Сохраните `ip_address` значение. Они будут использоваться в следующем разделе.

> [!TIP]
> Вы также можете найти IP-адрес из журналов выполнения для дочернего выполнения этого шага конвейера. Дополнительные сведения о просмотре этих сведений см. в статье [мониторинг запусков и метрик эксперимента машинного обучения Azure](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Настройка среды разработки

1. Чтобы установить Инструменты Python для Visual Studio (PTVSD) в среде разработки VS Code, используйте следующую команду:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Дополнительные сведения об использовании PTVSD с VS Code см. в разделе [Удаленная отладка](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Чтобы настроить VS Code для взаимодействия с Машинное обучение Azure вычислений, в котором работает отладчик, создайте новую конфигурацию отладки:

    1. В VS Code откройте меню __Отладка__ и выберите пункт __Открыть конфигурации__. Откроется файл с именем __Launch. JSON__ .

    1. В файле __Launch. JSON__ найдите строку, содержащую `"configurations": [`и вставьте следующий текст после него. Измените `"host": "10.3.0.5"` запись на IP-адрес, возвращенный в журналах из предыдущего раздела. Измените `"localRoot": "${workspaceFolder}/code/step"` запись на локальный каталог, содержащий копию отлаживаемого скрипта:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Если в разделе конфигурации уже есть другие записи, добавьте запятую (,) после вставленного кода.

        > [!TIP]
        > Рекомендуется размещать ресурсы для сценариев в отдельных каталогах, поэтому это указывает на то `localRoot` , что примерное значение ссылается `/code/step1`на.
        >
        > При отладке нескольких скриптов в разных каталогах создайте отдельный раздел конфигурации для каждого скрипта.

    1. Сохраните файл __Launch. JSON__ .

### <a name="connect-the-debugger"></a>Подключение отладчика

1. Откройте VS Code и откройте локальную копию скрипта.
2. Задайте точки останова, где сценарий должен останавливаться после присоединения.
3. Пока в дочернем процессе выполняется сценарий, а `Timeout for debug connection` в журналах отображается, используйте клавишу F5 или выберите __Отладка__. При появлении запроса выберите конфигурацию __удаленной отладки машинное обучение Azure вычисление: Удаленная отладка__ . Можно также выбрать значок отладки на боковой панели, в раскрывающемся меню Отладка выберите __машинное обучение Azure: Удаленная отладка__ , а затем с помощью зеленой стрелки присоединить отладчик.

    На этом этапе VS Code подключается к PTVSD на кластерном узле и останавливается в точке останова, заданной ранее. Теперь можно выполнять код по мере выполнения, просматривать переменные и т. д.

    > [!NOTE]
    > Если в журнале отображается запись `Debugger attached = False`, то время ожидания истекает, и скрипт продолжает работу без отладчика. Снова отправьте конвейер и Подключите отладчик после `Timeout for debug connection` сообщения и до истечения времени ожидания.

## <a name="next-steps"></a>Дальнейшие действия

* Обратитесь к Справочнику по пакету SDK, чтобы получить справку по пакету [azureml-конвейеры-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) и пакету [azureml-конвейеры-этапов](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* См. список [исключений и кодов ошибок конструктора](algorithm-module-reference/designer-error-codes.md).
