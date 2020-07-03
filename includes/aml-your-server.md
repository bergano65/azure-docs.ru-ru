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
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486007"
---
1. Чтобы установить пакет SDK службы "Машинное обучение Azure" для Python, выполните инструкции из [этой статьи](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

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