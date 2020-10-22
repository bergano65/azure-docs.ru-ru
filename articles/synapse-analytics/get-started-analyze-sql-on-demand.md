---
title: Руководство по Начало работы с анализом данных с помощью бессерверного SQL
description: В этом учебнике вы узнаете, как анализировать данные с помощью SQL по запросу, используя данные, хранящиеся в базах данных Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: fa87ee06e0ed01828148a58b2d2f754a40d8803a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088471"
---
# <a name="analyze-data-with-sql-on-demand"></a>Анализ данных с помощью SQL по запросу

Из этого учебника вы узнаете, как анализировать данные с помощью бессерверного SQL, используя пул SQL по запросу и данные, хранящиеся в базах данных Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>Анализ данных такси Нью-Йорка в Хранилище BLOB-объектов с помощью пула SQL по запросу

1. В центре **Данные** в разделе **Связанный** щелкните правой кнопкой мыши элемент **Хранилище BLOB-объектов Azure > Sample Datasets (Образцы наборов данных) > nyc_tlc_yellow** и выберите **первые 100 строк**.
1. Будет создан скрипт SQL со следующим кодом:

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Нажмите кнопку **Выполнить**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Анализируйте данные нью-йоркского такси в базах данных Spark с помощью SQL по запросу

Таблицы в базах данных Spark автоматически будут видимы и данные в них могут быть доступны для SQL по запросу.

1. В Synapse Studio перейдите в центр **Разработка** и создайте новый скрипт SQL.
1. Установите для параметра **Подключиться к** значение **SQL on-demand**.
1. Вставьте в скрипт следующий текст и выполните скрип.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > При первом выполнении запроса, использующего SQL по запросу, SQL по запросу потребуется примерно 10 секунд на сбор ресурсов SQL, необходимых для выполнения запросов. Следующие запросы будут выполняться гораздо быстрее.
  


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Анализ данных в службе хранилища](get-started-analyze-storage.md)
