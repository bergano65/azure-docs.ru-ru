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
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391797"
---
1. Следуйте инструкциям по [созданию рабочей области Службы машинного обучения Azure](../articles/machine-learning/service/setup-create-workspace.md#portal), чтобы выполнить следующее:
    * создать среду Miniconda;
    * установить пакет SDK для Машинного обучения Azure для Python.
    * Создание рабочей области
    * Запишите файл конфигурации рабочей области (**aml_config/config.json**).

1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Запустите сервер записной книжки из клонированного каталога.

    ```shell
    jupyter notebook
    ```