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
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035378"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Использование записных книжек Jupyter в Azure Machine Learning Workbench

Для вашего удобства разработан ряд записных книжек Jupyter Python, которые можно использовать для изучения Службы машинного обучения Azure. 

Благодаря документации на этом сайте, узнайте, как использовать службу и как настроить записные книжки по своему усмотрению. 

Используйте один из приведенных ниже путей, чтобы запустить сервер с этими примерами записных книжек.  Когда сервер будет запущен, найдите записные книжки для руководств в папке **tutorials** или ознакомьтесь с различными компонентами в папке **how-to-use-azureml**.

## <a name="a-managed-cloud-notebook-server"></a>Сервер управляемой одноранговой сети записной книжки

С помощью облачного сервера записной книжки можно легко начать работу. Пример записной книжки и [пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) будут установлены и настроены, как только вы создадите этот облачный ресурс.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Образцы можно найти на веб-странице записной книжки.

## <a name="a-data-science-virtual-machine-dsvm"></a>Виртуальная машина для обработки и анализа данных (DSVM)

[Пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) и сервер записной книжки уже установлены и настроены в DSVM. 

Когда вы [создадите DSVM](how-to-configure-environment.md#dsvm), выполните следующие действия на этой виртуальной машине, чтобы запустить записные книжки.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>Собственный сервер Jupyter Notebook

Чтобы создать локальный сервер Jupyter Notebook на компьютере, выполните следующие действия.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

При выполнении инструкций по настройке будут установлены пакеты, необходимые для выполнения заданий руководства и запуска записных книжек.  Другие примеры записных книжек могут потребовать установки дополнительных компонентов.  Дополнительные сведения об этих компонентах см. в статье об [установке пакета SDK для Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="azure-notebooks"></a>Azure Notebook

Примеры записных книжек и [пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) уже установлены и настроены в [Записных книжках Azure](https://notebooks.azure.com/). Службы Azure автоматически управляют установкой и последующими обновлениями.

Начните работу с Записными книжками Azure с помощью [портала Azure](https://portal.azure.com).  Откройте рабочую область и в разделе **Обзор** выберите **Начало работы в Записных книжках Azure**.

## <a name="next-steps"></a>Дополнительная информация

+ Изучите пример записных книжек для Службы машинного обучения Azure в этом репозитории GitHub: https://aka.ms/aml-notebooks.

Ниже представлен список следующих руководств:
+ [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md)

+ [Руководство 1. Подготовка данных для моделирования регрессии](tutorial-data-prep.md)