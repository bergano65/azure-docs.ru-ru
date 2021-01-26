---
title: Руководство по Начало работы с анализом данных с помощью бессерверного пула SQL
description: В этом руководстве показано, как анализировать данные с помощью бессерверного пула SQL, используя данные, хранимые в базах данных Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209412"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Анализ данных с помощью бессерверного пула SQL в Azure синапсе Analytics

В этом руководстве показано, как анализировать данные с помощью бессерверного пула SQL, используя данные, хранимые в базах данных Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Анализ данных такси Нью-Йорка в Хранилище BLOB-объектов с помощью бессерверного пула SQL

1. В центре **Данные** в разделе **Связанный** щелкните правой кнопкой мыши элемент **Хранилище BLOB-объектов Azure > Sample Datasets (Образцы наборов данных) > nyc_tlc_yellow**, выберите **New SQL Script** (Создать скрипт SQL) и щелкните **Выбрать первые 100 строк**.
1. Будет создан скрипт SQL со следующим кодом:

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Нажмите кнопку **Выполнить**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>Анализ данных такси Нью-Йорка в базах данных Spark с помощью бессерверного пула SQL

Таблицы в базах данных Spark отображаются автоматически, а данные в них можно запрашивать с помощью бессерверного пула SQL.

1. В Synapse Studio перейдите в центр **Разработка** и создайте новый скрипт SQL.
1. Задайте для параметра **Подключиться к** значение **Встроенный** (бессерверный пул SQL).
1. Вставьте в скрипт следующий текст и выполните скрип.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > При первом выполнении запроса, использующего бессерверный пул SQL, потребуется примерно 10 секунд на сбор ресурсов SQL, необходимых для выполнения запросов. Следующие запросы будут выполняться гораздо быстрее.
  


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Анализ данных в службе хранилища](get-started-analyze-storage.md)
