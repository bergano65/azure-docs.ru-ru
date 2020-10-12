---
title: Отладка и устранение неполадок ParallelRunStep
titleSuffix: Azure Machine Learning
description: Отладка и устранение неполадок ParallelRunStep в конвейерах машинного обучения в пакете SDK решения "Машинное обучение Azure" для Python. Изучите распространенные ошибки для разработки с помощью конвейеров и рекомендации по отладке сценариев до и во время удаленного выполнения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 7866f2dcaebe396759eb7f6315c457bfce307723
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315581"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Отладка и устранение неполадок ParallelRunStep


Из этой статьи вы узнаете, как выполнять отладку и устранение неполадок класса [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py&preserve-view=true) из [пакета SDL решения "Машинное обучение Azure"](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

## <a name="testing-scripts-locally"></a>Локальное тестирование сценариев

См. раздел [Локальное тестирование сценариев](how-to-debug-pipelines.md#debug-scripts-locally) для конвейеров машинного обучения. Ваш класс ParallelRunStep выполняется как этап в конвейерах машинного обучения, поэтому в обоих случаях применимы одни и те же решения.

## <a name="debugging-scripts-from-remote-context"></a>Отладка сценариев из удаленного контекста

Переход от локальной отладки сценария оценки к отладке сценария оценки в фактическом конвейере может оказаться сложной задачей. Сведения о поиске журналов на портале см. в разделе о [конвейерах машинного обучения для сценариев отладки из удаленного контента](how-to-debug-pipelines.md). Сведения в этом разделе также применимы к классу ParallelRunStep.

Например, файл журнала `70_driver_log.txt` содержит сведения о контроллере, который запускает код ParallelRunStep.

Из-за того, что задания ParallelRunStep имеют распределенный характер, журналы могут поступать из нескольких разных источников. Однако создается два консолидированных файла, которые предоставляют общие сведения.

- `~/logs/job_progress_overview.txt`: Этот файл содержит общие сведения о количестве мини-пакетов (т. н. задач), созданных до этого момента, и количестве мини-пакетов, обработанных на данный момент. В этом случае отображается результат задачи. Если задание завершилось с ошибкой, отобразится сообщение об ошибке, а также рекомендации по началу устранения неполадок.

- `~/logs/sys/master_role.txt`: Этот файл содержит основной узел (также известный как Orchestrator) для выполняемого задания. Включает создание задач, мониторинг хода выполнения, результат выполнения.

Журналы, созданные на основе начального сценария с помощью вспомогательного метода EntryScript и операторов Print, находятся следующих файлах:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Эти файлы являются журналами, записанными из entry_script с помощью вспомогательного приложения Ентрискрипт.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Эти файлы являются журналами из stdout (например, инструкции Print) entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Эти файлы являются журналами из stderr entry_script.

Следующие признаки позволяют быстро распознать ошибки в сценарии.

- `~/logs/user/error.txt`: Этот файл попытается обобщить ошибки в сценарии.

Дополнительные сведения об ошибках в сценарии:

- `~/logs/user/error/`: Содержит полные трассировки стека исключений, возникших при загрузке и выполнении скрипта записи.

Если необходимо в полной мере оценить, как сценарий оценки выполняется на каждом из узлов, просмотрите отдельные журналы процесса для каждого узла. Журналы процесса можно найти в папке `sys/node`. Они сгруппированы по рабочим узлам:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Этот файл содержит подробные сведения о каждом мини-пакете по мере его комплектации или завершения рабочими процессами. Для каждого мини-пакета этот файл содержит:

    - IP-адрес и идентификатор рабочего процесса. 
    - Общее число элементов, число успешно обработанных элементов и число элементов, обработка которых завершилась сбоем.
    - Время начала, продолжительность, время обработки и время выполнения метода.

Здесь также приведены сведения об использовании ресурсов процессами для каждой рабочей роли. Эти сведения доступны в файле CSV по адресу `~/logs/sys/perf/<ip_address>/node_resource_usage.csv`. Сведения о каждом процессе доступны в разделе `~logs/sys/perf/<ip_address>/processes_resource_usage.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Ведение журнала из пользовательского сценария в удаленном контексте
Параллелрунстеп может запускать несколько процессов на одном узле на основе process_count_per_node. Чтобы упорядочить журналы из каждого процесса на узле и объединить инструкции Print и log, рекомендуется использовать средство ведения журнала Параллелрунстеп, как показано ниже. Вы получаете средство ведения журнала из Ентрискрипт и записываете журналы в папку **журналы/пользователь** на портале.

**Пример начального сценария с использованием средства ведения журнала:**
```python
from azureml_user.parallel_run import EntryScript

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

Пользователь может передать ссылочные данные в скрипт, используя параметр side_inputs Параллерунстеп. Все наборы данных, предоставленные как side_inputs, будут подключены к каждому рабочему узлу. Пользователь может получить расположение Mount, передав аргумент.

Создайте [набор данных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) , содержащий эталонные данные, и зарегистрируйте его в рабочей области. Передайте его в параметр `side_inputs` вашего `ParallelRunStep`. Кроме того, можно добавить путь в раздел, `arguments` чтобы легко получить доступ к подключенному пути:

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

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Как использовать входные наборы данных с проверкой подлинности субъекта-службы?

Пользователь может передавать входные наборы данных с проверкой подлинности субъекта-службы в рабочей области. Использование такого набора данных в Параллелрунстеп требует регистрации набора данных для создания конфигурации Параллелрунстеп.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Дальнейшие шаги

* Сведения о пакете [azureml-Pipeline-пошаговые инструкции](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true) см. в справочнике по пакету SDK. Просмотрите справочную [документацию](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py&preserve-view=true) по классу параллелрунстеп.

* Ознакомьтесь с [дополнительным руководством](tutorial-pipeline-batch-scoring-classification.md) по использованию конвейеров с параллелрунстеп. В этом руководстве показано, как передать другой файл в качестве побочного входа. 
