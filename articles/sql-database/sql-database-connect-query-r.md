---
title: Краткое руководство по использованию Служб машинного обучения (с использованием R) в Базе данных SQL Azure (предварительная версия) | Документация Майкрософт
description: В этом разделе показано, как использовать Службы машинного обучения в Базе данных SQL Azure и как запускать сценарии R для предоставления расширенной аналитики в масштабе, а также возможность направлять вычисления и обработку туда, где находятся данные, устраняя необходимость извлечения данных через сеть.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: e15cf93514f921223fea37aa480730bba46dd195
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864955"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>Краткое руководство. Использование Служб машинного обучения (с использованием R) в Базе данных SQL Azure (предварительная версия)

В этой статье описывается, как можно использовать общедоступную предварительную версию [Служб машинного обучения (с использованием R) в Базе данных SQL Azure (предварительная версия)](sql-database-machine-learning-services-overview.md). В ней рассматриваются основы перемещения данных между Базой данных SQL и R. Кроме того, объясняется, как упаковать правильно сформированный код R в хранимой процедуре [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) для построения, обучения и использования моделей машинного обучения в Базе данных SQL.

Используйте возможности языка R для доставки расширенной аналитики и машинного обучения внутри базы данных. Данное свойство переносит вычисления и обработку в место хранения данных, тем самым исключая потребность в извлечении данных при помощи сети. Используйте также возможности корпоративных пакетов R для масштабной доставки расширенной аналитики.

Службы машинного обучения включают основу дистрибутива на языке R, которая накладывается на корпоративные пакеты R от Майкрософт. Функции и алгоритмы R от Майкрософт разработаны как для масштаба, так и для служебной программы, доставки прогнозной аналитики, статистического моделирования, визуализации данных и передовых алгоритмов машинного обучения.

Если у вас еще нет подписки Azure, [создайте учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

> [!IMPORTANT]
> Сейчас службы машинного обучения в базе данных SQL Azure находятся в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Зарегистрируйтесь, чтобы получить предварительную версию](sql-database-machine-learning-services-overview.md#signup).

## <a name="prerequisites"></a>Предварительные требования

Чтобы запустить пример кода в этих упражнениях, сначала необходимо включить в Базе данных SQL Службы машинного обучения (с использованием R). Во время использования общедоступной предварительной версии корпорация Майкрософт подключит вас и включит машинное обучение для имеющейся или новой базы данных. Выполните шаги, приведенные в разделе [Sign up for the preview](sql-database-machine-learning-services-overview.md#signup) (Регистрация для получения предварительной версии).

Можно подключиться к Базе данных SQL и запустить скрипты R любого средства управления базами данных или запроса, если возможно подключиться к Базе данных SQL и выполнить запрос или хранимую процедуру T-SQL. В этом кратком руководстве используется [SQL Server Management Studio](sql-database-connect-query-ssms.md).

Для упражнения [добавления пакета](#add-package) необходимо также установить [R](https://www.r-project.org/) и [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) на локальном компьютере.

Это руководство также предусматривает настройку правила брандмауэра на уровне сервера. Краткое руководство, в котором показано, как это сделать, см. в разделе [Правило брандмауэра для логического сервера](sql-database-server-level-firewall-rule.md).

## <a name="verify-r-exists"></a>Проверка наличия R

Можно убедиться, что Службы машинного обучения (с использованием R) включены для Базы данных SQL. Для этого сделайте следующее.

1. Откройте SQL Server Management Studio и подключитесь к базе данных SQL. Дополнительные сведения о подключении см. в статье [Краткое руководство. Подключение к базе данных SQL Azure и создание запросов к ней с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md).

1. Выполните указанный внизу код. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    Если все хорошо, вы должны увидеть результирующее сообщение, подобное этому.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. Если вы получаете какие-либо ошибки, это может быть связано с тем, что общедоступная предварительная версия Служб машинного обучения (с использованием R) не включена для базы данных SQL. Узнайте, как зарегистрироваться для получения общедоступной предварительной версии, выше.

## <a name="grant-permissions"></a>Предоставление разрешений

Если вы являетесь администратором, можете настроить автоматическое выполнение внешнего кода. Другим пользователям нужно предоставить разрешение.

Прежде чем выполнить команду, замените `<username>` допустимым именем пользователя для входа в базу данных.

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>Основное взаимодействие с R

Код R можно выполнять в Базе данных SQL двумя способами:

+ Добавьте скрипт R в качестве аргумента системной хранимой процедуры [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).
+ Из [удаленного R-клиента](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client) подключитесь к базе данных SQL и выполните код, используя Базу данных SQL в качестве контекста вычисления.

Следующее упражнение сфокусировано на первой модели взаимодействия: как передать код R в хранимую процедуру.

1. Запустите простой скрипт, чтобы увидеть, как скрипт R выполняется в базе данных SQL.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. С учетом того, что все настроено правильно, вычисляется корректный результат, а функция R `print`возвращает результат в окне **Сообщения**.

    **Результаты**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    При получении сообщений **stdout** полезен при тестировании кода, но чаще всего необходимо возвращать результаты в табличном формате, чтобы можно было использовать его в приложении или записать в таблицу. Смотрите раздел входных и выходных данных ниже для получения дополнительной информации.

Помните, что все внутри аргумента `@script` должно быть допустимым кодом R.

## <a name="inputs-and-outputs"></a>Входные и выходные данные

По умолчанию [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) принимает один набор входных данных, который обычно можно указать в форме допустимого SQL-запроса. Другие типы входных данных могут передаваться как переменные SQL.

Хранимая процедура возвращает один фрейм данных R как выходные данные, но можно также выводить скаляры и модели в виде переменных. Например, можно вывести обученную модель в виде двоичной переменной и передать ее в инструкцию T-SQL INSERT, чтобы записать эту модель в таблицу. Вы также можете создавать графики (в двоичном формате) или скаляры (отдельные значения, такие как дата и время, время, затраченное на обучение модели, и т. д.).

Давайте рассмотрим только стандартные входные и выходные переменные sp_execute_external_script: `InputDataSet` и `OutputDataSet`.

1. Создайте небольшую таблицу тестовых данных, выполнив следующую инструкцию T-SQL:

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    После создания таблицы используйте следующую инструкцию для запроса в таблицу:
  
    ```sql
    SELECT * FROM RTestData
    ```

    **Результаты**

    ![Содержимое таблицы RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

2. Данные из таблицы можно получить в качестве входных данных для скрипта R. Выполните приведенную ниже инструкцию. Она получает данные из таблицы, совершает циклический проход через среду выполнения R и возвращает значения с именем столбца *NewColName*.

    Данные, возвращаемые запросом, передаются в среду выполнения R, которая возвращает данные в Базу данных SQL в качестве фрейма данных. Предложение WITH RESULT SETS определяет схему возвращаемой таблицы данных для Базы данных SQL.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **Результаты**

    ![Выходные данные из скрипта R, который возвращает данные из таблицы](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. Давайте изменим имя входных или выходных переменных. Приведенный выше скрипт использовал имена входных и выходных переменных по умолчанию, _InputDataSet_ и _OutputDataSet_. Для определения входных данных, связанных с _InputDatSet_, используется переменная *\@input_data_1*.

    В этом скрипте имена выходных и входных переменных для хранимой процедуры были изменены на *SQL_out* и *SQL_in*:

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Обратите внимание, что в R учитывается регистр, поэтому регистр входных и выходных переменных в `@input_data_1_name` и `@output_data_1_name` должен совпадать с данными в коде R в `@script`. 

    Помимо этого важен порядок параметров. Необходимо сначала указать обязательные параметры *\@input_data_1* и *\@output_data_1*, чтобы использовать дополнительные параметры *\@input_data_1_name* и *\@output_data_1_name*.

    В качестве параметра может быть передан только один входной набор данных, и можно возвращать только один набор данных. Однако вы можете вызывать другие наборы данных из кода R, а также возвращать выходные данные других типов в дополнение к набору данных. Вы также можете добавить ключевое слово OUTPUT к любому параметру, чтобы он возвращался с результатами. 

    Инструкция `WITH RESULT SETS` определяет схему для данных, которые используются в Базе данных SQL. Для каждого столбца, возвращаемого из R, необходимо предоставить типы данных, совместимые с SQL. Вы можете использовать определение схемы для предоставления новых имен столбцов, так как нет необходимости использовать имена столбцов из фрейма данных R.

4. Помимо того, вы можете создать значения с помощью скрипта R и оставить строку ввода запроса в _@input_data_1_ пустой.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **Результаты**

    ![Результаты запроса с помощью @script в качестве входных данных](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Проверка версии R

Если вы хотите узнать, какая версия R установлена в базе данных SQL, выполните следующие действия:

1. Выполните приведенный ниже скрипт в базе данных SQL.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. Функция R `print` возвращает версию в окне **Сообщения**. В приведенном ниже примере выходных данных видно, что в этом случае База данных SQL имеет версию R 3.4.4.

    **Результаты**

    ```text
    STDOUT message(s) from external script:
                   _
    platform       x86_64-w64-mingw32
    arch           x86_64
    os             mingw32
    system         x86_64, mingw32
    status
    major          3
    minor          4.4
    year           2018
    month          03
    day            15
    svn rev        74408
    language       R
    version.string R version 3.4.4 (2018-03-15)
    nickname       Someone to Lean On
    ```

## <a name="list-r-packages"></a>Вывод списка пакетов R

Корпорация Майкрософт предоставляет ряд пакетов R, предварительно установленных с помощью Служб машинного обучения в базе данных SQL. Чтобы просмотреть список установленных пакетов R, включая версию, зависимости, лицензию и сведения о пути к библиотеке, выполните следующие действия. Чтобы добавить дополнительные пакеты, обратитесь к разделу о [добавлении пакета](#add-package).

1. Выполните приведенный ниже скрипт в базе данных SQL.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. Выходные данные выходят из `installed.packages()` в R и возвращаются в виде результирующего набора.

    **Результаты**

    ![Установленные пакеты в R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>Создание прогнозной модели

Вы можете обучить модель с помощью R и сохранить ее в таблице в базе данных SQL. В этом упражнении вы будете тренировать простую регрессионную модель, которая предсказывает расстояние торможения автомобиля на основе скорости. Вы будете использовать набор данных `cars`, входящий в состав R, так как он мал и понятен.

1. Сначала создайте таблицу для сохранения обучающих данных.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    Многие наборы данных, малые и большие, включаются в среду выполнения R. Чтобы получить список наборов данных, установленных с помощью R, введите `library(help="datasets")` в командной строке R.

2. Создайте регрессионную модель. Данные о скорости автомобиля содержат два числовых столбца, `dist` и `speed`. Есть несколько наблюдений некоторых скоростей. Из этих данных вы создадите модель линейной регрессии, описывающую некоторую связь между скоростью автомобиля и расстоянием, необходимым для его остановки.

    Требования линейной модели просты:

   - Определите формулу, описывающую связь между зависимой переменной `speed` и независимой переменной `distance`.

   - Предоставьте входные данные для использования при обучении модели.

     > [!TIP]
     > Если вам нужно переподготовить линейные модели, мы рекомендуем следующее руководство, в котором описан процесс установки модели с помощью rxLinMod: [Fitting Linear Models using RevoScaleR](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model) (Подготовка линейных моделей с помощью RevoScaleR)

     Для построения модели необходимо определить формулу внутри кода R и передать данные в качестве входного параметра.

     ```sql
     DROP PROCEDURE IF EXISTS generate_linear_model;
     GO
     CREATE PROCEDURE generate_linear_model
     AS
     BEGIN
       EXEC sp_execute_external_script
       @language = N'R'
       , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
           trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
       , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
       , @input_data_1_name = N'CarsData'
       , @output_data_1_name = N'trained_model'
       WITH RESULT SETS ((model VARBINARY(max)));
     END;
     GO
     ```

     Первым аргументом rxLinMod является параметр *формулы*, определяющий расстояние в зависимости от скорости. Входные данные хранятся в переменной `CarsData`, которая заполняется с помощью SQL-запроса. Если для входных данных не назначено определенное имя, имя переменной по умолчанию будет _InputDataSet_.

2. Затем создайте таблицу, в которой хранится модель, чтобы можно было переобучать или использовать ее для прогнозирования. Выходные данные пакета R, который создает модель, обычно представляют собой **двоичный объект**. Таким образом, таблица должна предоставлять столбец типа **VARBINARY(max)**.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. Чтобы сохранить модель, выполните следующую инструкцию Transact-SQL для вызова хранимой процедуры, создания модели и сохранения ее в таблице.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    Обратите внимание, что если вы запустите этот код во второй раз, вы получите эту ошибку:

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    Один из вариантов, который поможет избежать этой ошибки, — обновить имя для каждой новой модели. Например, можно изменить имя на нечто более описательное и включить тип модели, день создания и т. д.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. Как правило, выходные данные R из хранимой процедуры [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ограничиваются одним фреймом данных.

    Однако в дополнение к фрейму данных можно возвращать выходные данные других типов, таких как скаляры.

    Например, предположим, что требуется обучить модель, но сразу же просмотреть таблицу коэффициентов из модели. Вы можете создать таблицу коэффициентов в качестве основного результирующего набора и вывести обученную модель в переменную SQL. Вы можете сразу же повторно использовать модель, вызвав переменную, или можно сохранить модель в таблице, как показано здесь.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **Результаты**

    ![Обученная модель с дополнительными выходными данными](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>Прогноз

Используйте модель, созданную в предыдущем разделе, для оценки прогнозов по новым данным. Для выполнения _оценки_ с использованием новых данных получите одну из обученных моделей из таблицы, а затем вызовите новый набор данных, на котором основываются прогнозы. Оценка — это термин, который иногда используется в процессе обработки и анализа данных и означает генерирование прогнозов, вероятностей или других значений, основанных на новых данных, которые поступают в обученную модель.

1. Сначала создайте таблицу с новыми данными о скорости. Вы заметили, что исходные данные обучения останавливаются на скорости 25 миль в час? Это потому, что исходные данные были основаны на эксперименте от 1920 года! Вы можете задаться вопросом, как долго автомобиль 1920-х годов будет тормозить, чтобы остановиться, предполагая, что он может разогнаться до 60 миль/ч или даже 100 миль/ч? Чтобы ответить на этот вопрос, необходимо указать новые значения скорости.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    В этом примере, так как модель основана на алгоритме **rxLinMod**, предоставленном в составе пакета **RevoScaleR**, вызовите функцию [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict), а не универсальную функцию R `predict`.

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
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
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    Приведенный выше скрипт выполняет следующие действия:

   + Используйте инструкцию SELECT для получения одной модели из таблицы и передайте ее в качестве входного параметра.

   + После извлечения модели из таблицы вызовите функцию `unserialize` для модели.

       > [!TIP] 
       > Кроме того, проверьте новые [функции сериализации](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel), предоставляемые RevoScaleR, которые поддерживают оценку в реальном времени.
   + Примените функцию `rxPredict` с соответствующими аргументами к модели и предоставьте новые входные данные.

   + В этом примере функция `str` добавляется на этапе тестирования, чтобы проверить схему данных, возвращаемых из R. Оператор можно удалить позже.

   + Имена столбцов, используемые в скрипте R, не обязательно передаются в выходные данные хранимой процедуры. Здесь мы использовали предложение WITH RESULTS для определения некоторых новых имен столбцов.

     **Результаты**

     ![Результирующий набор для прогнозирования расстояния торможения](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

     Помимо того, можно использовать прогноз [в Transact-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) для создания прогнозируемого значения или оценки на основе хранимой модели.

<a name="add-package"></a>

## <a name="add-a-package"></a>Добавление пакета

Если необходимо использовать пакет, который еще не установлен в базе данных SQL, его можно установить с помощью [sqlmlutils](https://github.com/Microsoft/sqlmlutils). Чтобы установить пакет, выполните следующие действия.

1. Скачайте последнюю версию ZIP-файла **sqlmlutils** из [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) на локальный компьютер. Вам не нужно распаковывать файл.

1. Если у вас не установлен R, скачайте R из [www.r-project.org](https://www.r-project.org/) и установите его на локальный компьютер. Пакет R доступен для операционной системы Windows, macOS и Linux. В этом примере мы используем Windows.

1. Сначала установите пакет **RODBCext**, который является обязательным условием для **sqlmlutils**. **RODBCext** также устанавливает зависимость пакета **RODBC**. Откройте **командную строку** и выполните следующую команду:

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    Если вы получаете сообщение об ошибке, например "R не является внутренней или внешней командой, исполняемой программой или пакетным файлом", скорее всего, это означает, что путь к R.exe не включен в вашу переменную среды **PATH** в Windows. Вы можете либо добавить каталог в переменную среды, либо перейти к каталогу в командной строке (например, `cd C:\Program Files\R\R-3.5.1\bin`), прежде чем выполнить команду.

1. Используйте команду **R CMD INSTALL** для установки **sqlmlutils**. Укажите путь к каталогу, в который вы скачали ZIP-файл, и имя ZIP-файла. Например: 

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    Должен быть получен приметно такой результат:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. В этом примере вы будете использовать RStudio Desktop в качестве IDE. При желании можно использовать другую среду IDE. Скачайте и установите RStudio Desktop из [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/), если вы еще не установили RStudio.

1. Откройте **RStudio** и создайте файл **скрипта R**. Используйте следующий код R для установки пакета с помощью sqlmlutils. В приведенном ниже примере будет установлен пакет [glue](https://cran.r-project.org/web/packages/glue/), который может форматировать и интерполировать строку.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > **Область** может быть либо **общедоступной**, либо **частной**. Общедоступная область полезна администратору базы данных для установки пакетов, которые могут использовать все пользователи. Частная область делает пакет доступным только для пользователя, который его устанавливает. Если область не указана, областью по умолчанию является **частная**.

1. Теперь убедитесь, что установлен пакет **glue**.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **Результаты**

    ![Содержимое таблицы RTestData](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. После установки пакета можно использовать его в скрипте R через **sp_execute_external_script**. Откройте **SQL Server Management Studio** и подключитесь к базе данных SQL. Выполните следующий скрипт:

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    На вкладке сообщения появится следующий результат.

    **Результаты**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. Если вы хотите удалить пакет, выполните следующий сценарий R в **RStudio** на локальном компьютере.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> Другим способом установки пакетов R в базу данных SQL является передача пакета R из потока байтов с помощью [создания внешней библиотеки](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Службах машинного обучения содержатся в указанных ниже статьях. Хотя некоторые из этих статьей предназначены для SQL Server, большая часть информации также применима к Службам машинного обучения (с использованием R) в Базе данных SQL Azure.

- [Machine Learning Services (with R) in Azure SQL Database (preview)](sql-database-machine-learning-services-overview.md) (Службы машинного обучения (с использованием R) в Базе данных SQL Azure (предварительная версия))
- [Службы машинного обучения SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Руководство Анализ данных R для разработчиков SQL](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Комплексное пошаговое руководство по обработке и анализу данных для R и SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Руководство Use RevoScaleR R functions with SQL Server data](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages) (Руководство. Использование функций R RevoScaleR с данными SQL Server)
