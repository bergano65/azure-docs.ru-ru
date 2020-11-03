---
title: Примеры записных книжек Jupyter
titleSuffix: Azure Machine Learning
description: Узнайте, как работать с пакетом SDK Машинного обучения Azure для Python, используя примеры записных книжек Jupyter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8b4256f33a143c711a23a3564c2a46c69e0783b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675776"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Использование записных книжек Jupyter в Машинном обучении Azure

> [!NOTE] 
> Репозиторий примеров, поддерживаемый сообществом, можно найти здесь: https://github.com/Azure/azureml-examples.

[Репозиторий с записными книжками службы "Машинное обучение Azure"](https://github.com/azure/machinelearningnotebooks) включает последние примеры пакета SDK Машинного обучения Azure для Python. Эти записные книжки Juypter помогут вам изучить пакет SDK. Вы можете использовать их как модели для собственных проектов машинного обучения.

В этой статье показано, как получить доступ к репозиторию из следующих сред:

- [Вычислительная операция Машинного обучения Azure](#notebookvm)
- [Сервер собственных записных книжек](#byo)
- [Виртуальные машины для обработки и анализа данных](#dsvm)

> [!NOTE]
> Клонировав репозиторий, вы получите руководства по работе с записными книжками в папке **tutorials** , а сами записные книжки, связанные с определенными функциями, — в папке **how-to-use-azureml**.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Получение примеров вычислительных операций Машинного обучения Azure

Самый простой способ приступить к работе с примерами, — ознакомиться с руководством по [ настройке среды и рабочей области](tutorial-1st-experiment-sdk-setup.md). После этого вы получите выделенный сервер записных книжек, предварительно загруженный с пакетом SDK, и репозиторий с примером. Скачивать или устанавливать что-либо вам не нужно.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Получение примеров на сервере записных книжек

Если вы хотите использовать собственный сервер записных книжек для локальной разработки, сделайте следующее.

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

Выполнив эти инструкции вы установите основные пакеты SDK, требуемые для работы с руководствами по использованию записных книжек. Другие примеры записных книжек могут потребовать установки дополнительных компонентов. См. подробнее об [установке пакета SDK Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Получение примеров в среде "Виртуальная машина для обработки и анализа данных"

Виртуальная машина для анализа и обработки данных (DSVM) — это созданный и настроенный образ специализированной виртуальной машины. Если вы [создаете DSVM](how-to-configure-environment.md#dsvm), сервер с пакетом SDK и записными книжками устанавливается и настраивается автоматически. Но вас все равно нужно создать рабочую область и клонировать репозиторий с примером.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Дальнейшие действия

См. [примеры записных книжек](https://github.com/Azure/MachineLearningNotebooks), чтобы узнать, какие функции поддерживает Машинное обучение Azure.

Дополнительные образцы проектов и примеры см. в следующих репозиториях GitHub:
+ [Azure/azureml-examples](https://github.com/Azure/azureml-examples)
+ [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
+ [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

Ниже представлен список следующих руководств:

- [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md)

- [Руководство 1. Подготовка данных для моделирования регрессии](tutorial-auto-train-models.md)
