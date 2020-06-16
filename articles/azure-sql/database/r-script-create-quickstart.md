---
title: Создание и выполнение простых сценариев R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Выполнение простых сценариев R в Службах машинного обучения в Базе данных SQL Azure (предварительная версия).
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
ms.openlocfilehash: 9b78b696b42431c744c30c91a730fdc7ec8c1032
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324612"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Краткое руководство. Создание и выполнение простых сценариев R в Службах машинного обучения в Базе данных SQL Azure (предварительная версия)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

В этом кратком руководстве вы создадите и выполните набор сценариев R с помощью Служб машинного обучения (с поддержкой R) в Базе данных SQL Azure.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.
- [База данных в службе "База данных SQL Azure"](single-database-create-quickstart.md) с [правилом брандмауэра на уровне сервера](firewall-create-server-level-portal-quickstart.md)
- [Службы машинного обучения](machine-learning-services-overview.md) (с поддержкой R) включены.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

В этом примере для заключения правильно сформированного сценария R используется хранимая процедура [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

## <a name="run-a-simple-script"></a>Выполнение простого сценария

Чтобы выполнить сценарий R, необходимо передать его в качестве аргумента в системную хранимую процедуру [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

На следующих этапах вы запустите этот сценарий R в базе данных:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Откройте **SQL Server Management Studio** и подключитесь к базе данных.

   Если вам нужна помощь с подключением, ознакомьтесь с разделом [Краткое руководство. Подключение к базе данных в службе "База данных SQL Azure" и создание запросов к ней с помощью SQL Server Management Studio](connect-query-ssms.md).

1. Передайте полный сценарий R в хранимую процедуру [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Сценарий передается с помощью аргумента `@script`. Все, что находится внутри аргумента `@script`, должно быть допустимым кодом R.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Если вы получаете сообщения о каких-либо ошибках, это может быть связано с тем, что общедоступная предварительная версия Служб машинного обучения (с использованием R) не включена для базы данных. См. [предварительные требования](#prerequisites) выше.

   > [!NOTE]
   > Если вы являетесь администратором, можете настроить автоматическое выполнение внешнего кода. Разрешение можно предоставить другим пользователям с помощью команды:
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<username\>* .

2. Далее вычисляется правильный результат, и функция R `print` возвращает результат в окне **Сообщения**.

   Он должен выглядеть примерно так.

    **Результаты**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Выполнение сценария Hello World

Типичный пример — сценарий, который просто выводит строку "Hello World". Выполните следующую команду.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Входные данные для этой хранимой процедуры.

| | |
|-|-|
| @language | Определяет вызываемое расширение языка (в данном случае R). |
| @script | Определяет команды, которые передаются в среду выполнения R. Весь сценарий R должен содержаться в этом аргументе в виде текста в Юникоде. Также можно добавить текст в переменную типа **nvarchar**, а затем вызвать ее. |
| @input_data_1 | Данные, возвращаемые запросом, передаются в среду выполнения R, которая возвращает их в виде кадра данных. |
|WITH RESULT SETS | Это предложение определяет схему возвращаемой таблицы данных. В данном случае добавляется "Hello World" в качестве имени столбца и **int** в качестве типа данных. |

Эта команда выводит следующий текст:

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Использование входных и выходных данных

По умолчанию [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) принимает один входной набор данных, который обычно можно указать в форме допустимого SQL-запроса. Затем она возвращает один кадр данных R в качестве выходных данных.

Давайте используем стандартные входные и выходные переменные [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** и **OutputDataSet**.

1. Создайте небольшую таблицу тестовых данных.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Используйте инструкцию `SELECT` для запроса в эту таблицу.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Результаты**

    ![Содержимое таблицы RTestData](./media/r-script-create-quickstart/select-rtestdata.png)

1. Выполните следующий сценарий R. Он получает данные из таблицы с помощью инструкции `SELECT`, передает их через среду выполнения R и возвращает результаты в виде кадра данных. Предложение `WITH RESULT SETS` определяет схему возвращаемой таблицы данных для Базы данных SQL, добавляя имя столбца *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Результаты**

    ![Выходные данные сценария R, возвращающего данные из таблицы](./media/r-script-create-quickstart/r-output-rtestdata.png)

1. Теперь изменим имена входных и выходных переменных. Имена входных и выходных переменных по умолчанию°— **InputDataSet** и **OutputDataSet**. Сценарий изменяет их на **SQL_in** и **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Обратите внимание, что в R учитывается регистр. Значения входных и выходных переменных, используемых в сценарии R (**SQL_out**, **SQL_in**) должны соответствовать значениям, определяемым с помощью `@input_data_1_name` и `@output_data_1_name`, включая регистр знаков.

   > [!TIP]
   > В качестве параметра может быть передан только один входной набор данных, и можно возвращать только один набор данных. Однако вы можете вызывать другие наборы данных из кода R, а также возвращать выходные данные других типов в дополнение к набору данных. Вы также можете добавить ключевое слово OUTPUT к любому параметру, чтобы он возвращался с результатами.

1. Также можно формировать значения только с помощью сценария R, без каких-либо входных данных (в аргументе `@input_data_1` задано пустое значение).

   Следующий сценарий выводит текст "hello" и "world".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Результаты**

    ![Результаты запроса с использованием @script в качестве входных данных](./media/r-script-create-quickstart/r-data-generated-output.png)

## <a name="check-r-version"></a>Проверка версии R

Если вы хотите узнать, какая версия R установлена в базе данных, выполните приведенный ниже скрипт.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Функция R `print` возвращает версию в окне **Сообщения**. В приведенном ниже примере выходных данных видно, что в этом случае База данных SQL использует версию R 3.4.4.

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

## <a name="list-r-packages"></a>Получение списка пакетов R

Корпорация Майкрософт предоставляет ряд пакетов R, предварительно установленных с помощью Служб машинного обучения в базе данных.

Чтобы просмотреть список установленных пакетов R, включая сведения о версии, зависимостях, лицензии и пути к библиотеке, выполните следующий сценарий.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

Выходные данные `installed.packages()` в R и возвращаются в виде результирующего набора.

**Результаты**

![Установленные пакеты в R](./media/r-script-create-quickstart/r-installed-packages.png)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы создать модель машинного обучения с помощью языка R в Базе данных SQL, изучите данное краткое руководство.

> [!div class="nextstepaction"]
> [Создание и обучение прогнозной модели на языке R с помощью Служб машинного обучения Azure в Базе данных SQL (предварительная версия)](r-train-score-model-create-quickstart.md)

Дополнительные сведения о Службе машинного обучения с использованием R в Базе данных SQL Azure (предварительная версия) см. в следующих статьях.

- [Azure SQL Database Machine Learning Services with R (preview)](machine-learning-services-overview.md) (Служба машинного обучения с использованием R в Базе данных SQL Azure (предварительная версия))
- [Запись расширенных функций R в Базу данных SQL Azure с помощью Служб машинного обучения (предварительная версия)](machine-learning-services-functions.md)
- [Work with R and SQL data in Azure SQL Database Machine Learning Services (preview)](machine-learning-services-data-issues.md) (Работа с данными R и SQL в Службе машинного обучения в Базе данных SQL Azure)
