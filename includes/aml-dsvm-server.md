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
ms.openlocfilehash: a882a874574395095e98079cd0f8aa4a4987c749
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391789"
---
1. [Создайте рабочую область Службы машинного обучения Azure](../articles/machine-learning/service/setup-create-workspace.md).

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