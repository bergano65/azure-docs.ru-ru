---
title: Файлы запроса CSV с использованием S'L по требованию (предварительный просмотр)
description: В этой статье вы узнаете, как заставить зазапрос одного файла CSV с различными форматами файлов с помощью S'L по требованию (предварительный просмотр).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431596"
---
# <a name="query-csv-files"></a>Файлы запроса CSV

В этой статье вы узнаете, как задать запрос одного файла CSV с помощью S'L по требованию (предварительный просмотр) в Azure Synapse Analytics. Файлы CSV могут иметь разные форматы: 

- С и без строки заголовка
- Значения запятых и разграничения вкладок
- Окончания линейки Windows и Unix
- Не цитируемые и цитируемые значения и убегающие символы

Все вышеперечисленные варианты будут рассмотрены ниже.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем читать остальную часть этой статьи, просмотрите следующие статьи:

- [Первая установка](query-data-storage.md#first-time-setup)
- [Предварительные требования](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Новая линия стиля Windows

В следующем запросе показано, как читать файл CSV без строки заголовка, с новой строкой в стиле Windows и столбцов с запятой.

Предварительный просмотр файла:

![Первые 10 строк файла CSV без заголовка, новая линия стиля Windows.](./media/query-single-csv-file/population.png)

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

## <a name="unix-style-new-line"></a>Новая линия в стиле Unix

В следующем запросе показано, как читать файл без строки заголовка, с новой строкой в стиле Unix и столбцов в стиле запятой. Обратите внимание на различное местоположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк файла CSV без строки заголовка и с новой строкой Unix-Style.](./media/query-single-csv-file/population-unix.png)

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

В следующем запросе показано, как читать файл с заголовком строки, с Unix стиле новой строки, и запятой разграничения столбцов. Обратите внимание на различное местоположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк файла CSV с заголовком строки и с Unix-Стиль новой линии.](./media/query-single-csv-file/population-unix-hdr.png)

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

## <a name="custom-quote-character"></a>Пользовательский характер цитаты

В следующем запросе показано, как читать файл с строкой заголовка, с новой строкой в стиле Unix, столбцы с запятой и цитируемыми значениями. Обратите внимание на различное местоположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк файла CSV с заголовком строки и с Unix-Стиль новой линии и цитируемых значений.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

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
> Этот запрос вернет те же результаты, если вы опустили параметр FIELDQUOTE, так как значение по умолчанию для ФИЛДЗУТИВе является двойной цитатой.

## <a name="escaping-characters"></a>Экранирование символов

В следующем запросе показано, как читать файл с строкой заголовка, с новой строкой в стиле Unix, столбцы мизаню и символ побега, используемый для разграничения поля (комма) в пределах значений. Обратите внимание на различное местоположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк файла CSV с заголовком строки и с Unix-Стиль новой линии и побега символ, используемый для поля делимитетер.](./media/query-single-csv-file/population-unix-hdr-escape.png)

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
> Этот запрос не удастся, если ESCAPECHAR не указано, поскольку запятая в "Слове,Enia" будет рассматриваться как поле делимитатор вместо части названия страны. "Слов,Зенит" будет рассматриваться как две колонны. Таким образом, конкретная строка будет иметь один столб больше, чем другие строки, и один столб больше, чем вы определили в оговорке С.

## <a name="tab-delimited-files"></a>Файлы с разграничением вкладок

В следующем запросе показано, как читать файл с строкой заголовка, с новой строкой в стиле Unix и столбцов в формате вкладки. Обратите внимание на различное местоположение файла по сравнению с другими примерами.

Предварительный просмотр файла:

![Первые 10 строк файла CSV с заголовком строки и с Unix-Стиль новой линии и вкладки делимитетер.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

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

До сих пор вы указали схему файла CSV, используя WITH и перечисляя все столбцы. Вы можете указать в запросе только столбцы, которые вам действительно нужны, используя для каждого необходимого столбца ординаторский номер. Вы также опустите столбцы, не представляющие интереса.

Следующий запрос возвращает число различных названий стран в файле, указывая только необходимые столбцы:

> [!NOTE]
> Взгляните на положение С Смс в запросе ниже и обратите внимание, что есть "2" (без котировок) в конце строки, где вы определяете столбец *"country_name".* Это означает, что столбец *«country_name»* является второй колонкой в файле. Запрос будет игнорировать все столбцы в файле, кроме второго.

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

## <a name="next-steps"></a>Дальнейшие действия

Следующие статьи покажут вам, как:

- [Запрос файлов паркета](query-parquet-files.md)
- [Папки запросов и несколько файлов](query-folders-multiple-csv-files.md)
