---
title: Отладка и устранение неполадок ParallelRunStep
titleSuffix: Azure Machine Learning
description: Отладка и устранение неполадок ParallelRunStep в конвейерах машинного обучения в пакете SDK решения "Машинное обучение Azure" для Python. Изучите распространенные ошибки для разработки с помощью конвейеров и рекомендации по отладке сценариев до и во время удаленного выполнения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: c4e2777f59bab8d7d874019004bff2e30395ab1d
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723482"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Отладка и устранение неполадок ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как выполнять отладку и устранение неполадок класса [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) из [пакета SDL решения "Машинное обучение Azure"](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Локальное тестирование сценариев

См. раздел [Локальное тестирование сценариев](how-to-debug-pipelines.md#testing-scripts-locally) для конвейеров машинного обучения. Ваш класс ParallelRunStep выполняется как этап в конвейерах машинного обучения, поэтому в обоих случаях применимы одни и те же решения.

## <a name="debugging-scripts-from-remote-context"></a>Отладка сценариев из удаленного контекста

Переход от локальной отладки сценария оценки к отладке сценария оценки в фактическом конвейере может оказаться сложной задачей. Сведения о поиске журналов на портале см. в разделе о [конвейерах машинного обучения для сценариев отладки из удаленного контента](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Сведения в этом разделе также применимы к классу ParallelRunStep.

Например, файл журнала `70_driver_log.txt` содержит сведения о контроллере, который запускает код ParallelRunStep.

Из-за того, что задания ParallelRunStep имеют распределенный характер, журналы могут поступать из нескольких разных источников. Однако создается два консолидированных файла, которые предоставляют общие сведения.

- `~/logs/overview.txt`: Этот файл содержит общие сведения о количестве мини-пакетов (т. н. задач), созданных до этого момента, и количестве мини-пакетов, обработанных на данный момент. В этом случае отображается результат задачи. Если задание завершилось с ошибкой, отобразится сообщение об ошибке, а также рекомендации по началу устранения неполадок.

- `~/logs/sys/master.txt`: В этом файле доступно представление главного узла (т. н. оркестратор) выполняемого задания. Включает создание задач, мониторинг хода выполнения, результат выполнения.

Журналы, созданные на основе начального сценария с помощью вспомогательного метода EntryScript и операторов Print, находятся следующих файлах:

- `~/logs/user/<ip_address>/<node_name>.log.txt`: Это журналы, записываемые из entry_script с помощью вспомогательного метода EntryScript. Также содержит оператор print (stdout) из entry_script.

Следующие признаки позволяют быстро распознать ошибки в сценарии.

- `~/logs/user/error.txt`: Этот файл попытается обобщить ошибки в сценарии.

Дополнительные сведения об ошибках в сценарии:

- `~/logs/user/error/`: Содержит все возникшие ошибки и полные трассировки стека, упорядоченные по узлам.

Если необходимо в полной мере оценить, как сценарий оценки выполняется на каждом из узлов, просмотрите отдельные журналы процесса для каждого узла. Журналы процесса можно найти в папке `sys/node`. Они сгруппированы по рабочим узлам:

- `~/logs/sys/node/<node_name>.txt`: Этот файл содержит подробные сведения о каждом мини-пакете, который выбирается или выполняется рабочей ролью. Для каждого мини-пакета этот файл содержит:

    - IP-адрес и идентификатор рабочего процесса. 
    - Общее число элементов, число успешно обработанных элементов и число элементов, обработка которых завершилась сбоем.
    - Время начала, продолжительность, время обработки и время выполнения метода.

Здесь также приведены сведения об использовании ресурсов процессами для каждой рабочей роли. Эти сведения доступны в файле CSV по адресу `~/logs/sys/perf/overview.csv`. Сведения о каждом процессе можно найти в разделе `~logs/sys/processes.csv`.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Ведение журнала из пользовательского сценария в удаленном контексте
Можно получить средство ведения журнала из EntryScript, как показано в приведенном ниже примере кода, чтобы журналы отображались в папке **logs/user** на портале.

**Пример начального сценария с использованием средства ведения журнала:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Передача всем рабочим ролям сторонних входных данных, таких как файлы, содержащие таблицу подстановки.

Создайте [набор данных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py), который содержит сторонние входные данные, и зарегистрируйте его в рабочей области. Передайте его в параметр `side_input` вашего `ParallelRunStep`. Кроме того, можно добавить путь к нему в раздел `arguments`, чтобы легко получить доступ к смонтированному пути:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

После этого доступ к нему можно будет получить в сценарии вывода (например, в методе init ()) следующим образом:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="next-steps"></a>Дальнейшие действия

* См. справку по пакету SDK с пакетом [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) и [документацию](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) по классу ParallelRunStep.

* Ознакомьтесь с [расширенным учебником](tutorial-pipeline-batch-scoring-classification.md) по использованию конвейеров с ParallelRunStep, а также с примером передачи другого файла в качестве сторонних входных данных. 
