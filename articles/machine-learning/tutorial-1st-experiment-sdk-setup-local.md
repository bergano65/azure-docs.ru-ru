---
title: Руководство по началу работы с машинным обучением — Python
titleSuffix: Azure Machine Learning
description: В этом учебнике описано начало работы с пакетом SDK Машинного обучения Azure для Python, запущенным в вашей личной среде разработки.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: c0fe3c3808709de732bec8ce0599d380094405e8
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368487"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Руководство по началу работы с Машинным обучением Azure в среде разработки (часть 1 из 4)

В рамках этой *серии учебников, состоящей из четырех частей*, вы изучите основы Машинного обучения Azure и выполните указанные ниже задачи машинного обучения для Python на основе заданий на облачной платформе Azure. Сюда входят следующие задачи:

1. Настройка рабочей области и локальной среды разработчика машинного обучения.
2. Выполнение кода в облаке с помощью пакета SDK Машинного обучения Azure для Python.
3. Управление средой Python, используемой для обучения модели.
4. Отправка данных в Azure и их использование для обучения.

В части 1 этой серии учебников вы выполните указанные ниже задачи.

> [!div class="checklist"]
> * Установка пакета SDK Машинного обучения Azure.
> * Настройка структуры каталогов для кода.
> * Создайте рабочую область машинного обучения Azure.
> * Настройка локальной среды разработки.
> * Настройка вычислительного кластера.

>[!NOTE]
> В этой серии учебников основное внимание уделяется концепциям Машинного обучения Azure, подходящим для выполнения задач машинного обучения *на основе заданий* в Python, для которых требуются ресурсоемкие вычисления и/или воспроизводимость. Если задачи машинного обучения не соответствуют этому профилю, используйте функции [Jupyter или RStudio для вычислительного экземпляра Машинного обучения Azure](tutorial-1st-experiment-sdk-setup.md), чтобы перейти на Машинное обучение Azure.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [Машинное обучение Azure](https://aka.ms/AMLFree).
- Ознакомьтесь с Python и [концепциями Машинного обучения](concept-azure-machine-learning-architecture.md). Примеры: среды, обучение и оценка.
- Локальная среда разработки: портативный компьютер с установленным Python и предпочтительной интегрированной средой разработки (например, Visual Studio Code, PyCharm или Jupyter).

## <a name="install-the-azure-machine-learning-sdk"></a>Установка пакета SDK Машинного обучения Azure.

В рамках этого учебника используется пакет SDK Машинного обучения Azure для Python.

Вы можете применять знакомые вам инструменты, например Conda и pip, чтобы настроить среду для использования в рамках этого учебника. Установите в среде пакет SDK Машинного обучения Azure для Python с помощью pip:

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>Создание структуры каталогов для кода
Для работы с этим руководством рекомендуется настроить следующую простую структуру каталогов:

```markdown
tutorial
└──.azureml
```

- `tutorial`: каталог верхнего уровня проекта.
- `.azureml`: скрытый подкаталог для хранения файлов конфигурации Машинного обучения Azure.

## <a name="create-an-azure-machine-learning-workspace"></a>Создание рабочей области машинного обучения Azure

Рабочая область — это ресурс верхнего уровня для Машинного обучения Azure и централизованное расположение для:

- управления ресурсами (например, вычислительными ресурсами);
- хранения ресурсов, таких как записные книжки, среды, наборы данных, конвейеры, модели и конечные точки;
- совместной работы с другими участниками команды.

В каталоге верхнего уровня `tutorial` добавьте новый файл Python с именем `01-create-workspace.py`, используя следующий код. Задайте параметры (имя, идентификатор подписки, группа ресурсов и [расположение](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) согласно своим предпочтениям.

Вы можете выполнить код в интерактивном сеансе или с помощью файла Python.

>[!NOTE]
> Если вы используете локальную среду разработки (например, портативный компьютер), вам будет предложено пройти проверку подлинности в рабочей области с помощью *кода устройства* при первом запуске следующего кода. Следуйте инструкциям на экране.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Запустите этот код из каталога `tutorial`:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

После выполнения предыдущего фрагмента кода структура папок будет выглядеть следующим образом:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Файл `.azureml/config.json` содержит метаданные, необходимые для подключения к рабочей области Машинного обучения Azure. А именно, он содержит идентификатор подписки, группу ресурсов и имя рабочей области. 

> [!NOTE]
> Содержимое `config.json` не является секретным. Вы можете использовать эти сведения совместно.
>
> Для взаимодействия с рабочей областью Машинного обучения Azure также необходимо пройти проверку подлинности.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Создание вычислительного кластера Машинного обучения Azure

Создайте скрипт Python в каталоге верхнего уровня `tutorial` с именем `02-create-compute.py`. Заполните его следующим кодом, чтобы создать кластер Машинного обучения Azure, который будет автоматически масштабироваться от нуля до четырех узлов:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Запустите файл Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> После создания кластера для него будут подготовлены 0 узлов. За кластер *не* взимается плата, пока вы не отправите задание. Если этот кластер бездействует на протяжении 2400 секунд (40 минут), его размер уменьшается.

Теперь структура папок будет выглядеть следующим образом:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Следующие шаги

В этом учебнике по установке вы выполнили следующие задачи:

- создали рабочую область Машинного обучения Azure;
- настройка локальной среды разработки;
- создание вычислительного кластера Машинного обучения Azure.

В следующем учебнике описано, как отправить скрипт в вычислительный кластер Машинного обучения Azure.

> [!div class="nextstepaction"]
> [Учебник по выполнению скрипта "Hello world!" в Azure](tutorial-1st-experiment-hello-world.md)
