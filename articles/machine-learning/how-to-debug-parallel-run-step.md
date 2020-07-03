---
title: Отладка и устранение неполадок Параллелрунстеп
titleSuffix: Azure Machine Learning
description: Отладка и устранение неполадок Параллелрунстеп в конвейерах машинного обучения в пакете SDK Машинное обучение Azure для Python. Изучите распространенные ошибки для разработки с помощью конвейеров и советы по отладке сценариев до и во время удаленного выполнения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122968"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Отладка и устранение неполадок Параллелрунстеп
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как выполнять отладку и устранение неполадок класса [параллелрунстеп](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) из [пакета SDK машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Локальное тестирование скриптов

См. [раздел тестирование скриптов в локальной](how-to-debug-pipelines.md#testing-scripts-locally) среде для конвейеров машинного обучения. Ваш Параллелрунстеп выполняется как шаг в конвейерах машинного обучения, поэтому один и тот же ответ применяется к обоим.

## <a name="debugging-scripts-from-remote-context"></a>Отладка скриптов из удаленного контекста

Переход от отладки скрипта оценки локально для отладки скрипта оценки в фактическом конвейере может оказаться сложной задачей. Сведения о поиске журналов на портале см. в [разделе конвейеры машинного обучения статьи об отладке скриптов из удаленного контекста](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Сведения в этом разделе также применяются к выполнению параллельных шагов.

Например, файл `70_driver_log.txt` журнала содержит сведения об контроллере, который запускает код шага параллельного выполнения.

Из-за распределенной природы задач параллельного выполнения существуют журналы из нескольких разных источников. Однако создаются два консолидированных файла, которые предоставляют сведения высокого уровня:

- `~/logs/overview.txt`: Этот файл содержит общие сведения о количестве мини-пакетов (которые также называются задачами), созданных до сих пор и количества мини-пакетов, обработанных на данный момент. На этом конце отображается результат задания. Если задание завершилось с ошибкой, отобразится сообщение об ошибке, где можно начать устранение неполадок.

- `~/logs/sys/master.txt`: Этот файл предоставляет главный узел (также известный как Orchestrator) в представлении выполняемого задания. Включает создание задач, мониторинг хода выполнения, результат выполнения.

Журналы, созданные на основе скрипта записи с использованием Ентрискрипт. Logger и инструкций Print, будут найдены в следующих файлах:

- `~/logs/user/<ip_address>/Process-*.txt`: Этот файл содержит журналы, записанные из entry_script с помощью Ентрискрипт. Logger. Он также содержит инструкцию PRINT (stdout) от entry_script.

Если вам необходимо полное понимание того, как каждый узел выполняет скрипт оценки, просмотрите отдельные журналы процесса для каждого узла. Журналы процесса можно найти в `sys/worker` папке, сгруппированной по рабочим узлам:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Этот файл содержит подробные сведения о каждом мини-пакете по мере его выбора или завершения рабочими процессами. Для каждого мини-пакета в этот файл входят:

    - IP-адрес и идентификатор процесса рабочего процесса. 
    - Общее число элементов, число успешно обработанных элементов и число невыполненных элементов.
    - Время начала, длительность, время обработки и время выполнения метода.

Также можно найти сведения об использовании ресурсов процессами для каждого работника. Эти сведения находятся в формате CSV и находятся в папке `~/logs/sys/perf/<ip_address>/`. Для одного узла файлы заданий будут доступны в разделе `~logs/sys/perf`. Например, при проверке использования ресурсов просмотрите следующие файлы:

- `Process-*.csv`: Использование ресурсов для рабочего процесса. 
- `sys.csv`: Журнал каждого узла.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Разделы справки журнал из удаленного контекста из пользовательского скрипта?
Вы можете получить средство ведения журнала из Ентрискрипт, как показано в приведенном ниже примере кода, чтобы журналы отображались в папке **журналы/пользователь** на портале.

**Пример входного скрипта с использованием средства ведения журнала:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Как передать входные данные на стороне, такие как, файл или файлы, содержащие таблицу подстановки, всем моим рабочим работникам?

Создайте объект [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) , содержащий побочный ввод и зарегистрируйтесь в рабочей области. После этого к нему можно получить доступ в скрипте определения (например, в методе init ()) следующим образом:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о пакете [azureml-от участников сообщества-конвейер-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) и [документации](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) по классу параллелрунстеп см. в справочнике по пакету SDK.

* Ознакомьтесь с [дополнительным руководством](tutorial-pipeline-batch-scoring-classification.md) по использованию конвейеров с шагом параллельного выполнения.
