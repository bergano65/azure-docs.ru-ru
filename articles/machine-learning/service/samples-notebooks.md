---
title: Примеры записных книжек Jupyter
titleSuffix: Azure Machine Learning service
description: Для изучения Службы машинного обучения Azure в Python найдите и используйте примеры записных книжек Jupyter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b597d6efa87aa2811ce42f3315698acfa17426b2
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548594"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Использование записных книжек Jupyter в Azure Machine Learning Workbench

Для вашего удобства разработан ряд записных книжек Jupyter Python, которые можно использовать для изучения Службы машинного обучения Azure. 

Благодаря документации на этом сайте, узнайте, как использовать службу и как настроить записные книжки по своему усмотрению. 

Используйте один из приведенных ниже путей, чтобы запустить сервер с этими примерами записных книжек.  Когда сервер будет запущен, найдите записные книжки для руководств в папке **tutorials** или ознакомьтесь с различными компонентами в папке **how-to-use-azureml**.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Поработайте со службой "Записные книжки Azure": к вашим услугам бесплатные записные книжки на основе Jupyter в облаке Azure

Начать работу с Записными книжками Azure очень просто. [Пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) уже установлен и настроен в [Записных книжках Azure](https://notebooks.azure.com/). Службы Azure автоматически управляют установкой и последующими обновлениями.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Использование Виртуальной машины для обработки и анализа данных (DSVM)

[Пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) и сервер записной книжки уже установлены и настроены в DSVM. 

Когда вы [создадите DSVM](how-to-configure-environment.md#dsvm), выполните следующие действия на этой виртуальной машине, чтобы запустить записные книжки.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>Использование собственного сервера записных книжек Jupyter

Чтобы создать локальный сервер Jupyter Notebook на компьютере, выполните следующие действия.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

При выполнении инструкций этого краткого руководства будут установлены пакеты, необходимые для выполнения заданий руководства и запуска записных книжек.  Другие примеры записных книжек могут потребовать установки дополнительных компонентов.  Дополнительные сведения об этих компонентах см. в статье об [установке пакета SDK для Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="next-steps"></a>Дополнительная информация

Изучите репозиторий записных книжек GitHub для Службы машинного обучения Azure [здесь](https://aka.ms/aml-notebooks)

Ниже представлен список следующих руководств:
+ [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md)

+ [Руководство 1. Подготовка данных для моделирования регрессии](tutorial-data-prep.md)
