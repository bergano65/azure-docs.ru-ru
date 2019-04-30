---
title: Создание сложных функций R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Вы можете научиться писать функции R для расширенной статистических вычислений в базе данных SQL Azure с помощью служб машинного обучения (Предварительная версия).
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
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702458"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Запись расширенных функций R в базе данных SQL Azure с помощью служб машинного обучения (Предварительная версия)

В этой статье описывается, как внедрить R математических и служебных функций в SQL хранимой процедуры. Статистических функций, которые сложно реализовать в T-SQL может выполняться в R с помощью одной строки кода.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Технические условия

- Если у вас еще нет подписки Azure, [создайте учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

- Чтобы запустить пример кода в этих упражнениях, сначала необходимо включить в Базе данных SQL Azure Службы машинного обучения (с поддержкой R). Во время использования общедоступной предварительной версии корпорация Майкрософт подключит вас и включит машинное обучение для имеющейся или новой базы данных. Выполните шаги, приведенные в разделе [Sign up for the preview](sql-database-machine-learning-services-overview.md#signup) (Регистрация для получения предварительной версии).

- Убедитесь, что у вас установлена последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Вы можете выполнять сценарии R с помощью других инструментов управления базами данных или выполнения запросов, но в этом кратком руководстве вы будете использовать SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Создание хранимой процедуры для формирования случайных чисел

Для простоты используем R `stats` пакет, который установленный и загруженный по умолчанию, с базой данных SQL Azure с помощью служб машинного обучения (Предварительная версия). Он содержит сотню функций для общих статистических задач, среди них `rnorm` функции. Эта функция создает указанное количество случайных чисел, используя нормального распределения, с учетом означает, что и стандартном отклонении.

Например следующий код R возвращает 100 чисел со средним значением 50 и стандартным отклонением 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Чтобы вызвать строку кода R из T-SQL, выполните `sp_execute_external_script` и добавьте функцию R в параметр скрипта R, следующим образом:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Что делать, если вы хотели бы упростить формирование другого набора случайных чисел?

Это просто, если вместе с SQL. Вы определите хранимую процедуру, которая получает аргументы от пользователя, а затем передайте эти аргументы в скрипт R как переменные.

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

- В первой строке определяется каждый из входных параметров SQL, которые требуются при выполнении хранимой процедуры.

- Строка, начинающаяся с `@params` определяет все переменные, используемые в коде R и соответствующие типы данных SQL.

- Строки, которые следуют за имена параметров SQL сопоставляются с соответствующими именами переменных r.

Теперь, когда функция R заключил в хранимой процедуре, можно вызвать функцию и передать различные значения, следующим образом:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Использование служебных функций R для устранения неполадок

`utils` Пакетов, установленных по умолчанию, предоставляет широкий набор служебных функций для изучения текущей среды r. Эти функции могут быть полезны в том случае, если вы нашли несоответствия в того, который выполняет код R в SQL и внешних средах. Например, можно использовать R `memory.limit()` функцию для получения памяти для текущей среды r.

Так как `utils` пакет установлен, но не загружается по умолчанию, необходимо использовать `library()` функции, чтобы сначала загрузить.

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
> Многие пользователи хотели использовать функции системного времени в R, например `system.time` и `proc.time`, чтобы фиксировать время, затрачиваемое процессами R и анализировать проблемы производительности.
