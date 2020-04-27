---
title: Как использовать OPENROWSET в службе SQL по запросу (предварительная версия)
description: В этой статье описан синтаксис функции OPENROWSET в службе SQL по запросу (предварительная версия) и использование аргументов.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680494"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Как использовать OPENROWSET в службе SQL по запросу (предварительная версия)

Функция OPENROWSET(BULK...) позволяет получить доступ к файлам в службе хранилища Azure. В ресурсе службы SQL по запросу (предварительная версия) доступ к поставщику больших наборов строк OPENROWSET осуществляется путем вызова функции OPENROWSET и задания параметра BULK.  

Функцию OPENROWSET можно использовать в предложении FROM запроса так, как если бы она была именем таблицы OPENROWSET. Функция также поддерживает массовые операции с помощью встроенного поставщика BULK, позволяющего считывать данные из файла и возвращать их в виде набора строк.

Сейчас пул SQL не поддерживает OPENROWSET.

## <a name="syntax"></a>Синтаксис

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Аргументы

Есть два варианта ввода входных файлов, содержащих целевые данные для запросов. Допустимые значения:

- "CSV" — включает любой текстовый файл с разделителями (разделители строк и столбцов). В качестве разделителя полей можно использовать любой символ, например TSV: FIELDTERMINATOR = знак табуляции.

- "PARQUET" — двоичный файл в формате Parquet. 

**unstructured_data_path**

Аргумент unstructured_data_path, который позволяет установить путь к данным, имеет следующую структуру:  
\<prefix>://\<путь_к_учетной_записи_хранения>/\<путь_к_хранилищу>
 
 
 Ниже приведены соответствующие пути к учетной записи хранения, которые будут связаны с конкретным внешним источником данных. 

| Внешний источник данных       | Prefix | Путь к учетной записи хранения                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| хранилище BLOB-объектов Azure         | HTTPS  | \<учетная_запись_хранения>.blob.core.windows.net             |
| Azure Data Lake Store 1-го поколения | HTTPS  | \<учетная_запись_хранения>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store 2-го поколения | HTTPS  | \<учетная_запись_хранения>.dfs.core.windows.net              |
||||

\<путь_к_хранилищу>

 Позволяет задать путь в хранилище, указывающий на папку или файл, который нужно считать. Если путь указывает на контейнер или папку, все файлы будут считываться только из этого контейнера или папки. Файлы во вложенных папках не включаются. 
 
 Для выбора нескольких файлов или папок можно использовать подстановочные знаки. Допускается использование нескольких подстановочных знаков, если они размещены не подряд.
Ниже приведен пример для считывания всех файлов *.csv*, в начале имени которых указано *population*, из всех папок, в начале имени которых указано */csv/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Если указать в аргументе unstructured_data_path папку, после обращения к службе SQL по запросу будут получены файлы из этой папки. 

> [!NOTE]
> В отличие от Hadoop и Polybase, SQL по запросу не возвращает вложенные папки. Еще одно отличие от Hadoop и Polybase заключается в том, что служба SQL по запросу не возвращает файлы, имя которых начинается с подчеркивания (_) или точки (.).

Если в приведенном ниже примере unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`, после обращения в SQL по запросу будут возвращены строки из mydata.txt и _hidden.txt. Служба не будет возвращать mydata2.txt и mydata3.txt, так как они находятся во вложенной папке.

![Рекурсивные данные для внешних таблиц](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Предложение WITH позволяет указать столбцы, которые нужно считать из файлов.

- Для чтения всех столбцов в файлах данных CSV укажите имена столбцов и их типы данных. Если нужно считать подмножество столбцов, используйте порядковые номера, чтобы выбрать столбцы из исходных файлов данных по порядковому номеру. Столбцы будут привязаны к порядковым обозначениям. 

> [!IMPORTANT]
> Предложение WITH является обязательным для CSV-файлов.
- Для файлов данных Parquet укажите имена столбцов, совпадающих с именами столбцов в исходных файлах данных. Столбцы будут привязаны к именам. Если предложение WITH не указано, возвращаются все столбцы из файлов Parquet.

column_name (имя_столбца) = имя выходного столбца. Если значение указано, это имя переопределяет имя столбца в исходном файле.

column_type (тип_столбца) = тип данных в выходном столбце. Выполняется неявное преобразование типов данных.

column_ordinal (порядковый_номер_столбца) = порядковый номер столбца в исходных файлах. Этот аргумент не учитывается для файлов Parquet, так как привязка выполняется по имени. В следующем примере из CSV-файла будет возвращен только второй столбец:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator' (признак_конца_поля)

Указывает признак конца поля, который нужно использовать. Признак конца поля по умолчанию — запятая (" **,** ").

ROWTERMINATOR ='row_terminator' (признак_конца_строки)

Указывает признак конца строки, который нужно использовать. По умолчанию признаком конца строки является символ новой строки, например \r\n.

ESCAPE_CHAR = 'char'

Задает символ в файле, используемый для экранирования собственно этого символа и всех значений разделителей в файле. Если за escape-символом следует значение, отличное от него самого или какого-либо из значений разделителей, при считывании этого значения escape-символ пропускается. 

Параметр ESCAPE_CHAR будет применяться независимо от того, включен ли параметр FIELDQUOTE. Он не будет использоваться для экранирования символа кавычек. Символ кавычек экранируется с помощью двойных кавычек, как и в CSV-файлах Excel.

FIRSTROW = 'first_row' (первая_строка) 

Указывает номер первой строки для загрузки. Значение по умолчанию — 1. Значение по умолчанию — первая строка указанного файла данных. Номера строк определяются с помощью подсчета признаков конца строки. Значения аргумента FIRSTROW начинаются с 1.

FIELDQUOTE = 'field_quote' (символ_кавычек) 

Определяет символ, который будет использоваться в качестве символа кавычки в CSV-файле. Если значение не указано, будут использоваться текущий символ кавычек ("). 

## <a name="examples"></a>Примеры

В следующем примере из файлов population*.csv возвращаются только два столбца с порядковыми номерами 1 и 4. В файлах нет строки заголовка, поэтому чтение начинается с первой строки.

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



В следующем примере возвращаются все столбцы первой строки из набора данных переписи в формате Parquet без указания имен столбцов и типов данных. 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Дальнейшие действия

Чтобы получить дополнительные примеры, см. [краткие руководства](query-data-storage.md) или сохраните результаты своего запроса в службе хранилища Azure с помощью [CETAS](develop-tables-cetas.md).
