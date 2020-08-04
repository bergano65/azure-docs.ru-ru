---
title: Учебник. Приступая к анализу данных с помощью SQL по запросу
description: В этом учебнике вы узнаете, как анализировать данные с помощью SQL по запросу, используя данные, хранящиеся в базах данных Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093822"
---
# <a name="analyze-data-with-sql-on-demand"></a>Анализ данных с помощью SQL по запросу

В этом учебнике вы узнаете, как анализировать данные с помощью SQL по запросу, используя данные, хранящиеся в базах данных Spark. 

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
> [Анализ с использованием Spark](get-started-analyze-spark.md)
