---
title: Отладка и устранение неполадок в конвейерах машинного обучения
titleSuffix: Azure Machine Learning
description: Отладить и устранить неполадки конвейеров машинного обучения в Azure Machine Learning SDK для Python. Узнайте общие подводные камни для разработки конвейеров и советы, которые помогут вам отладить скрипты до и во время удаленного выполнения. Узнайте, как использовать Visual Studio Code для интерактивного отладки конвейеров машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: 578e935ee742ad476aeafb53670f0a92035249e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064081"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Отладка и устранение неполадок в конвейерах машинного обучения
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как отладить и устранить [неполадки конвейеров машинного обучения](concept-ml-pipelines.md) в [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) и [дизайнере машинного обучения Azure (предварительный просмотр)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Предоставляется информация о том, как:

* Отладить с помощью SDK машинного обучения Azure
* Отладить с помощью дизайнера машинного обучения Azure
* Отладить с помощью приложений Исследования
* Отладить интерактивно с помощью визуального кода студии (VS Code) и Python Tools for Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Ошибка и устранение неполадок в SDK по изучению машин Azure
Следующие разделы содержат общее представление о подводных камнях при строительстве трубопроводов и различных стратегий отладки кода, работающего в конвейере. Используйте следующие советы, когда у вас возникли проблемы с запуском конвейера, как ожидалось.

### <a name="testing-scripts-locally"></a>Тестирование сценариев локально

Одним из наиболее распространенных сбоев в конвейере является то, что прикрепленный скрипт (скрипт очистки данных, скрипт скоринга и т.д.) не работает по назначению или содержит ошибки в режиме выполнения, которые трудно отладить в рабочем пространстве в Azure Machine Учебная студия. 

Сами трубопроводы не могут быть запущены локально, но запуск скриптов в изоляции на локальной машине позволяет отладить быстрее, потому что вам не придется ждать процесса построения вычислений и среды. Для этого требуется определенная работа по разработке:

* Если данные хранятся в облачном хранилище данных, необходимо загрузить данные и сделать их доступными для скрипта. Использование небольшой выборки данных является хорошим способом сократить время выполнения и быстро получить обратную связь о поведении скрипта
* Если вы пытаетесь смоделировать промежуточный шаг конвейера, возможно, потребуется вручную построить типы объектов, которые ожидает конкретный сценарий от предыдущего шага
* Вам также нужно будет определить собственную среду и воспроизвести зависимости, определенные в среде удаленных вычислений

Если у вас есть настройка скрипта для работы на локальной среде, гораздо проще сделать отладку задач, таких как:

* Присоединение пользовательской конфигурации отладки
* Приостановка выполнения и проверки состояния объекта
* Поймать тип или логические ошибки, которые не будут выставлены до времени выполнения

> [!TIP] 
> Как только вы сможете проверить, работает ли ваш скрипт, следующим шагом является запуск скрипта в одноступенчатом конвейере, прежде чем пытаться запустить его в конвейер с несколькими шагами.

### <a name="debugging-scripts-from-remote-context"></a>Отладка скриптов из удаленного контекста

Тестирование сценариев локально является отличным способом отладить основные фрагменты кода и сложную логику, прежде чем начать строительство конвейера, но в какой-то момент вам, скорее всего, потребуется отладить скрипты во время самого запуска конвейера, особенно при диагностике поведения, которое происходит во время взаимодействия между шагами конвейера. Мы рекомендуем использовать `print()` операторы в скриптах шагов, чтобы можно было видеть состояние объектов и ожидаемые значения во время удаленного выполнения, подобно тому, как отладить код JavaScript.

Файл `70_driver_log.txt` журнала содержит: 

* Все печатные операторы во время выполнения сценария
* След стека для скрипта 

Чтобы найти этот и другие файлы журнала на портале, сначала нажмите на конвейер, запущенный в рабочем пространстве.

![Страница списка запуска трубопровода](./media/how-to-debug-pipelines/pipelinerun-01.png)

Перейдите к странице детального выполнения конвейера.

![Страница детального проработки трубопровода](./media/how-to-debug-pipelines/pipelinerun-02.png)

Нажмите на модуль для конкретного шага. Перейдите на вкладку **Журналы.** Другие журналы включают информацию о процессе построения изображений среды и сценариях подготовки шагов.

![Вкладка журнала страницы детального запуска трубопровода](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Запуски *для опубликованных конвейеров* можно найти во вкладке **Endpoints** в рабочем пространстве. Запуски для *неопубликованных трубопроводов* можно найти в **экспериментах** или **трубопроводах.**

### <a name="troubleshooting-tips"></a>Советы по устранению неполадок

Следующая таблица содержит общие проблемы при разработке трубопровода с потенциальными решениями.

| Проблема | Возможное решение |
|--|--|
| Невозможно передать данные в `PipelineData` каталог | Убедитесь, что вы создали каталог в скрипте, который соответствует месту, где ваш конвейер ожидает данные вывода шагов. В большинстве случаев аргумент ввода определяет каталог вывода, а затем создает каталог явно. Используется `os.makedirs(args.output_dir, exist_ok=True)` для создания каталога вывода. Смотрите [учебник](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) для примера сценария скоринга, который показывает этот шаблон проектирования. |
| Ошибки зависимости | Если вы разработали и протестировали сценарии локально, но обнаружите проблемы зависимости при запуске удаленного вычисления в конвейере, убедитесь, что зависимости и версии среды вычислений соответствуют вашей тестовой среде. (См. [Здание среды, кэширование и повторное использование](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Неоднозначные ошибки с целевыми показателями вычислений | Удаление и повторное создание вычислительных целей может решить определенные проблемы с помощью вычислительных целей. |
| Трубопровод не используется повторно | Повторное использование шага включено по умолчанию, но гарантирует, что вы не отключили его в шаге конвейера. Если повторное использование `allow_reuse` отключено, параметр в `False`шаге будет установлен. |
| Трубопровод перезапускается неоправданно | Чтобы убедиться, что шаги будут повторены только при изменении базовых данных или скриптов, отсоедините каталоги для каждого шага. Если вы используете один и тот же исходный каталог для нескольких шагов, вы можете столкнуться с ненужными повторами. Используйте `source_directory` параметр на объекте шага конвейера, чтобы указать на изолированный каталог `source_directory` для этого шага и убедиться, что вы не используете один и тот же путь для нескольких шагов. |

### <a name="logging-options-and-behavior"></a>Параметры и поведение журналов

В таблице ниже приводится информация о различных вариантах отладки конвейеров. Это не исчерпывающий список, так как существуют и другие варианты, кроме показанных здесь только Azure Machine Learning, Python и OpenCensus.

| Библиотека                    | Тип   | Пример                                                          | Назначение                                  | Ресурсы                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| пакет SDK для Машинного обучения Azure; | Метрика | `run.log(name, val)`                                             | UI портала машинного обучения Azure             | [Как отслеживать эксперименты](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml.core.Run класса](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Печать/лесозаготовки python    | Журнал    | `print(val)`<br>`logging.info(message)`                          | Драйвер журналы, Дизайнер машинного обучения Azure | [Как отслеживать эксперименты](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Регистрация python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Открытый питон переписи          | Журнал    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Приложение Исследования - следы                | [Отладка конвейеров в Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Агенты OpenCensus Azure Monitor Exporter](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python лесозаготовки поваренная книга](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Пример параметров журналов

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

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Отладить и устранить неполадки в дизайнере машинного обучения Azure (предварительный просмотр)

В этом разделе представлен обзор того, как устранить неполадки в конструкторе.
Для конвейеров, созданных в конструкторе, можно найти **файлы журнала** на странице авторов или на странице детального выполнения конвейера.

### <a name="access-logs-from-the-authoring-page"></a>Журналы доступа со страницы авторов

При отправке запуска конвейера и пребывания на странице авторов можно найти файлы журнала, созданные для каждого модуля.

1. Выберите любой модуль в автор-холсте.
1. В правом стеле модуля перейдите на вкладку **Outputs'ogs.**
1. Выберите файл журнала`70_driver_log.txt`

    ![Авторство журналов модулей страниц](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Журналы доступа из проводов

Вы также можете найти файлы журнала конкретных запусков в разделе подробной проработки выполнения конвейера в **разделах Трубопроводов** или **Экспериментов.**

1. Выберите запуск конвейера, созданный в проекте.
    ![Страница запуска трубопровода](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Выберите любой модуль в панели предварительного просмотра.
1. В правом стеле модуля перейдите на вкладку **Outputs'ogs.**
1. Выберите файл журнала`70_driver_log.txt`

## <a name="debug-and-troubleshoot-in-application-insights"></a>Ошибка и устранение неполадок в application Insights
Для получения дополнительной информации об использовании библиотеки OpenCensus Python таким образом, см. [Debug and troubleshoot machine learning pipelines in Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Ошибка и устранение неполадок в коде Visual Studio

В некоторых случаях может потребоваться интерактивно отладить код Python, используемый в конвейере ML. Используя Visual Studio Code (VS Code) и Python Tools for Visual Studio (PTVSD), вы можете прикрепить к коду, как он работает в учебной среде.

### <a name="prerequisites"></a>Предварительные требования

* __Рабочее пространство Для машинного обучения Azure,__ настроенное на использование __виртуальной сети Azure.__
* __Конвейер машинного обучения Azure,__ который использует скрипты Python как часть шагов конвейера. Например, PythonScriptStep.
* Кластер Azure Machine Learning Compute, который находится __в виртуальной сети__ и __используется конвейером для обучения.__
* __Среда разработки,__ которая находится __в виртуальной сети.__ Среда разработки может быть одной из следующих:

    * Виртуальная машина Azure в виртуальной сети
    * Вычислительный экземпляр ноутбука VM в виртуальной сети
    * Клиентская машина, подключенная к виртуальной сети виртуальной частной сетью (VPN).

Для получения дополнительной информации об использовании виртуальной сети Azure с помощью машинного обучения Azure [см.](how-to-enable-virtual-network.md)

### <a name="how-it-works"></a>Принцип работы

Ваши шаги конвейера ML запускают скрипты Python. Эти скрипты изменяются для выполнения следующих действий:
    
1. Зарегистрируй IP-адрес узла, на который он работает. Для подключения отладчика к скрипту используется IP-адрес.

2. Запустите компонент отладки PTVSD и дождитесь подключения отладчика.

3. Из среды разработки вы отслеживаете журналы, созданные учебным процессом, чтобы найти IP-адрес, на котором работает скрипт.

4. Вы сообщаете VS Code IP-адрес, чтобы подключить `launch.json` отладчика с помощью файла.

5. Вы прикрепите отладчик и интерактивно шаг через сценарий.

### <a name="configure-python-scripts"></a>Настройка скриптов Python

Для обеспечения отладки вносите следующие изменения в скрипт Python (ы), используемый шагами в конвейере ML:

1. Добавьте следующие заявления об импорте:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Добавьте следующие аргументы. Эти аргументы позволяют включить отладчик по мере необходимости и установить тайм-аут для крепления отладчика:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Добавьте следующие инструкции. Эти операторы загружают текущий контекст выполнения, чтобы можно было войти ip-адрес узла, на который работает код:

    ```python
    global run
    run = Run.get_context()
    ```

1. Добавьте `if` заявление, которое запускает сяртор PTVSD и ждет присоединения отладчика. Если перед тайм-аутом нет отладчика, скрипт продолжает работать в обычном режиме.

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

Следующий пример Python `train.py` показывает базовый файл, который позволяет отладку:

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

### <a name="configure-ml-pipeline"></a>Настройка конвейера ML

Для обеспечения пакетов Python необходимо запустить PTVSD и получить контекст `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`запуска, создать среду и установить. Измените версию SDK в соответствии с той, которая вы используете. Следующий фрагмент кода демонстрирует, как создать среду:

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

В разделе ["Настройка python"](#configure-python-scripts) к скриптам, используемым на этапах конвейера ML, были добавлены два новых аргумента. Следующий фрагмент кода демонстрирует, как использовать эти аргументы для отладки компонента и установки тайм-аута. Он также демонстрирует, как использовать среду, `runconfig=run_config`созданную ранее, установив:

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

При запуске конвейера каждый шаг создает запуск ребенка. При включении отладки измененный скрипт регистрирует информацию, похожую `70_driver_log.txt` на следующий текст в запущенном для ребенка:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Сохранить `ip_address` значение. Они будут использоваться в следующем разделе.

> [!TIP]
> Вы также можете найти IP-адрес из журналов выполнения для ребенка, запускаемого для этого шага конвейера. Для получения дополнительной информации о просмотре этой [информации](how-to-track-experiments.md)см.

### <a name="configure-development-environment"></a>Настройка среды разработки

1. Для установки Python Tools for Visual Studio (PTVSD) в среде разработки VS Code используйте следующую команду:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Для получения дополнительной информации об использовании PTVSD с VS Code [см.](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)

1. Для настройки VS Code для связи с компьютером Azure Machine Learning, который работает с отладчиком, создайте новую конфигурацию отладки:

    1. Из VS Code выберите меню __Debug,__ а затем выберите __открытые конфигурации.__ Открывается файл под названием __launch.json.__

    1. В файле __launch.json__ найдите `"configurations": [`строку, содержащую ее, и вставьте следующий текст после нее. Измените `"host": "10.3.0.5"` запись на IP-адрес, возвращенный в журналы из предыдущего раздела. Измените `"localRoot": "${workspaceFolder}/code/step"` запись в локальном каталоге, содержащем копию отладки скрипта:

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
        > Если в разделе конфигураций уже есть другие записи, добавьте запятую ()) после вставленного кода.

        > [!TIP]
        > Наилучшая практика заключается в том, чтобы сохранить ресурсы `localRoot` для скриптов в отдельных каталогах, поэтому пример значения ссылки `/code/step1`.
        >
        > Если вы отлажаете несколько скриптов, в разных каталогах создайте отдельный раздел конфигурации для каждого скрипта.

    1. Сохранить файл __launch.json.__

### <a name="connect-the-debugger"></a>Подключение отладчика

1. Откройте VS Code и откройте локальную копию скрипта.
2. Установите точки разрыва, где вы хотите, чтобы сценарий остановился после присоединения.
3. Во время выполнения скрипта и `Timeout for debug connection` отображения в журналах, используйте ключ F5 или выберите __Debug.__ При запросе выберите __Azure Machine Learning Compute: конфигурация удаленного отладки.__ Вы также можете выбрать значок отладки из боковой панели, __Azure Machine Learning: удаленная запись отладки__ из меню отладки, а затем использовать зеленую стрелку, чтобы прикрепить отладчик.

    На этом этапе VS Code подключается к PTVSD на вычислительном узлах и останавливается в точке разрыва, установленной ранее. Теперь вы можете пройти через код, как он работает, просматривать переменные и т.д.

    > [!NOTE]
    > Если журнал отображает запись `Debugger attached = False`с указанием, то тайм-аут истек, и скрипт продолжается без отладчика. Отправьте конвейер снова и подключите `Timeout for debug connection` отладчика после сообщения, и до истечения тайм-аута.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с справкой SDK о помощи с пакетом [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) и пакетом [шагов лазурных конвейеров.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

* Ознакомьтесь со списком [исключений конструкторов и кодов ошибок.](algorithm-module-reference/designer-error-codes.md)
