---
title: включить файл
description: включить файл
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 6e7580abfd113a279e2223b9cc9665f6a0b86bc8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553367"
---
1. Чтобы установить пакет SDK службы "Машинное обучение Azure" для Python, выполните инструкции из [этой статьи](/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Создайте [рабочую область Машинного обучения Azure](../articles/machine-learning/how-to-manage-workspace.md).

1. Создайте [файл конфигурации](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Запустите сервер записной книжки из клонированного каталога.

    ```bash
    jupyter notebook
    ```