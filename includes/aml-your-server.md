---
title: включение файла
description: включение файла
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846030"
---
- Установленный пакет SDK службы "Машинное обучение Azure" для Python. Следуйте инструкциям из статьи об [установке пакета SDK службы "Машинное обучение Azure" для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), чтобы выполнить следующее:


1. Следуйте инструкциям из статьи об [установке пакета SDK службы "Машинное обучение Azure" для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), чтобы выполнить следующее:
    * создать среду Miniconda [создание рабочих областей службы "Машинное обучение Azure" и управление ими];
    * установить пакет SDK для Машинного обучения Azure для Python.

1. Создайте [рабочую область службы "Машинное обучение Azure"](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Создайте [файл конфигурации](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Запустите сервер записной книжки из клонированного каталога.

    ```shell
    jupyter notebook
    ```