---
title: Отладить и устранить неполадки ParallelRunStep
titleSuffix: Azure Machine Learning
description: Отладить и устранить неполадки ParallelRunStep в конвейерах машинного обучения в SDK Для Python. Узнайте общие подводные камни для разработки с конвейерами и советы, которые помогут вам отладить скрипты до и во время удаленного выполнения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122968"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Отладить и устранить неполадки ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как отладить и устранить неполадки в классе [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) из [SDK Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

## <a name="testing-scripts-locally"></a>Тестирование сценариев локально

Ознакомьтесь с [локальным разделом «Сценарии тестирования»](how-to-debug-pipelines.md#testing-scripts-locally) для конвейеров машинного обучения. Ваш ParallelRunStep выполняется как шаг в конвейерах ML, поэтому один и тот же ответ применим к обоим.

## <a name="debugging-scripts-from-remote-context"></a>Отладка скриптов из удаленного контекста

Переход от локальной отладки сценария скоринга к отладке сценария скоринга в фактическом конвейере может быть трудным скачком. Для получения информации о поиске журналов на портале [раздел конвейеров машинного обучения, посвященный отладке скриптов из удаленного контекста.](how-to-debug-pipelines.md#debugging-scripts-from-remote-context) Информация в этом разделе также применяется к параллельному этапу выполнения.

Например, файл `70_driver_log.txt` журнала содержит информацию от контроллера, который запускает параллельный код шага запуска.

Из-за распределенного характера параллельных заданий, есть журналы из нескольких различных источников. Тем не менее, создаются два сводных файла, которые предоставляют информацию высокого уровня:

- `~/logs/overview.txt`: Этот файл предоставляет информацию высокого уровня о количестве мини-пакетов (также известных как задачи), созданных до сих пор, и количестве мини-пакетов, обработанных до сих пор. На этом конце он показывает результат работы. Если задание не удалось, оно покажет сообщение об ошибке и с чего начать устранение неполадок.

- `~/logs/sys/master.txt`: Этот файл предоставляет мастер-узла (также известный как оркестратор) представление работаза. Включает в себя создание задач, мониторинг хода, результат выполнения.

Логи, созданные по сценарию входа с помощью Записи EntryScript.logger и печатных инструкций, будут найдены в следующих файлах:

- `~/logs/user/<ip_address>/Process-*.txt`: Этот файл содержит журналы, написанные из entry_script с помощью EntryScript.logger. Он также содержит заявление печати (stdout) от entry_script.

Если вам нужно полное понимание того, как каждый узла выполняется сценарий оценки, посмотрите на отдельные журналы процессов для каждого узла. Журналы процессов можно найти `sys/worker` в папке, сгруппированной рабочими узлами:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Этот файл предоставляет подробную информацию о каждой мини-пакет, как он взял или завершена работником. Для каждого мини-пакета этот файл включает в себя:

    - IP-адрес и PID рабочего процесса. 
    - Общее количество элементов, успешно обработанные элементы подсчитываются и не удаётся.
    - Время начала, продолжительность, время процесса и время выполнения метода.

Вы также можете найти информацию об использовании ресурсов процессов для каждого работника. Эта информация находится в формате `~/logs/sys/perf/<ip_address>/`CSV и находится по адресу . Для одного узла файлы задания `~logs/sys/perf`будут доступны под . Например, при проверке использования ресурсов смотрите следующие файлы:

- `Process-*.csv`: Использование ресурсов процесса рабочего процесса. 
- `sys.csv`: Входной журнал узлов.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Как войти в свой пользовательский скрипт из удаленного контекста?
Вы можете получить регистратор из EntryScript, как показано ниже пример кода, чтобы журналы отображаются в **журналах / пользователь** папке на портале.

**Скрипт входа образца с помощью регистратора:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Как я могу передать всем моим работникам ввод иной вход, такой как файл или файл (ы), содержащий таблицу поиска?

Создайте объект [Dataset,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) содержащий боковой вход, и зарегистрируйтесь в рабочем пространстве. После этого вы можете получить к нему доступ в сценарии выводов (например, в методе init() следующим образом:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с справкой по SDK с пакетом [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) и [документацией](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) для класса ParallelRunStep.

* Следуйте [передовому учебнику](tutorial-pipeline-batch-scoring-classification.md) по использованию конвейеров с параллельным шагом запуска.
