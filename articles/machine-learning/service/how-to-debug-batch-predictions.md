---
title: Отладка и устранение неполадок Параллелрунстеп
titleSuffix: Azure Machine Learning
description: Отладка и устранение неполадок конвейеров машинного обучения в пакете SDK для Машинное обучение Azure для Python. Изучите распространенные ошибки для разработки с помощью конвейеров и советы по отладке сценариев до и во время удаленного выполнения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: db17dca1a859807f4d92b4953ec0e2785a3a4160
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852332"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Отладка и устранение неполадок с помощью Параллелрун
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как выполнять отладку и устранение неполадок класса [параллелрунстеп](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) из [пакета SDK машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Локальное тестирование скриптов

См. [раздел тестирование скриптов в локальной](how-to-debug-pipelines.md#testing-scripts-locally) среде для конвейеров машинного обучения. Ваш Параллелрунстеп выполняется как шаг в конвейерах машинного обучения, поэтому один и тот же ответ применяется к обоим.

## <a name="debugging-scripts-from-remote-context"></a>Отладка скриптов из удаленного контекста

Переход от отладки скрипта оценки локально для отладки скрипта оценки в фактическом конвейере может оказаться сложной задачей. Сведения о поиске журналов на портале см. в [разделе конвейеры машинного обучения статьи об отладке скриптов из удаленного контекста](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Сведения в этом разделе также применяются к выполнению вывода пакета.

Например, файл журнала `70_driver_log.txt` также содержит: 

* Все печатные инструкции во время выполнения скрипта.
* Трассировка стека скрипта.

Из-за распределенного характера заданий вывода пакетов есть журналы из нескольких разных источников. Однако создаются два консолидированных файла, которые предоставляют сведения высокого уровня:

- `~/logs/overview.txt`: этот файл содержит общие сведения о количестве мини-пакетов (которые также называются задачами), созданных до сих пор, и количестве мини-пакетов, обработанных на данный момент. На этом конце отображается результат задания. Если задание завершилось с ошибкой, отобразится сообщение об ошибке, где можно начать устранение неполадок.

- `~/logs/master.txt`: этот файл предоставляет главный узел (также известный как Orchestrator) в представлении выполняемого задания. Включает в себя создание задачи, мониторинг хода выполнения, результат запуска.

Если вам необходимо полное понимание того, как каждый узел выполняет скрипт оценки, просмотрите отдельные журналы процесса для каждого узла. Журналы процесса можно найти в папке `worker`, сгруппированной по рабочим узлам.

- `~/logs/worker/<ip_address>/Process-*.txt`: этот файл содержит подробные сведения о каждом мини-пакете по мере его выбора или завершения рабочими процессами. Для каждого мини-пакета в этот файл входят:

    - IP-адрес и идентификатор процесса рабочего процесса. 
    - Общее число элементов и число успешно обработанных элементов. 
    - Время начала и окончания в настенном часовом времени (`start1` и `end1`). 
    - Время начала и окончания затраченного времени процессора (`start2` и `end2`). 

Также можно найти сведения об использовании ресурсов процессами для каждого работника. Эти сведения находятся в формате CSV и находятся по адресу `~/logs/performance/<ip_address>/`. Например, при проверке использования ресурсов просмотрите следующие файлы:

- `process_resource_monitor_<ip>_<pid>.csv`: использование ресурсов для рабочего процесса. 
- `sys_resource_monitor_<ip>.csv`: журнал каждого узла.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Разделы справки журнал из удаленного контекста из пользовательского скрипта?
Чтобы сделать журналы отображаемыми в папке **logs/Users** на портале, можно настроить средство ведения журнала с приведенными ниже шагами.
1. Сохраните первый раздел кода ниже в файле entry_script_helper. корректировки и вставьте его в ту же папку, что и скрипт записи. Этот класс получает путь внутри Амлкомпуте. Для локального теста можно изменить `get_working_dir()`, чтобы он возвращал локальную папку.
2. Настройка средства ведения журнала в методе `init()` и его использование. В примере ниже приведен второй раздел кода. 

**entry_script_helper. корректировка:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**Пример входного скрипта с использованием средства ведения журнала:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о пакете [azureml-от участников сообщества-конвейер-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) и [документации](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) по классу параллелрунстеп см. в справочнике по пакету SDK.

* Ознакомьтесь с [дополнительным руководством](tutorial-pipeline-batch-scoring-classification.md) по использованию конвейеров для пакетной оценки.
