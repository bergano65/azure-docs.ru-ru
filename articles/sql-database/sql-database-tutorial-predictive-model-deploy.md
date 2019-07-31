---
title: Руководство по развертыванию прогнозной модели на языке R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: В третьей части этого учебника вы развернете прогнозную модель на языке R с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/26/2019
ms.openlocfilehash: 9fa816b2a8e736f03c99b66b898f48bd2a483b31
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596771"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Руководство по развертыванию прогнозной модели на языке R с помощью Служб машинного обучения Azure в Базе данных SQL (предварительная версия)

В третьей части этого руководства объясняется, как развернуть прогнозную модель, разработанную на языке R, в базе данных SQL с помощью Служб машинного обучения в Базе данных SQL Azure (предварительная версия).

Вы создадите хранимую процедуру с внедренным сценарием R, который создает прогнозы с использованием модели. Так как ваша модель выполняется в Базе данных SQL Azure, ее легко можно обучить с помощью данных, хранящихся в базе данных.

Из этой статьи вы узнаете, как с помощью скриптов R, разработанных в первой и второй частях руководства, выполнить следующие задачи:

> [!div class="checklist"]
> * Создание хранимой процедуры, которая создает модель машинного обучения.
> * Сохранение модели в таблице базы данных
> * Создание хранимой процедуры, которая создает прогнозы с использованием модели.
> * Выполнение модели с новыми данными.

Из [первой части](sql-database-tutorial-predictive-model-prepare-data.md) вы узнали, как импортировать пример базы данных, а затем подготовить данные, которые будут использоваться для обучения прогнозной модели на языке R.

Из [второй части](sql-database-tutorial-predictive-model-build-compare.md) вы узнали, как создавать и обучать несколько моделей машинного обучения на языке R, а затем выбирать наиболее точную.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* В третьей части этой серии учебников предполагается, что вы ознакомились с [**первой**](sql-database-tutorial-predictive-model-prepare-data.md) и [**второй частью**](sql-database-tutorial-predictive-model-build-compare.md).

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Создание хранимой процедуры, которая создает модель

Во второй части этой серии учебников вы решили, что модель дерева принятия решений (dtree) была самой точной. Теперь с помощью разработанных вами скриптов R создайте хранимую процедуру (`generate_rental_rx_model`), которая обучает и создает модель dtree с использованием rxDTree из пакета RevoScaleR.

Выполните следующие команды в Azure Data Studio или SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Сохранение модели в таблице базы данных

Создайте таблицу в базе данных TutorialDB и сохраните модель в эту таблицу.

1. Создайте таблицу (`rental_rx_models`) для хранения модели.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Сохраните модель в таблицу как двоичный объект с именем rxDTree.

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Создание хранимой процедуры, которая создает прогнозы

Создайте хранимую процедуру (`predict_rentalcount_new`), которая создает прогнозы с использованием обученной модели и набора новых данных.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Выполнение модели с новыми данными

Теперь вы можете использовать хранимую процедуру `predict_rentalcount_new` для прогнозирования аренды на основе новых данных.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Отобразится результат, аналогичный приведенному ниже.

```results
RentalCount_Predicted
332.571428571429
```

Вы успешно создали, обучили и развернули модель в Базе данных SQL Azure. Затем вы использовали эту модель в хранимой процедуре для прогнозирования значений на основе новых данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

После использования базы данных TutorialDB удалите ее с сервера Базы данных SQL Azure.

На портале Azure сделайте следующее:

1. В меню слева на портале Azure выберите **Все ресурсы** или **Базы данных SQL**.
1. В поле **Фильтровать по имени...** введите **TutorialDB** и выберите свою подписку.
1. Выберите базу данных TutorialDB.
1. На странице **Обзор** выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В рамках третьей части этой серии учебников вы выполнили такие задачи:

* Создание хранимой процедуры, которая создает модель машинного обучения.
* Сохранение модели в таблице базы данных
* Создание хранимой процедуры, которая создает прогнозы с использованием модели.
* Выполнение модели с новыми данными.

Дополнительные сведения об использовании языка R в Службе машинного обучения Базы данных SQL Azure (предварительная версия) см. в следующих статьях:

* [Запись расширенных функций R в Базу данных SQL Azure с помощью Служб машинного обучения (предварительная версия)](sql-database-machine-learning-services-functions.md)
* [Work with R and SQL data in Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md) (Работа с данными R и SQL в Службах машинного обучения в Базе данных SQL Azure)
* [Добавление пакета R в Службу машинного обучения в Базе данных SQL Azure (предварительная версия)](sql-database-machine-learning-services-add-r-packages.md)
