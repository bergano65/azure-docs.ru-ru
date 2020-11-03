---
title: Руководство по выполнению скрипта "Hello world!" Скрипт Python
titleSuffix: Azure Machine Learning
description: Во второй части серии о начале работы с Машинным обучением Azure показано, как отправить тривиальный сценарий Python "Hello world!" в облако.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: afc851be08e6708efc0138dc45931cda147c67c1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895891"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Руководство по выполнению скрипта "Hello world!" Сценарий Python (часть 2 из 4)

В рамках этого учебника вы узнаете, как использовать пакет SDK Машинного обучения Azure для Python, чтобы отправить и запустить скрипт Python "Hello world!" .

Этот учебник является *второй частью серии учебников из четырех частей* , в рамках которой вы ознакомитесь с основами Машинного обучения Azure и выполните задачи машинного обучения на основе заданий в Azure. Этот учебник создан на основе работы, выполненной в рамках статьи [Часть 1. Настройка локального компьютера для службы "Машинное обучение Azure"](tutorial-1st-experiment-sdk-setup-local.md).

Выполняя данное руководство, вы сделаете следующее:

> [!div class="checklist"]
> * Создадите и запустите сценарий Python "Hello world!" локально.
> * Создадите сценарий элемента управления Python для отправки сценария "Hello world!" в службу "Машинное обучение Azure".
> * Примените концепции Машинного обучения Azure в сценарии элемента управления.
> * Отправите и запустите сценарий "Hello World!" .
> * просмотрите выходные данные кода в облаке;

## <a name="prerequisites"></a>Предварительные требования

- Выполнение инструкций [части 1](tutorial-1st-experiment-sdk-setup-local.md), если у вас еще нет рабочей области Машинного обучения Azure.
- Вводные знания о языке Python и рабочих процессах машинного обучения.
- Локальная среда разработки, например Visual Studio Code, Jupyter или PyCharm.
- Python (версия от 3.5 до 3.7).

## <a name="create-and-run-a-python-script-locally"></a>Создание и запуск скрипта Python в локальной среде

Создайте новый подкаталог с именем `src` в каталоге `tutorial` для хранения кода, который необходимо запустить в вычислительном кластере Машинного обучения Azure. В подкаталоге `src` создайте сценарий Python `hello.py`:

```python
# src/hello.py
print("Hello world!")
```

Теперь структура каталогов проекта будет выглядеть следующим образом:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Тестирование скрипта в локальной среде

Вы можете запустить свой код в локальной среде, используя избранную интегрированную среду разработки или терминал. Выполнение кода в локальной среде предоставляет преимущество в виде интерактивной отладки кода.

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Создание скрипта элемента управления

*Скрипт элемента управления* позволяет выполнять скрипт `hello.py` в облаке. Сценарий элемента управления используется, чтобы контролировать, как и где выполняется код машинного обучения.  

В каталоге учебника создайте новый файл Python с именем `03-run-hello.py`. Затем скопируйте и вставьте приведенный ниже код в этот файл:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Изучение кода

Ниже приведено описание функционирования скрипта элемента управления.

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) подключается к рабочей области Машинного обучения Azure, чтобы можно было взаимодействовать с ресурсами Машинного обучения Azure.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) предоставляет простой способ организации нескольких запусков с одним именем. В дальнейшем можно увидеть, как эксперименты упрощают сравнение метрик множества запусков.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) создает оболочку для кода `hello.py` и передает его в рабочую область. Как следует из названия, этот класс можно использовать для _настройки_ _запуска_ _скрипта_ в Машинном обучении Azure. Кроме того, он указывает, на каком целевом объекте вычислений будет выполняться скрипт. В этом коде целевым объектом является вычислительный кластер, созданный в рамках [учебника по настройке](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Отправляет скрипт. Эта отправка называется [запуском](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true). Запуск инкапсулирует одно выполнение кода. Используйте запуск, чтобы отслеживать ход выполнения скрипта, записывать выходные данные, анализировать результаты, визуализировать метрики и многое другое.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        Объект `run` предоставляет дескриптор для выполнения кода. Отслеживайте ход выполнения в Студии машинного обучения Azure, используя URL-адрес из скрипта Python.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>Отправка и выполнение кода в облаке

Запустите скрипт элемента управления, который затем выполняет `hello.py` в вычислительном кластере, созданном во время работы с [учебником по установке](tutorial-1st-experiment-sdk-setup-local.md).

```bash
python 03-run-hello.py
```

> [!TIP]
> Если выполнение этого кода приводит к ошибке с сообщением о том, что у вас нет доступа к подписке, см. раздел [Подключение к рабочей области](how-to-manage-workspace.md?tab=python#connect-multi-tenant), чтобы получить сведения о параметрах проверки подлинности.

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a>Мониторинг кода в облаке с помощью студии

Выходные данные будут содержать ссылку на студию, которая выглядит примерно так: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Щелкните ссылку и перейдите на вкладку **Выходные данные и журналы**. Перейдя, можно увидеть файл `70_driver_log.txt`, который выглядит следующим образом:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

В строке 8 отображается сообщение Hello World!

Файл `70_driver_log.txt` содержит стандартные выходные данные выполнения. Этот файл может быть полезен при отладке удаленных запусков в облаке.

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого учебника вы создали простой скрипт "Hello world!" и выполнили его в Azure. Вы узнали, как подключиться к рабочей области Машинного обучения Azure, создать эксперимент и отправить свой код `hello.py` в облако.

В следующем учебнике, основываясь на этих сведениях, вы запустите скрипт более интересный, чем `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Руководство. Обучение модели](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Если вы хотите на этом завершить серию учебников, не переходя к следующему шагу, [очистите ресурсы](tutorial-1st-experiment-bring-data.md#clean-up-resources).
