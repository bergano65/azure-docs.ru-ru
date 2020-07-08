---
title: Создание сложных функций R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Узнайте, как написать функцию R для расширенных статистических вычислений в базе данных SQL Azure с помощью Службы машинного обучения (Предварительная версия).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84035645"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Запись расширенных функций R в Базу данных SQL Azure с помощью Служб машинного обучения (предварительная версия)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

В этой статье описывается внедрение математических и служебных функций R в хранимую процедуру SQL. Расширенные статистические функции, которые сложно реализовать в T-SQL, можно создать в R с помощью всего одной строчки кода.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Если у вас еще нет подписки Azure, [создайте учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

- Чтобы запустить пример кода в этих упражнениях, необходимо сначала включить [базу данных SQL Azure с службы машинного обучения (с R)](machine-learning-services-overview.md) .

- Убедитесь, что у вас установлена последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Вы можете выполнять сценарии R с помощью других инструментов управления базами данных или выполнения запросов, но в этом кратком руководстве вы будете использовать SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Создание хранимой процедуры для формирования случайных чисел

Для простоты мы используем `stats` пакет R, установленный и загруженный по умолчанию в базе данных SQL Azure с помощью службы машинного обучения (Предварительная версия). Пакет содержит сотни функций для общих статистических задач, между которыми они `rnorm` работают. Эта функция создает указанное число случайных чисел с использованием нормального распределения, учитывая стандартное отклонение и средства.

Например, следующий код R возвращает 100 чисел со средним значением 50 и стандартным отклонением 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Чтобы вызвать эту строку R из T-SQL, запустите `sp_execute_external_script` и добавьте функцию r в параметр скрипта r следующим образом:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Как упростить формирование другого набора случайных чисел?

Это легко при объединении с SQL. Вы определяете хранимую процедуру, которая получает предоставленные пользователем аргументы и передает их в качестве переменных в скрипт R.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- В первой строке определяется каждый из входных параметров SQL, необходимых при выполнении хранимой процедуры.

- Строка, начинающаяся с `@params`, определяет все переменные, используемые в коде R, и соответствующие типы данных SQL.

- Следующие строки сопоставляют имена параметров SQL с соответствующими именами переменных R.

Теперь, когда функция R упакована в хранимой процедуре, вы можете легко вызвать функцию и передать различные значения, как показано ниже.

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Использование служебных функций R для устранения неполадок

`utils`Пакет, установленный по умолчанию, предоставляет разнообразные служебные функции для изучения текущей среды R. Эти функции могут быть полезны при поиске расхождений в способе, которым код R работает в SQL и во внешних средах. Например, можно использовать функцию R `memory.limit()`, чтобы получить память для текущей среды R.

По умолчанию пакет `utils` устанавливается, но не загружается, поэтому сначала необходимо использовать функцию `library()`, чтобы его загрузить.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Многие пользователи, например, используют системные функции времени в R, такие как `system.time` и `proc.time` , для записи времени, используемого процессами r, и анализа проблем производительности.
