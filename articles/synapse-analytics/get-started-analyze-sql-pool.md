---
title: Учебник. Приступая к анализу данных с помощью пула SQL
description: В этом учебнике вы будете использовать пример данных по такси Нью-Йорка для изучения аналитических возможностей пула SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325225"
---
# <a name="analyze-data-with-sql-pools"></a>Анализ данных с помощью пулов SQL

Azure Synapse Analytics предоставляет возможность анализировать данные с помощью пула SQL. В этом учебнике вы будете использовать пример данных по такси Нью-Йорка для изучения аналитических возможностей пула SQL.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Загрузите образец данных нью-йоркского такси в базу данных SQLDB1

1. В Synapse Studio в самом верхнем синем меню выберите знак вопроса ( **?** ).
1. Выберите **Начало работы** > **Центр начала работы**.
1. В карточке, помеченной как **Query sample data** (Образец данных запроса), выберите пул SQL с именем **SQLDB1**.
1. Выберите **Данные запроса**. Кратко отобразится уведомление "Загрузка демонстрационных данных". В верхней части Synapse Studio появится светло-синяя строка состояния, показывающая, что данные загружаются в SQLDB1.
1. Когда строка состояния станет зеленой, закройте ее.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Изучите данные нью-йоркского такси в пуле SQL

1. В Synapse Studio перейдите в центр **Данные**.
1. Перейдите в раздел **SQLDB1** > **Таблицы**. Вы увидите, что несколько таблиц загружены.
1. Щелкните правой кнопкой мыши таблицу **dbo.Trip** и выберите команду **Создать скрипт SQL** > **Выбрать первые 100 строк**.
1. Подождите, пока новый скрипт SQL будет создан и запущен.
1. Обратите внимание, что в верхней части скрипта SQL для параметра **Подключиться к** автоматически задано значение пула SQL, именуемого **SQLDB1**.
1. Замените текст скрипта SQL этим кодом и запустите его.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Этот запрос показывает, как общее время поездки и среднее расстояние поездки связаны с количеством пассажиров.
1. В окне результатов скрипта SQL измените представление с **Обзор** на **График**, чтобы увидеть визуализацию результатов в виде графика.



## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Анализ с помощью Spark](get-started-analyze-spark.md)

