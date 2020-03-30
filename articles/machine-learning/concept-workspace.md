---
title: Что такое рабочее пространство
titleSuffix: Azure Machine Learning
description: Рабочее пространство является ресурсом высшего уровня для машинного обучения Azure. Он хранит историю всех обучаемых запусков, включая журналы, метрики, выходные данные и моментальный снимок ваших скриптов. Вы используете эту информацию, чтобы определить, какой обучаемый запуск производит лучшую модель
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505580"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Что такое рабочее пространство для машинного обучения Azure?

Рабочее пространство — это ресурс верхнего уровня для машинного обучения Azure, обеспечивающий централизованное место для работы со всеми артефактами, которые вы создаете при использовании Машинного обучения Azure.  Рабочее пространство хранит историю всех обучаемых запусков, включая журналы, метрики, выходные данные и моментальный снимок скриптов. Используйте эти сведения для выбора обучающего запуска с оптимальной моделью.  

Если у вас есть модель, которая вам нравится, вы регистрируете ее в рабочем пространстве. Затем для развертывания в Azure Container Instances, Azure Kubernetes или в наборе программируемых стробов (FPGA) в качестве конечной точки HTTP-программы для развертывания на основе данных модели и скоринга используется в качестве локтевой точки безопасности. Можно также развернуть модель на устройстве Azure IoT Edge в качестве модуля.

Цены и доступные функции зависят от того, выбрано ли [издание Basic или Enterprise](overview-what-is-azure-ml.md#sku) для рабочего пространства. Вы выбираете издание при [создании рабочего пространства.](#create-workspace)  Вы также можете [перейти](#upgrade) от базового до Enterprise издания.

## <a name="taxonomy"></a>Классификация 

Таксономия рабочей области показана на следующей схеме:

[![Таксономия рабочего пространства](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Диаграмма показывает следующие компоненты рабочей области:

+ Рабочее пространство может содержать [вычислило вычислилок машинного обучения Azure Machine Learning,](concept-compute-instance.md)облачные ресурсы, настроенные с средой Python, необходимой для запуска Azure Machine Learning.

+ [Роли пользователей](how-to-assign-roles.md) позволяют делиться рабочим пространством с другими пользователями, группами или проектами.
+ Для выполнения экспериментов используются [вычислительные цели.](concept-azure-machine-learning-architecture.md#compute-targets)
+ При создании рабочего пространства для вас также создаются [связанные ресурсы.](#resources)
+ [Эксперименты](concept-azure-machine-learning-architecture.md#experiments) обучение работает вы используете для создания моделей.  
+ [Трубопроводы](concept-azure-machine-learning-architecture.md#ml-pipelines) — это многоразовые рабочие процессы для обучения и переподготовки модели.
+ [Наборы данных](concept-azure-machine-learning-architecture.md#datasets-and-datastores) помогают в управлении данными, которые вы используете для обучения модели и создания конвейеров.
+ Если у вас есть модель, которая требуется развернуть, создается зарегистрированная модель.
+ Используйте зарегистрированную модель и скоринговый скрипт для создания [конечной точки развертывания.](concept-azure-machine-learning-architecture.md#endpoints)

## <a name="tools-for-workspace-interaction"></a>Инструменты для взаимодействия рабочего пространства

Вы можете взаимодействовать с рабочим пространством следующим образом:

+ В Интернете:
    + [Студия машинного обучения Azure](https://ml.azure.com) 
    + [Дизайнер машинного обучения Azure (предварительный просмотр)](concept-designer.md) - доступен только в рабочих областях [корпоративного издания.](overview-what-is-azure-ml.md#sku)
+ В любой среде Python с [Azure Machine Learning SDK для Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ В любой среде R с [Azure Machine Learning SDK для R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ На командной строке с помощью расширения Azure Machine Learning [CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>Машинное обучение с рабочим пространством

Задачи машинного обучения читают и/или пишут артефакты в рабочее пространство.

+ Выполнить эксперимент для обучения модели - пишет результаты эксперимента запустить в рабочее пространство.
+ Используйте автоматизированный ML для обучения модели - пишет результаты обучения в рабочем пространстве.
+ Регистрация модели в рабочем пространстве.
+ Развертывание модели - использует зарегистрированную модель для создания развертывания.
+ Создавайте и запускайте многоразовые рабочие процессы.
+ Просмотр артефактов машинного обучения, таких как эксперименты, конвейеры, модели, развертывания.
+ Отслеживайте и отслеживайте модели.

## <a name="workspace-management"></a>Управление рабочим пространством

Вы также можете выполнить следующие задачи управления рабочим и рабочим пространством:

| Задача управления рабочим пространством   | Портал              | Студия | Python SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Создание рабочей области        | **&check;**     | | **&check;** | **&check;** |
| Управление доступом к рабочей области    | **&check;**   || |  **&check;**    |
| Обновление до корпоративного издания    | **&check;** | **&check;**  | |     |
| Создание и управление вычислительными ресурсами    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Создание ноутбука VM |   | **&check;** | |     |

> [!WARNING]
> Перемещение рабочего пространства Azure Machine Learning в другую подписку или перемещение подписки на новый арендатор не поддерживается. Это может привести к ошибкам.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Создание рабочего пространства

При создании рабочего пространства вы решаете, следует ли создавать его с [помощью издания Basic или Enterprise.](overview-what-is-azure-ml.md#sku) Издание определяет возможности, доступные в рабочей области. Среди других функций, Enterprise Edition дает вам доступ к [дизайнеру машинного обучения Azure](concept-designer.md) и студийной версии построения [автоматизированных экспериментов машинного обучения.](tutorial-first-experiment-automated-ml.md)  Для получения более подробной [Azure Machine Learning pricing](https://azure.microsoft.com/pricing/details/machine-learning/)информации и информации о ценах на цены см.

Существует несколько способов создания рабочего пространства:  

* Используйте [портал Azure](how-to-manage-workspace.md) для интерфейса «точка и клик», чтобы провести каждый шаг.
* Используйте [SDK Для Python для машинного обучения Azure для](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) Python для создания рабочего пространства на лету из скриптов Python или ноутбуков Jupiter
* При необходимости автоматизации или настройки создания с корпоративными стандартами безопасности используйте [шаблон Azure Resource Manager](how-to-create-workspace-template.md) или [CLI Azure Machine Learning.](reference-azure-machine-learning-cli.md)
* Если вы работаете в Visual Studio Code, используйте [расширение VS Code.](tutorial-setup-vscode-extension.md)

> [!NOTE]
> Имя рабочего пространства является нечувствительным.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Обновление до корпоративного издания

С помощью портала Azure можно [обновить рабочее пространство с базового на корпоративное издание.](how-to-manage-workspace.md#upgrade) Вы не можете понизить рабочее пространство корпоративного издания до рабочего пространства базового издания. 

## <a name="associated-resources"></a><a name="resources"></a>Связанные ресурсы

При создании новой рабочей области она автоматически создает несколько ресурсов Azure, которые используются в рабочей области:

+ [Реестр контейнеров Azure:](https://azure.microsoft.com/services/container-registry/)Регистры докерных контейнеров, которые используются во время обучения и при развертывании модели. Чтобы минимизировать затраты, ACR **загружается с ленивыми** до создания изображений развертывания.
+ [Учетная запись хранения Azure:](https://azure.microsoft.com/services/storage/)используется в качестве хранилища данных по умолчанию для рабочего пространства.  Здесь также хранятся ноутбуки Jupyter, которые используются в экземплярах azure Machine Learning.
+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)Хранит информацию о ваших моделях.
+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)Хранит секреты, используемые вычислительными целями, и другую конфиденциальную информацию, необходимую рабочей области.

> [!NOTE]
> В дополнение к созданию версий можно также использовать имеющиеся службы Azure.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать работу с Azure Machine Learning, см.:

+ [Обзор машинного обучения Azure](overview-what-is-azure-ml.md)
+ [Создание рабочего пространства](how-to-manage-workspace.md)
+ [Управление рабочим пространством](how-to-manage-workspace.md)
+ [Учебник: Начать создавать свой первый эксперимент ML с Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Учебник: Начать с Azure машинного обучения с R SDK](tutorial-1st-r-experiment.md)
+ [Учебник: Создайте свою первую модель классификации с помощью автоматизированного машинного обучения](tutorial-first-experiment-automated-ml.md) (доступно только в рабочих областях [корпоративного издания)](overview-what-is-azure-ml.md#sku)
+ [Учебник: Предсказать цену автомобиля с дизайнером](tutorial-designer-automobile-price-train-score.md) (доступно только в рабочих пространствах [издания Enterprise)](overview-what-is-azure-ml.md#sku)
