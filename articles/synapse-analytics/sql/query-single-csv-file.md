---
title: Запрос CSV-файлов с помощью SQL по запросу (предварительная версия)
description: В этой статье вы узнаете, как запрашивать отдельные CSV-файлы с различными форматами с помощью SQL по запросу (предварительная версия).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: f264a62428f919fe23797171926ddf63c585c42b
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234127"
---
# <a name="query-csv-files"></a>Запрос CSV-файлов

В этой статье вы узнаете, как выполнить запрос одного CSV-файлы с помощью SQL по запросу (предварительная версия) в Azure Synapse Analytics. CSV-файлы могут иметь разные форматы: 

- Со строкой заголовка и без нее
- Со значениями, разделенными запятыми и символами табуляции
- Завершение строк в стиле Windows и UNIX
- Значения, не заключенные в кавычки и заключенные в кавычки, и символы экранирования

Все приведенные выше варианты будут рассмотрены ниже.

## <a name="prerequisites"></a>Предварительные требования

Для начала **создайте базу данных**, в которой будут созданы таблицы. Затем инициализируйте объекты, выполнив [сценарий установки](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) для этой базы данных. Этот сценарий установки создает источники данных, учетные данные области базы данных и форматы внешних файлов, которые используются в этих примерах.

## <a name="windows-style-new-line"></a>Новая строка в стиле Windows

Следующий запрос показывает, как считать CSV-файл без строки заголовка, с новой строкой в стиле Windows и столбцами с разделителями-запятыми.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла без заголовка, новая строка в стиле Windows](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Новая строка в стиле Unix

Следующий запрос показывает, как считать CSV-файл без строки заголовка, с новой строкой в стиле Unix и столбцами с разделителями-запятыми. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла без заголовка, новая строка в стиле Unix.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Строка заголовка

Следующий запрос показывает, как считать CSV-файл со строкой заголовка, с новой строкой в стиле Unix и столбцами с разделителями-запятыми. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла с заголовком и новой строкой в стиле Unix.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Пользовательский символ кавычек

Следующий запрос показывает, как считать CSV-файл со строкой заголовка, с новой строкой в стиле Unix, столбцами с разделителями-запятым и значениями в кавычках. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла с заголовком, новой строкой в стиле Unix и значениями в кавычках.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Этот запрос возвращает те же результаты, если пропущен параметр FIELDQUOTE, поскольку значение по умолчанию для FIELDQUOTE является двойной кавычкой.

## <a name="escaping-characters"></a>Экранирование символов

Следующий запрос показывает, как считать CSV-файл со строкой заголовка, с новой строкой в стиле Unix, столбцами с разделителями-запятым, а также экранированием символа для разделителя полей (запятой) внутри значений. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла с заголовком и новой строкой в стиле Unix, а также экранированием символа разделителя.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Этот запрос завершится ошибкой, если ESCAPECHAR не указан, так как запятая в "Slov,enia" будет рассматриваться как разделитель полей, а не как часть названия страны или региона. "Slov,enia" будет обработано как два столбца. Таким образом, в конкретной строке будет на один столбец больше, чем в других строках, и на один столбец больше, чем определено в условии WITH.

## <a name="tab-delimited-files"></a>Файлы с разделителями-табуляциями

Следующий запрос показывает, как считать CSV-файл со строкой заголовка, с новой строкой в стиле Unix и столбцами с разделителями-табуляциями. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла с заголовком, новой строкой в стиле Unix и разделителем — знаком табуляции.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>Возвращение подмножества столбцов

До сих пор вы создавали схему CSV-файла с помощью WITH и перечисления всех столбцов. В запросе можно указать только те столбцы, которые действительно необходимы, используя порядковый номер для каждого столбца. Вы также пропускаете неважные столбцы.

Следующий запрос возвращает количество уникальных имен стран или регионов в файле, указывая только необходимые столбцы:

> [!NOTE]
> Взгляните на условие WITH в приведенном ниже запросе и обратите внимание на "2" (без кавычек) в конце строки, где определяется столбец *[country_name]* . Это означает, что столбец *[country_name]* является вторым столбцом в файле. Запрос будет игнорировать все столбцы файла, за исключением второго.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Дальнейшие действия

В следующих статьях будет показано:

- [Запрашивание файлов Parquet](query-parquet-files.md)
- [Запрашивание папок и нескольких файлов](query-folders-multiple-csv-files.md)
