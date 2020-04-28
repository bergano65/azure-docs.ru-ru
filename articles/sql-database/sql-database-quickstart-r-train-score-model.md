---
title: Создание и обучение прогнозной модели на языке R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Создайте простую прогнозную модель на языке R с помощью Служб машинного обучения Azure в Базе данных SQL (предварительная версия), а затем спрогнозируйте результат с помощью новых данных.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3c88bdf141e7784837a89c8104574d97c93296dc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460160"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Краткое руководство. Создание и обучение прогнозной модели на языке R с помощью Служб машинного обучения Azure в Базе данных SQL (предварительная версия)

В этом кратком руководстве вы создадите и обучите прогнозную модель на языке R, сохраните ее в таблице в базе данных, а затем используете для прогнозирования значений новых данных с помощью Служб машинного обучения (с поддержкой R) в Базе данных SQL Azure.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [База данных SQL Azure](sql-database-single-database-get-started.md) с [правилом брандмауэра на уровне сервера](sql-database-server-level-firewall-rule.md)
- [Службы машинного обучения](sql-database-machine-learning-services-overview.md) (с поддержкой R) включены.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

В этом примере используется простая модель регрессии для прогнозирования расстояния до остановки автомобиля на основе скорости с помощью набора данных **автомобилей** (с поддержкой R).

> [!TIP]
> Многие наборы данных входят в среду выполнения R, чтобы получить список установленных наборов данных, введите `library(help="datasets")` из командной строки R.

## <a name="create-and-train-a-predictive-model"></a>Создание и обучение прогнозной модели

Данные о скорости автомобилей в наборе данных **cars** содержат два числовых столбца: **dist** и **speed**. Они включают в себя результаты наблюдения за торможением на разных скоростях. На основе этих данных вы создадите линейную регрессионную модель, описывающую некоторую связь между скоростью автомобиля и расстоянием, необходимым для его остановки.

Требования линейной модели просты:
- Определите формулу, описывающую связь между зависимой переменной *speed* и независимой переменной *distance*.
- Предоставьте входные данные для использования при обучении модели.

> [!TIP]
> Если вам требуется средство обновления линейных моделей, ознакомьтесь со следующим руководством, в котором описывается процесс компоновки моделей с помощью функции rxLinMod: [Fitting Linear Models using RevoScaleR](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model) (Подготовка линейных моделей с помощью RevoScaleR)

Далее вы настроите данные для обучения, создадите регрессионную модель, обучите ее с помощью настроенных данных, а затем сохраните эту модель в таблице SQL.

1. Откройте **SQL Server Management Studio** и подключитесь к базе данных SQL.

   Если вам нужна помощь с подключением, ознакомьтесь с разделом [Краткое руководство. Подключение к базе данных SQL Azure и создание запросов к ней с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md).

1. Создайте таблицу **CarSpeed** для сохранения данных для обучения.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Создайте регрессионную модель с помощью `rxLinMod`. 

   Для создания модели необходимо определить формулу в коде R и передать данные для обучения **CarSpeed** в качестве входного параметра.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Первым аргументом rxLinMod является параметр *формулы*, определяющий расстояние в зависимости от скорости. Входные данные хранятся в переменной `CarsData`, которая заполняется с помощью SQL-запроса.

1. Создайте таблицу для хранения модели, чтобы ее можно было позже использовать для прогнозирования. 

   Выходными данными пакета R, который создает модель, обычно является **двоичный объект**, поэтому таблица должна содержать столбец типа **VARBINARY(max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Теперь вызовите хранимую процедуру, создайте модель и сохраните ее в таблице.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Обратите внимание, что если вы запустите этот код во второй раз, вы получите эту ошибку:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Один из вариантов, который поможет избежать этой ошибки, — изменять имя для каждой новой модели. Например, можно изменить имя на нечто более описательное и включить тип модели, день создания и т. д.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Просмотр таблицы коэффициентов

Как правило, выходные данные R из хранимой процедуры [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ограничиваются одним фреймом данных. Однако в дополнение к фрейму данных можно возвращать выходные данные других типов, таких как скаляры.

Предположим, что требуется обучить модель, но сразу же просмотреть таблицу коэффициентов из модели. Для этого можно создать таблицу коэффициентов в качестве основного результирующего набора и вывести обученную модель в переменную SQL. Вы можете сразу же повторно использовать модель, вызвав эту переменную, или можете сохранить модель в таблице, как показано здесь.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Результаты**

![Обученная модель с дополнительными выходными данными](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Оценка новых данных с использованием обученной модели

По *оценкой* в области обработки и анализа данных понимается вычисление прогнозов, вероятностей и других значений на основе данных, подаваемых в обученную модель. Для оценки прогнозов на основе новых данных вы будете использовать модель, созданную в предыдущем разделе.

Вы заметили, что исходные данные обучения останавливаются на скорости 25 миль в час? Это потому, что исходные данные были основаны на эксперименте от 1920 года! Вы можете задаться вопросом, как долго автомобиль 1920-х годов будет тормозить, чтобы остановиться, если он может разогнаться до 60 миль/ч или даже 100 миль/ч? Чтобы ответить на этот вопрос, необходимо указать новые значения скорости для модели.

1. Создайте таблицу с новыми данными о скорости.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Спрогнозируйте тормозной путь на основе этих новых значений.

   Так как модель основана на алгоритме **rxLinMod**, предоставленном в составе пакета **RevoScaleR**, вызовите функцию [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict), а не универсальную функцию R `predict`.

   Этот пример сценария:
   - Использует инструкцию SELECT для получения одной модели из таблицы.
   - Передает ее в качестве входного параметра.
   - Вызывает функцию `unserialize` для модели.
   - Применяет к модели функцию `rxPredict` с соответствующими аргументами.
   - Предоставляет новые входные данные.

   > [!TIP]
   > Оценка в реальном времени описывается в разделе [rxSerializeModel: RevoScaleR Model Serialization and Unserialization](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) (rxSerializeModel: сериализация и десериализация модели RevoScaleR) на примере RevoScaleR.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Результаты**

   ![Результирующий набор для прогнозирования расстояния торможения](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> В этом примере сценария функция `str` добавляется на этапе тестирования, чтобы проверить схему данных, возвращаемых из R. Оператор можно удалить позже.
>
> Имена столбцов, используемые в скрипте R, не обязательно передаются в выходные данные хранимой процедуры. Здесь предложение WITH RESULTS используется для определения нескольких новых имен столбцов.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Службе машинного обучения с использованием R в Базе данных SQL Azure (предварительная версия) см. в следующих статьях.

- [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md) (Служба машинного обучения с использованием R в Базе данных SQL Azure (предварительная версия))
- [Создание и выполнение простых сценариев R в Службах машинного обучения в Базе данных SQL Azure (предварительная версия)](sql-database-quickstart-r-create-script.md)
- [Запись расширенных функций R в Базу данных SQL Azure с помощью Служб машинного обучения (предварительная версия)](sql-database-machine-learning-services-functions.md)
- [Work with R and SQL data in Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md) (Работа с данными R и SQL в Службе машинного обучения в Базе данных SQL Azure)
