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
ms.date: 09/26/2019
ms.openlocfilehash: e11b1c8c19db73ace069d4bfda3516cdbbceaa02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476105"
---
1. Чтобы установить пакет SDK службы "Машинное обучение Azure" для Python, выполните инструкции из [этой статьи](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Создайте [рабочую область Машинного обучения Azure](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Создайте [файл конфигурации](../articles/machine-learning/service/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Запустите сервер записной книжки из клонированного каталога.

    ```shell
    jupyter notebook
    ```
