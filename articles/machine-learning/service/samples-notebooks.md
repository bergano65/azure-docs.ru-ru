---
title: Примеры записных книжек Jupyter
titleSuffix: Azure Machine Learning service
description: Узнайте, как работать с пакетом SDK Службы машинного обучения Azure для Python, найдя и используя примеры записных книжек Jupyter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391778"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Использование записных книжек Jupyter в Azure Machine Learning Workbench

[Репозиторий с записными книжками Машинного обучения Azure](https://github.com/azure/machinelearningnotebooks) включает последние примеры пакета SDK Машинного обучения Azure для Python. Эти записные книжки Juypter помогут вам изучить пакет SDK. Вы можете использовать их как модели для собственных проектов машинного обучения.

В этой статье показано, как получить доступ к репозиторию из следующих сред:

- [Виртуальная машина Notebook Машинного обучения Azure](#azure-machine-learning-notebook-vm)
- [Сервер собственных записных книжек](#bring-your-own-jupyter-notebook-server)
- [Виртуальные машины для обработки и анализа данных](#data-science-virtual-machine)
- [Записные книжки Azure](#azure-notebooks)

> [!NOTE]
> Клонировав репозиторий, вы получите руководства по работе с записными книжками в папке **tutorials**, а сами записные книжки, связанные с определенными функциями, — в папке **how-to-use-azureml**.

## <a name="azure-machine-learning-notebook-vm"></a>Виртуальная машина Notebook Машинного обучения Azure

Самый простой способ приступить к работе с примерами, — пройти [краткое руководство по использованию облачных записных книжек](quickstart-run-cloud-notebook.md). После этого вы получите выделенный сервер записных книжек, предварительно загруженный с пакетом SDK, и репозиторий с примером. Скачивать или устанавливать что-либо вам не нужно.

## <a name="bring-your-own-jupyter-notebook-server"></a>Использование собственного сервера Jupyter Notebook

Если вы хотите использовать собственный сервер записных книжек для локальной разработки, сделайте следующее.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Выполнив эти инструкции вы установите основные пакеты SDK, требуемые для работы с руководствами по использованию записных книжек. Другие примеры записных книжек могут потребовать установки дополнительных компонентов. См. подробнее об [установке пакета SDK Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="data-science-virtual-machine"></a>Виртуальная машина для обработки и анализа данных

Виртуальная машина для анализа и обработки данных (DSVM) — это созданный и настроенный образ специализированной виртуальной машины. Если вы [создаете DSVM](how-to-configure-environment.md#dsvm), сервер с пакетом SDK и записными книжками устанавливается и настраивается автоматически. Но вас все равно нужно создать рабочую область и клонировать репозиторий с примером.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Записные книжки Azure

Для [Записных книжек Azure](https://notebooks.azure.com/) сервер с пакетом SDK и записными книжками устанавливается и настраивается автоматически. Записные книжки Azure предоставляет полностью управляемую упрощенную среду для работы с записными книжками.

Чтобы открыть репозиторий с примерами Записных книжек Azure, перейдите к рабочей области Машинного обучения Azure на [портале Azure](https://portal.azure.com). В разделе **Обзор** щелкните **Начало работы в Записных книжках Azure**.

## <a name="next-steps"></a>Дополнительная информация

См. подробнее о [примерах записных книжек](https://aka.ms/aml-notebooks), чтобы узнать, какие функции поддерживает Машинное обучение Azure. Также вы можете изучить следующие руководства:

- [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md)

- [Руководство 1. Подготовка данных для моделирования регрессии](tutorial-data-prep.md)