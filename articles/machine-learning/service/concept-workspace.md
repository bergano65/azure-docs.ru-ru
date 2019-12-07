---
title: Что такое Рабочая область
titleSuffix: Azure Machine Learning
description: Рабочая область — это ресурс верхнего уровня для Машинное обучение Azure. Он хранит историю всех учебных запусков, включая журналы, метрики, выходные данные и моментальные снимки сценариев. Эти сведения используются для определения того, какая из учебных запусков обеспечивает наилучшую модель.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: 14fc91feb16fb45eee1fe2e757a717e04ef07f99
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894723"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Что такое Рабочая область Машинное обучение Azure?

Рабочая область — это ресурс верхнего уровня для Машинное обучение Azure, предоставляющий централизованное место для работы со всеми артефактами, создаваемыми при использовании Машинное обучение Azure.  Рабочая область хранит историю всех учебных запусков, включая журналы, метрики, выходные данные и моментальные снимки сценариев. Используйте эти сведения для выбора обучающего запуска с оптимальной моделью.  

Получив модель, вы регистрируете ее в рабочей области. Затем вы используете зарегистрированную модель и скрипты оценки для развертывания в службе "экземпляры контейнеров Azure", "служба Kubernetes Azure" или в виде массива шлюзов (FPGA), поддерживающего использование полей, в качестве конечной точки HTTP на основе протокола RESTFUL. Также можно развернуть модель на Azure IoT Edge устройстве в виде модуля.

Доступные цены и функции зависят от того, выбран ли [выпуск Basic или Enterprise Edition](overview-what-is-azure-ml.md#sku) для рабочей области. Выпуск выбирается при [создании рабочей области](#create-workspace).  Также можно [выполнить обновление](#upgrade) с выпуска Basic до Enterprise Edition.

## <a name="taxonomy"></a>Классификация 

Таксономия рабочей области показана на следующей схеме:

[![Схема рабочей области](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

На схеме показаны следующие компоненты рабочей области.

+ Рабочая область может содержать Машинное обучение Azure виртуальные машины записной книжки, облачные ресурсы, настроенные с помощью среды Python, необходимой для запуска Машинное обучение Azure.
+ [Роли пользователей](how-to-assign-roles.md) позволяют предоставлять общий доступ к рабочей области другим пользователям, командам или проектам.
+ [Целевые объекты вычислений](concept-azure-machine-learning-architecture.md#compute-targets) используются для выполнения экспериментов.
+ При создании рабочей области также создаются [связанные ресурсы](#resources) .
+ [Эксперименты](concept-azure-machine-learning-architecture.md#experiments) — это обучающие программы, которые используются для построения моделей.  
+ [Конвейеры](concept-azure-machine-learning-architecture.md#ml-pipelines) — это многократно используемые рабочие процессы для обучения и повторного обучения модели.
+ [Наборы](concept-azure-machine-learning-architecture.md#datasets-and-datastores) данных помогают в управлении данными, используемыми для обучения модели и создания конвейера.
+ После создания модели, которую необходимо развернуть, необходимо создать зарегистрированную модель.
+ Используйте зарегистрированную модель и Скрипт оценки для создания [конечной точки развертывания](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Средства для взаимодействия с рабочей областью

Вы можете взаимодействовать с рабочей областью следующими способами:

+ В Интернете:
    + [Машинное обучение Azure Studio](https://ml.azure.com) 
    + [Конструктор машинное обучение Azure (Предварительная версия)](concept-designer.md) доступен только в рабочих областях [Enterprise Edition](overview-what-is-azure-ml.md#sku) .
+ В любой среде Python с [пакетом SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ В любой среде R с [машинное обучение Azureным пакетом SDK для r](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ В командной строке с помощью [расширения CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) машинное обучение Azure

## <a name="machine-learning-with-a-workspace"></a>Машинное обучение с рабочей областью

Задачи машинного обучения считывают и (или) записывают артефакты в рабочую область.

+ Запуск эксперимента для обучения модели — записывает результаты выполнения эксперимента в рабочую область.
+ Использование автоматического ML для обучения модели — записывает результаты обучения в рабочую область.
+ Зарегистрируйте модель в рабочей области.
+ Развертывание модели. использует зарегистрированную модель для создания развертывания.
+ Создание и запуск рабочих процессов с возможностью повторного использования.
+ Просмотр артефактов машинного обучения, таких как эксперименты, конвейеры, модели, развертывания.
+ Отслеживание и мониторинг моделей.

## <a name="workspace-management"></a>Управление рабочей областью

Вы также можете выполнять следующие задачи по управлению рабочими областями:

| Задача управления рабочей областью   | Microsoft Azure              | Студия | Пакет SDK для Python или пакет SDK для R       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Создание рабочего пространства        | **&check;**     | | **&check;** | **&check;** |
| Управление доступом к рабочей области    | **&check;**   || |  **&check;**    |
| Обновление до выпуска Enterprise Edition    | **&check;** | **&check;**  | |     |
| Создание ресурсов вычислений и управление ими    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Создание виртуальной машины записной книжки |   | **&check;** | |     |


## <a name='create-workspace'></a>Создание рабочей области

При создании рабочей области вы решаете, следует ли создавать ее с помощью [выпуска Basic или Enterprise Edition](overview-what-is-azure-ml.md#sku). Этот выпуск определяет функции, доступные в рабочей области. Помимо прочего, Enterprise Edition предоставляет доступ к [конструктору машинное обучение Azure](concept-designer.md) и версии Studio для создания [автоматических экспериментов машинного обучения](tutorial-first-experiment-automated-ml.md).  Дополнительную информацию и сведения о ценах см. на странице [цен на машинное обучение Azure](https://azure.microsoft.com/pricing/details/machine-learning/).

Существует несколько способов создания рабочей области.  

* Используйте [портал Azure](how-to-manage-workspace.md) для интерфейса «точка-и щелчок», чтобы проанализировать каждый шаг.
* Использование [пакета SDK машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) для создания рабочей области на лету из сценариев Python и записных книжек Юпитер
* Используйте [шаблон Azure Resource Manager](how-to-create-workspace-template.md) или интерфейс [командной строки машинное обучение Azure](reference-azure-machine-learning-cli.md) , если необходимо автоматизировать или настроить создание с использованием корпоративных стандартов безопасности.
* При работе в Visual Studio Code используйте [расширение VS Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="upgrade"></a>Обновление до выпуска Enterprise Edition

Вы можете [Обновить рабочую область с уровня "базовый" до выпуска Enterprise](how-to-manage-workspace.md#upgrade) с помощью портал Azure. Рабочую область Enterprise Edition нельзя понизить до рабочей области Basic Edition. 

## <a name="resources"></a>Связанные ресурсы

При создании новой рабочей области она автоматически создает несколько ресурсов Azure, которые используются в рабочей области:

+ [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/): регистрирует контейнеры DOCKER, используемые во время обучения и при развертывании модели. Чтобы сократить затраты, запись контроля **доступа выполняется с отложенной загрузкой** до создания образов развертывания.
+ [Учетная запись хранения Azure](https://azure.microsoft.com/services/storage/). используется в качестве хранилища данных по умолчанию для рабочей области.  Записные книжки Jupyter, используемые для виртуальной машины Машинное обучение Azure Notebook, также хранятся здесь.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): хранит сведения о мониторинге моделей.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): хранит секреты, используемые целевыми объектами вычислений и другими важными сведениями, необходимыми для рабочей области.

> [!NOTE]
> В дополнение к созданию версий можно также использовать имеющиеся службы Azure.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы приступить к работе с Машинное обучение Azure, см.:

+ [Обзор Машинное обучение Azure](overview-what-is-azure-ml.md)
+ [Создание рабочей области](how-to-manage-workspace.md)
+ [Управление рабочей областью](how-to-manage-workspace.md)
+ [Руководство. Приступая к созданию первого эксперимента ML с помощью пакета SDK для Python](tutorial-1st-experiment-sdk-setup.md)
+ [Руководство. Начало работы с Машинное обучение Azure с помощью пакета SDK для R]( tutorial-1st-r-experiment.md)
+ [Учебник. Создание первой модели классификации с помощью автоматизированного машинного обучения](tutorial-first-experiment-automated-ml.md) (доступно только в рабочих областях [Enterprise Edition](overview-what-is-azure-ml.md#sku) )
+ [Учебник. Прогнозирование цен автомобилей с помощью конструктора](tutorial-designer-automobile-price-train-score.md) (доступно только в рабочих областях [Enterprise Edition](overview-what-is-azure-ml.md#sku) )
