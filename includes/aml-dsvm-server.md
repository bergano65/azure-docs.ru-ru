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
ms.openlocfilehash: 6bddfa048e4cb9cb7728c236417d4672df6f25b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476117"
---
1. [Создайте рабочую область Машинного обучения Azure](../articles/machine-learning/service/how-to-manage-workspace.md).

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