---
title: Использование расширения Visual Studio Code Tools for AI со службой "Машинное обучение Azure"
description: Сведения о Visual Studio Code Tools for AI и о том, как начать обучение и развертывание моделей машинного и глубокого обучения с использованием службы "Машинное обучение Azure" в VS Code.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 377a4bbf359b2c65136625fcef8a1093e49da728
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854311"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>VS Code Tools for AI. Обучение и развертывание моделей машинного обучения с помощью VS Code
Из этой статьи вы узнаете о том, как использовать расширение **VS Code Tools for AI** для обучения и развертывания моделей машинного и глубокого обучения в службе "Машинное обучение Azure" с помощью VS Code.

Служба "Машинное обучение Azure" предоставляет поддержку для выполнения экспериментов локально и на удаленных целевых объектах вычислений. Для каждого эксперимента вы можете отслеживать множество запусков, которые можно выполнять как угодно часто с целью итеративной проверки разных методик, гиперпараметров и т. п. Службу "Машинное обучение Azure" можно использовать для отслеживания пользовательских метрик и экспериментов, обеспечивая повторяемость и возможность проверки для задач обработки и анализа данных.

Также вы можете развернуть эти модели для тестирования и производственных задач.

## <a name="prerequisites"></a>Предварительные требования

+ Расширение [VS Code Tools for AI](how-to-vscode-tools.md), настроенное для работы со службой "Машинное обучение Azure".

+ [Пакет SDK службы "Машинного обучения Azure" для Python](how-to-vscode-tools.md), установленный в VS Code.

+ Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://aka.ms/AMLfree), прежде чем начинать работу.

## <a name="create-and-manage-compute-targets"></a>Создание целевых объектов вычислений и управление ими

Расширение Visual Studio Code Tools for AI позволяет подготовить данные, обучить модели и развернуть их как локально, так и на удаленных целевых объектах вычислений.

Это расширение поддерживает несколько удаленных целевых объектов вычислений для службы "Машинное обучение Azure". См. [полный список целевых объектов вычислений](how-to-set-up-training-targets.md), поддерживаемых службой "Машинное обучения Azure".

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>Создание целевых объектов вычислений для службы "Машинное обучение Azure" с помощью VS Code

**Чтобы создать целевой объект вычислений, выполните следующие действия:**

1. Щелкните значок Azure на панели действий Visual Studio Code. Появится боковая панель Azure: Machine Learning.

2. В представлении в виде дерева разверните подписку Azure и рабочую область службы "Машинное обучение Azure". В примере, показанном на анимированном изображении, имя подписки — OpenMind Studio, а рабочее пространство — MyWorkspace. 

3. В узле рабочей области щелкните правой кнопкой мыши узел **Compute** (Вычисления) и выберите **Create Compute** (Создать вычислительный ресурс).

4. Выберите из списка тип целевого объекта вычислений. 

5. В текстовом поле укажите уникальное имя для этого целевого объекта вычислений, а также размер виртуальной машины.

6. Настройте дополнительные свойства в файле конфигурации JSON, который откроется в новой вкладке. 

7. Завершив настройку целевого объекта вычислений, щелкните **Finish** (Готово) в правом нижнем углу.

Ниже приведен пример для Azure Batch AI: [![Создание вычислений для Azure Batch AI с помощью VS Code](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>Использование удаленных вычислительных ресурсов для экспериментов в VS Code

Чтобы использовать для обучения удаленный целевой объект вычислений, создайте файл конфигурации запуска. Этот файл указывает службе "Машинное обучение Azure" не только место выполнения эксперимента, но и метод подготовки среды.

#### <a name="the-run-configuration-file"></a>Файл конфигурации запуска

Расширение для VS Code автоматически создает конфигурацию запуска для сред **local** и **docker** на локальном компьютере.

Здесь представлен фрагмент файла с конфигурацией запуска по умолчанию.

Если вы хотите самостоятельно установить все библиотеки и зависимости, задайте `userManagedDependencies: True`. Теперь локальные запуски экспериментов будут использовать окружение Python по умолчанию, которая настроена в расширении VS Code для Python.

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

#### <a name="the-conda-dependencies-file"></a>Файл зависимостей conda

По умолчанию создается новая среда conda, которая управляет устанавливаемыми зависимостями. Но сами зависимости следует включить в файл `aml_config/conda_dependencies.yml`.

Ниже представлен фрагмент файла aml_config/conda_dependencies.yml, используемого по умолчанию.
Вы можете добавить в файл конфигурации дополнительные зависимости.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>Обучение и тонкая настройка моделей

Работа со службой "Машинное обучение Azure" из VS Code позволяет выполнять быструю итерации кода, пошаговое выполнение и отладку, а также использовать любую систему управления версиями. 

**Чтобы запустить эксперимент с помощью службы "Машинное обучение Azure", выполните следующие шаги:**

1. Щелкните значок Azure на панели действий Visual Studio Code. Появится боковая панель Azure: Machine Learning.

1. В представлении в виде дерева разверните подписку Azure и рабочую область службы "Машинное обучение Azure". В примере, показанном на анимированном изображении, имя подписки — OpenMind Studio, а рабочее пространство — MyWorkspace. 

1. В узле рабочей области разверните узел **Compute** (Вычисления) и щелкните правой кнопкой мыши **Run Config** (Выполнить конфигурацию) для вычислительного ресурса, который вы хотите использовать. 

1. Щелкните **Run Experiment** (Выполнить эксперимент).

1. Щелкните **View Experiment Run** (Просмотр выполнения эксперимента), чтобы открыть встроенный портал службы "Машинное обучение Azure", где вы сможете отслеживать запуски и просматривать обученные модели.

   [![Запуск экспериментов машинного обучения из VS Code](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

## <a name="deploy-and-manage-models"></a>Развертывание моделей и управление ими
Служба "Машинное обучение Azure" позволяет развертывать модели машинного обучения и управлять ими в облаке и на пограничных устройствах. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>Регистрация модели в службе "Машинное обучение Azure" с помощью VS Code

После того как вы завершили обучение модели, ее можно зарегистрировать в рабочей области.
Зарегистрированные модели можно отслеживать и развертывать.

**Чтобы зарегистрировать модель, выполните следующие шаги:**

1. Щелкните значок Azure на панели действий Visual Studio Code. Появится боковая панель Azure: Machine Learning.

1. В представлении в виде дерева разверните подписку Azure и рабочую область службы "Машинное обучение Azure".

1. В узле рабочей области щелкните правой кнопкой мыши **Models** (Модели) и выберите **Register Model** (Зарегистрировать модель).

1. Выберите в списке, нужно ли отправить **model file** (файл модели для одной модели) или **model folder** (папку модели для моделей с несколькими файлами, например Tensorflow). 

1. Используйте диалоговое окно выбора файла, чтобы выбрать файл или папку.

   [![Вычисления](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> Теперь удалите теги из созданного JSON-файла.

### <a name="deploy-your-service-from-vs-code"></a>Развертывание службы с помощью VS Code

С помощью VS Code веб-службу можно развернуть в следующих расположениях:
+ экземпляр контейнера Azure (ACI) — для тестирования;
+ служба Azure Kubernetes (AKS) — для производственных задач. 

Контейнеры ACI для тестирования не нужно создавать заранее, так как они создаются автоматически. Но кластеры AKS нужно обязательно настраивать заранее. 

См. дополнительные сведения [о развертывании в службе "Машинное обучение Azure"](how-to-deploy-and-where.md).

**Чтобы развернуть веб-службу, выполните следующие шаги:**

1. Щелкните значок Azure на панели действий Visual Studio Code. Появится боковая панель Azure: Machine Learning.

1. В представлении дерева разверните подписку Azure и рабочую область службы "Машинное обучение Azure".

1. В узле рабочей области, разверните узел **Models** (Модели).

1. Щелкните правой кнопкой мыши модель, которую вы намерены развернуть, и выберите в контекстном меню команду **Deploy Service from Registered Model** (Развернуть службу из зарегистрированной модели).

1. В палитре команд VS Code выберите из списка целевой объект вычислений, в котором нужно развернуть службу. 

1. В текстовом поле укажите имя для этой службы. 

1. В диалоговом окне в правом нижнем углу щелкните **Browse** (Обзор) и выберите скрипт оценки. Диалоговое окно закроется.

1. Если у вас есть локальный файл Docker, щелкните **Browse** (Обзор) во втором диалоговом окне. 

   Если вы закроете это диалоговое окно, не указав локальный файл Docker, по умолчанию используется файл для службы "Машинное обучение Azure".

1. В третьем диалоговом окне щелкните **Browse** (Обзор) и выберите путь к локальному файлу conda. Также этот путь к файлу можно позднее указать с помощью редактора JSON.

1. Если вы намерены использовать существующий файл schema.json, щелкните **Browse** (Обзор) в четвертом диалоговом окне и выберите этот файл.

Теперь веб-служба развернута.

Здесь вы найдете [![пример для экземпляра контейнера Azure](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox), выполняемый из VS Code.

## <a name="next-steps"></a>Дополнительная информация

Пошаговые инструкции по обучению с помощью службы "Машинное обучение" без VS Code вы найдете в [руководстве по обучению моделей в службе "Машинное обучение Azure"](tutorial-train-models-with-aml.md).

Пошаговые инструкции по редактированию, запуску и отладке кода Python на локальном компьютере см. в [руководстве Hello World по языку Python](https://code.visualstudio.com/docs/python/python-tutorial).
