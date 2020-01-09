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
ms.date: 09/26/2019
ms.openlocfilehash: 1260ee32c267a2652ad02e74c3978654bf2f9669
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529798"
---
1. Чтобы установить пакет SDK службы "Машинное обучение Azure" для Python, выполните инструкции из [этой статьи](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Создайте [рабочую область Машинного обучения Azure](../articles/machine-learning/how-to-manage-workspace.md).

1. Создайте [файл конфигурации](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Запустите сервер записной книжки из клонированного каталога.

    ```shell
    jupyter notebook
    ```
