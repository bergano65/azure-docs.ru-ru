---
title: Создание сложных функций R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Узнайте, как написать функцию R для продвинутых статистических вычислений в базе данных Azure S'L с помощью служб машинного обучения (предварительный просмотр).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60702458"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Запись расширенных функций R в Базу данных SQL Azure с помощью Служб машинного обучения (предварительная версия)

В этой статье описывается, как встраивать математические и полезные функции R в процедуру хранения S'L. Расширенные статистические функции, которые сложно реализовать в T-SQL, можно создать в R с помощью всего одной строчки кода.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Если у вас нет подписки Azure, [создайте учетную запись перед началом.](https://azure.microsoft.com/free/)

- Чтобы запустить пример кода в этих упражнениях, сначала необходимо включить в Базе данных SQL Azure Службы машинного обучения (с поддержкой R). Во время использования общедоступной предварительной версии корпорация Майкрософт подключит вас и включит машинное обучение для имеющейся или новой базы данных. Выполните шаги, приведенные в разделе [Sign up for the preview](sql-database-machine-learning-services-overview.md#signup) (Регистрация для получения предварительной версии).

- Убедитесь, что у вас установлена последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Вы можете выполнять сценарии R с помощью других инструментов управления базами данных или выполнения запросов, но в этом кратком руководстве вы будете использовать SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Создание хранимой процедуры для формирования случайных чисел

Для простоты давайте использовать пакет `stats` R, установленный и загруженный по умолчанию, с помощью служб машинного обучения (предварительный просмотр). Пакет содержит сотни функций для общих `rnorm` статистических задач, в том числе функции. Эта функция генерирует определенное количество случайных чисел, используя нормальное распределение, учитывая стандартное отклонение и средства.

Например, следующий код R возвращает 100 чисел со средним значением 50 и стандартным отклонением 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Чтобы вызвать эту строку R от `sp_execute_external_script` T-S'L, запустите и добавьте функцию R в параметр R-скрипта, как это:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Как упростить формирование другого набора случайных чисел?

Это легко в сочетании с S'L. Вы определяете хранимую процедуру, которая получает предоставленные пользователем аргументы и передает их в качестве переменных в скрипт R.

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

Пакет, `utils` установленный по умолчанию, предоставляет различные функции утилиты для исследования текущей среды R. Эти функции могут быть полезны, если вы обнаружите расхождения в том, как ваш R-код работает в S'L и в внешних средах. Например, можно использовать функцию R `memory.limit()`, чтобы получить память для текущей среды R.

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
> Многим пользователям нравится использовать функции синхронизации `system.time` системы в R, такие как и, `proc.time`чтобы захватить время, используемое R-процессами и проанализировать проблемы с производительностью.
