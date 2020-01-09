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
ms.date: 01/25/2019
ms.openlocfilehash: ac5656a8efe1dee932bfe753f8bdc06b787e20af
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529799"
---
1. [Создайте рабочую область Машинного обучения Azure](../articles/machine-learning/how-to-manage-workspace.md).

1. Клонируйте [репозиторий GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Добавьте файл конфигурации рабочей области в клонированный каталог с помощью любого из следующих методов:

    * На [портале Azure](https://ms.portal.azure.com) выберите **Скачать config.json** в разделе **Обзор** рабочей области. 

    ![Скачивание config.json](./media/aml-dsvm-server/download-config.png)

    * Создайте новую рабочую область с помощью кода в записной книжке [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb), расположенной в клонированном каталоге.

1. Запустите сервер записной книжки из клонированного каталога.

    ```shell
    jupyter notebook
    ```