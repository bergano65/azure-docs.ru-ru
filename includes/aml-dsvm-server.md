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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302203"
---
1. Чтобы создать рабочую область для службы "Машинное обучение Azure" с помощью Python, выполните действия, описанные в [этом кратком руководстве](../articles/machine-learning/service/quickstart-create-workspace-with-python.md).  При необходимости можете пропустить раздел об **использовании записной книжки**.
1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Добавьте файл конфигурации рабочей области с помощью любого из следующих методов:
    * С помощью предварительных требований краткого руководства скопируйте созданный файл **aml_config\config.json** в клонированный каталог.
    * Создайте новую рабочую область с помощью кода в записной книжке [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb), расположенной в клонированном каталоге.
1. Запустите сервер записной книжки из клонированного каталога.
    
    ```shell
    jupyter notebook
    ```