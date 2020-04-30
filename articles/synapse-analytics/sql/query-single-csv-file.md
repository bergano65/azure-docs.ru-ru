---
title: Запрос CSV-файлов с помощью SQL по запросу (Предварительная версия)
description: В этой статье вы узнаете, как выполнять запросы к отдельным CSV-файлам с различными форматами с помощью SQL по запросу (Предварительная версия).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431596"
---
# <a name="query-csv-files"></a>Запрос CSV-файлов

В этой статье вы узнаете, как выполнить запрос к одному CSV-файлу с помощью SQL по запросу (Предварительная версия) в Azure синапсе Analytics. CSV-файлы могут иметь разные форматы: 

- С строкой заголовка и без нее
- Значения, разделенные запятыми и символами табуляции
- Завершение строк в стиле Windows и UNIX
- Значения, не заключенные в кавычки и кавычки, и символы экранирования

Все приведенные выше варианты будут рассмотрены ниже.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем читать оставшуюся часть этой статьи, ознакомьтесь со следующими статьями:

- [Изначальная настройка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Новая строка в стиле Windows

В следующем запросе показано, как считать CSV-файл без строки заголовка, с новой строкой в стиле Windows и со столбцами, разделенными запятыми.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла без заголовка, новая строка в стиле Windows](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
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

## <a name="unix-style-new-line"></a>Новая строка в стиле UNIX

В следующем запросе показано, как считать файл без строки заголовка с новой строкой в стиле UNIX и столбцами с разделителями-запятыми. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла без строки заголовка и новой строки в стиле UNIX.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
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

В следующем запросе показано, как прочитать файл со строкой заголовка, с новой строкой в стиле UNIX и со столбцами, разделенными запятыми. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла со строкой заголовка и новой строкой в стиле UNIX.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
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

## <a name="custom-quote-character"></a>Символ пользовательской кавычки

В следующем запросе показано, как считать файл со строкой заголовка с новой строкой в стиле UNIX, столбцами с разделителями-запятыми и значениями в кавычках. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла со строкой заголовка и новой строкой в стиле UNIX и заключенными в кавычки значениями.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
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
> Этот запрос возвращает те же результаты, если пропущен параметр ФИЕЛДКУОТЕ, поскольку значение по умолчанию для ФИЕЛДКУОТЕ является двойной кавычкой.

## <a name="escaping-characters"></a>Экранирование символов

В следующем запросе показано, как считать файл со строкой заголовка с новой строкой в стиле UNIX, столбцами, разделенными запятыми, и escape-символом, используемым в качестве разделителя полей (запятая) в значениях. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла со строкой заголовка и с новой строкой в стиле UNIX и символом Escape, используемым для разделителя полей.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
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
    country_name = 'Slov,enia';
```

> [!NOTE]
> Этот запрос завершится ошибкой, если ESCAPECHAR не указан, так как запятая в "слов, ениа" будет рассматриваться как разделитель полей, а не как часть названия страны. "Слов, ениа" будет рассматриваться как два столбца. Таким образом, в конкретной строке будет по одному столбцу больше, чем у других строк, и по одному столбцу больше, чем определено в предложении WITH.

## <a name="tab-delimited-files"></a>Файлы с разделителями-знаками табуляции

В следующем запросе показано, как считать файл со строкой заголовка, с новой строкой в стиле UNIX и столбцами, разделенными символами табуляции. Обратите внимание на другое расположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк CSV-файла со строкой заголовка и новой строкой и разделителем табуляции в стиле UNIX.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
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

До сих пор вы указали схему CSV-файла с помощью и перечислите все столбцы. В запросе можно указать только те столбцы, которые действительно необходимы, используя порядковый номер для каждого столбца. Вы также не пропускаете столбцы без интереса.

Следующий запрос возвращает количество различных названий стран в файле, указывая только необходимые столбцы:

> [!NOTE]
> Взгляните на предложение WITH в приведенном ниже запросе и обратите внимание на то, что в конце строки, где определен столбец *[country_name]* , есть "2" (без кавычек). Это означает, что столбец *[country_name]* является вторым столбцом в файле. Запрос будет игнорировать все столбцы файла, за исключением второго.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Дальнейшие шаги

В следующих статьях будет показано, как:

- [Запрос файлов Parquet](query-parquet-files.md)
- [Запросы к папкам и нескольким файлам](query-folders-multiple-csv-files.md)
