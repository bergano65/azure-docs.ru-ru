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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395689"
---
1. Выполните инструкции из статьи о [создании рабочей области Службы машинного обучения Azure](../articles/machine-learning/service/setup-create-workspace.md#portal), чтобы создать среду Miniconda, рабочую область и файл конфигурации рабочей области (**aml_config/config.json**).

1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Добавьте файл конфигурации рабочей области с помощью любого из следующих методов:
    * В клонированный каталог скопируйте файл **aml_config\config.json**, созданный в ходе выполнения краткого руководства из раздела предварительных требований.
    * Создайте новую рабочую область с помощью кода в [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Запустите сервер записной книжки из клонированного каталога.
    
    ```shell
    jupyter notebook
    ```