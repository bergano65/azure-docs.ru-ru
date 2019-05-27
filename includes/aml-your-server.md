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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123238"
---
1. Следуйте инструкциям по [созданию рабочей области Службы машинного обучения Azure](../articles/machine-learning/service/setup-create-workspace.md#portal), чтобы:
    * создать среду Miniconda;
    * установить пакет SDK для Машинного обучения Azure для Python.
    * Создание рабочей области
    * Запишите файл конфигурации рабочей области (**aml_config/config.json**).
    
1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Добавьте файл конфигурации рабочей области с помощью любого из следующих методов:
    * В клонированный каталог скопируйте файл **aml_config\config.json**, созданный на шаге 1.

    * На [портале Azure](https://ms.portal.azure.com) выберите **Скачать config.json** в разделе **Обзор** рабочей области. 

    ![Скачивание config.json](./media/aml-dsvm-server/download-config.png)

    * Создайте новую рабочую область с помощью кода в [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Запустите сервер записной книжки из клонированного каталога.
    
    ```shell
    jupyter notebook
    ```