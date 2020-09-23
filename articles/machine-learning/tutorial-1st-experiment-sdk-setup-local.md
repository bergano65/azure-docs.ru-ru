---
title: Руководство по  началу работы с машинным обучением для Python
titleSuffix: Azure Machine Learning
description: В этом учебнике описано начало работы с пакетом SDK для Python Машинного обучения Azure, запущенным в вашей личной среде разработки.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929708"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Руководство по началу работы с Машинным обучением Azure в среде разработки (часть 1 из 4)

В рамках этой **серии учебников, состоящей из четырех частей**, вы изучите основы Машинного обучения Azure и выполните указанные ниже задачи машинного обучения для Python на основе заданий в облаке Azure.

1. Настройка рабочей области и локальной среды разработчика машинного обучения.
2. Выполнение кода в облаке с помощью пакета SDK для Python Машинного обучения Azure.
3. Управление средой Python, используемой для обучения модели.
4. Отправка данных в Azure и их использование для обучения.

В **части 1 этой серии учебников** вы выполните указанные ниже задачи.

> [!div class="checklist"]
> * Установка пакета SDK Машинного обучения Azure.
> * Настройка структуры каталогов для кода.
> * Создание рабочей области машинного обучения Azure
> * Настройка локальной среды разработки.
> * Настройка вычислительного кластера.

>[!NOTE]
> В этой серии учебников основное внимание уделяется концепциям Машинного обучения Azure, подходящим для выполнения задач машинного обучения __на основе заданий__ в Python, для которых требуются ресурсоемкие вычисления и/или воспроизводимость. Если задачи машинного обучения не соответствуют этому профилю, используйте функции [Jupyter или RStudio для вычислительного экземпляра Машинного обучения Azure](tutorial-1st-experiment-sdk-setup.md), чтобы подключить Машинное обучение Azure.

## <a name="prerequisites"></a>Prerequisites

- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте использовать [Машинное обучение Azure](https://aka.ms/AMLFree) прямо сейчас.
- Ознакомьтесь с Python и [концепциями Машинного обучения](concept-azure-machine-learning-architecture.md). Например, со сведениями о средах, обучении, оценке и т. д.
- Локальная среда разработки — ноутбук с установленными Python и предпочтительной интегрированной средой разработки (например, VSCode, Pycharm, Jupyter и т. д.)

## <a name="install-the-azure-machine-learning-sdk"></a>Установка пакета SDK Машинного обучения Azure.

В рамках этого учебника используется пакет SDK для Python Машинного обучения Azure.

Вы можете применить знакомые вам инструменты, например conda, pip и т. д., чтобы настроить среду для использования в рамках этого учебника. Установите в среде пакет SDK для Python Машинного обучения Azure с помощью pip:

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>Создание структуры каталогов для кода
Для работы с этим учебником рекомендуется выполнить следующую простую настройку каталога:

```markdown
tutorial
└──.azureml
```

- **tutorial** (каталог верхнего уровня проекта)
- **.azureml** (скрытый подкаталог учебника):  каталог `.azureml` используется для хранения файлов конфигурации Машинного обучения Azure.

## <a name="create-an-azure-machine-learning-workspace"></a>Создание рабочей области машинного обучения Azure

Рабочая область — это ресурс верхнего уровня для Машинного обучения Azure и централизованное расположение для:

- управления ресурсами (например, вычислительными ресурсами);
- хранения ресурсов, таких как записные книжки, среды, наборы данных, конвейеры, модели, конечные точки и т. д.;
- совместной работы с другими участниками команды.

В верхнем родительском каталоге (`tutorial`) добавьте новый файл Python с именем `01-create-workspace.py` и указанным ниже кодом. Задайте параметры (имя, идентификатор подписки, группа ресурсов и [расположение](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) согласно вашим предпочтениям.

Вы можете выполнить код в интерактивном сеансе или с помощью файла python.

>[!NOTE]
> При использовании локальной среды разработки (например, ноутбука) отобразится запрос для проверки подлинности в рабочей области с помощью *кода устройства* при первом выполнении указанного ниже кода. Следуйте инструкциям на экране.

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

После выполнения приведенного выше фрагмента кода структура папок будет выглядеть следующим образом:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Файл `.azureml/config.json` содержит метаданные, необходимые для подключения к рабочей области Машинного обучения Azure, а именно идентификатор подписки, группу ресурсов и имя рабочей области. 

> [!NOTE]
> Содержимое `config.json` не является секретом. К нему можно предоставлять общий доступ.
> Для взаимодействия с рабочей областью Машинного обучения Azure также необходимо пройти проверку подлинности.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Создание вычислительного кластера Машинного обучения Azure

Создайте скрипт python в каталоге `tutorial` верхнего уровня с именем `02-create-compute.py` и заполните его приведенным ниже кодом, чтобы создать вычислительный кластер Машинного обучения Azure с автомасштабированием (0–4 узла).

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
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

Запустите файл python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> После создания кластера для него будут подготовлены 0 узлов. Следовательно, за кластер **не** взимается плата, пока вы не отправите задание. Если этот кластер бездействует на протяжении 2400 секунд (40 минут), для него вертикально уменьшается масштаб.

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

- создание рабочей области Машинного обучения Azure;
- настройка локальной среды разработки;
- создание вычислительного кластера Машинного обучения Azure.

В следующем учебнике описано, как отправить скрипт в вычислительный кластер Машинного обучения Azure.

> [!div class="nextstepaction"]
> [Руководство. Запуск скрипта Python Hello World в Azure](tutorial-1st-experiment-hello-world.md)
