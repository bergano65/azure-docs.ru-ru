---
title: Добавление пакета R в Службу машинного обучения в Базе данных SQL Azure (предварительная версия)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: В этой статье объясняется, как установить пакет R, который еще не установлен в Службе машинного обучения в Базе данных SQL Azure (предварительная версия).
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
ms.date: 04/29/2019
ms.openlocfilehash: 4e7145570cbc906ea540c9d8f95f6c3cbde1c610
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928629"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Добавление пакета R в Службу машинного обучения в Базе данных SQL Azure (предварительная версия)

В этой статье объясняется, как добавить пакет R в Службу машинного обучения в Базе данных SQL Azure (предварительная версия).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Установите [R](https://www.r-project.org) и [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) на локальном компьютере. Пакет R доступен для операционной системы Windows, macOS и Linux. В этом документе предполагается, что вы используете Windows.

- В этой статье содержится пример запуска скрипта R в Базе данных SQL Azure с использованием [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) или [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Вы можете выполнять скрипты R с помощью других инструментов управления базами данных или выполнения запросов, но в этом примере предполагается использование Azure Data Studio или SSMS.
   
> [!NOTE]
> Вы не можете установить пакет, выполнив скрипт R с помощью **sp_execute_external_script** в Azure Data Studio или SSMS. Вы можете только установить и удалить пакеты с помощью командной строки R и RStudio, как описано в этой статье. После установки пакета можно получить доступ к функциям пакета в скрипте R с помощью **sp_execute_external_script**.

## <a name="list-r-packages"></a>Вывод списка пакетов R

Корпорация Майкрософт предоставляет ряд пакетов R, предварительно установленных с помощью Службы машинного обучения в базе данных SQL Azure.
Чтобы отобразился список установленных пакетов R, выполните следующую команду в Azure Data Studio или SSMS.

1. Откройте Azure Data Studio или SSMS и подключитесь к Базе данных SQL Azure.

1. Выполните следующую команду:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

Результат должен выглядеть следующим образом.

**Результаты**

![Установленные пакеты в R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Добавление пакета с помощью sqlmlutils

Если необходимо использовать пакет, который еще не установлен в Базе данных SQL Azure, его можно установить с помощью [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** — это пакет, с помощью которого пользователи могут взаимодействовать с базами данных SQL (SQL Server и Базой данных SQL Azure) и выполнять код R или Python в SQL из клиента R или Python. Сейчас в базе данных SQL Azure поддерживается только версия R **sqlmlutils**.

В приведенном ниже примере будет установлен пакет **[glue](https://cran.r-project.org/web/packages/glue/)** , который может форматировать и интерполировать строки. На этих шагах устанавливаются **sqlmlutils** и **RODBCext** (необходимое условие для **sqlmlutils**) и добавляется пакет **glue**.

### <a name="install-sqlmlutils"></a>Установка **sqlmlutils**

1. Скачайте последнюю версию ZIP-файла **sqlmlutils** из https://github.com/Microsoft/sqlmlutils/tree/master/R/dist на локальный компьютер. Вам не нужно распаковывать файл.

1. Откройте **командную строку** и выполните следующие команды, чтобы установить **RODBCext** и **sqlmlutils** на локальном компьютере. Замените полный путь к скачанному ZIP-файлу **sqlmlutils** (в примере предполагается, что файл находится в папке "Документы").
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Должен быть получен примерно такой результат:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Если вы получаете сообщение об ошибке, например "R не является внутренней или внешней командой, исполняемой программой или пакетным файлом", скорее всего, это означает, что путь к R.exe не включен в вашу переменную среды **PATH** в Windows. Вы можете либо добавить путь в переменную среды, либо перейти к папке в командной строке (например, `cd C:\Program Files\R\R-3.5.3\bin`), а затем выполнить команду снова.

### <a name="add-the-package"></a>Добавление пакета

1. Откройте RStudio и создайте файл **скрипта R**. 

1. Используйте следующий код R для установки пакета **glue** с помощью **sqlmlutils**. Подставьте собственные сведения о подключении к Базе данных SQL Azure.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **Область** может быть либо **общедоступной**, либо **частной**. Общедоступная область полезна администратору базы данных для установки пакетов, которые могут использовать все пользователи. Частная область делает пакет доступным только для пользователя, который его устанавливает. Если область не указана, областью по умолчанию является **частная**.

### <a name="verify-the-package"></a>Проверка пакета

Убедитесь, что пакет **glue** установлен, выполнив следующий скрипт R в RStudio. Используйте то же **подключение**, определенное на предыдущем шаге.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Результаты**

![Содержимое таблицы RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Использование пакета

После установки пакета можно использовать его в скрипте R через **sp_execute_external_script**.

1. Откройте Azure Data Studio или SSMS и подключитесь к Базе данных SQL Azure.

1. Выполните следующую команду:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
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

    На вкладке **Сообщения** появится следующий результат.

    **Результаты**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Удаление пакета

Если вы хотите удалить пакет, выполните следующий скрипт R в RStudio. Используйте то же **подключение**, определенное ранее.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Другой способ установить пакет R в базе данных SQL Azure — отправить пакет R из потока байтов с помощью инструкции T-SQL **CREATE EXTERNAL LIBRARY**. Ознакомьтесь с разделом [Создание библиотеки из потока байтов](/sql/t-sql/statements/create-external-library-transact-sql#c-create-a-library-from-a-byte-stream) в справочной документации [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Службе машинного обучения с использованием R в Базе данных SQL Azure (предварительная версия) см. в следующих статьях.

- [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md) (Служба машинного обучения с использованием R в Базе данных SQL Azure (предварительная версия))
- [Запись расширенных функций R в Базу данных SQL Azure с помощью Служб машинного обучения (предварительная версия)](sql-database-machine-learning-services-functions.md)
- [Work with R and SQL data in Azure SQL Database Machine Learning Services (preview)](sql-database-machine-learning-services-data-issues.md) (Работа с данными R и SQL в Службе машинного обучения в Базе данных SQL Azure)