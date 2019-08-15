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
ms.openlocfilehash: 09a3cc5a623be2ee5a9d50204f0902ca9f400a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857475"
---
1. [Создайте рабочую область Службы машинного обучения Azure](../articles/machine-learning/service/how-to-manage-workspace.md).

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