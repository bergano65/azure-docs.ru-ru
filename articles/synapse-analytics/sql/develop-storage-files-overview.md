---
title: Запрашивание файлов хранилища с помощью SQL по запросу (предварительная версия) в Synapse SQL
description: Узнайте, как запрашивать файлы хранилища с помощью ресурсов SQL по запросу (предварительная версия) в Synapse SQL
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: bfea79fe232fbb6f1b39c03a5cc8e9fe06bee867
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204945"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Запрашивание файлов хранилища с помощью ресурсов SQL по запросу (предварительная версия) в Synapse SQL

SQL по запросу (предварительная версия) позволяет запрашивать данные в озере данных. Предоставляемая контактная зона для запросов T-SQL позволяет запрашивать частично структурированные и неструктурированные данные.

Возможность запрашивания данных поддерживает следующие аспекты T-SQL:

- Полная контактная зона инструкции [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), включая большинство функций, операторов SQL и пр.
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) создает [внешнюю таблицу](develop-tables-external-tables.md), а затем в параллельном режиме экспортирует результаты инструкции SELECT Transact-SQL в службу хранилища Azure.

См. сведения о [поддерживаемых и неподдерживаемых аспектах SQL по запросу](on-demand-workspace-overview.md).

Когда пользователи Azure AD выполняют запросы, по умолчанию для получения доступа к учетным записям хранения используется протокол сквозной проверки подлинности Azure AD. Это означает, что олицетворение пользователей и проверка разрешений выполняется на уровне хранилища. Вы можете [управлять доступом к хранилищу](develop-storage-files-storage-access-control.md) в соответствии с текущими требованиями.

## <a name="extensions"></a>Модули

Чтобы оптимизировать процесс запрашивания данных, размещенных в файлах службы хранилища Azure, SQL по запросу использует функцию [OPENROWSET](develop-openrowset.md) со следующими дополнительными возможностями:

- [Запрашивание нескольких файлов или папок](#query-multiple-files-or-folders)
- [Формат PARQUET](#parquet-file-format)
- [Дополнительные параметры для текстов с разделителями (признак конца поля, признак конца строки, escape-символ)](#additional-options-for-working-with-delimited-text)
- [Считывание выбранного подмножества столбцов](#read-a-chosen-subset-of-columns)
- [Вывод схемы](#schema-inference)
- [Функция filename](#filename-function)
- [Функция filepath](#filepath-function)
- [Работа со сложными типами, а также вложенными и повторяющимися структурами данных](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Запрашивание нескольких файлов или папок

Чтобы выполнить запрос T-SQL набора файлов в папке или набора папок в качестве одной сущности или набора строк, укажите путь к папке или шаблон пути (с подстановочными знаками) к набору файлов или папок.

Применяются следующие правила.

- Шаблоны можно использовать как часть пути к папке или в имени файла.
- В одной части пути к каталогу или имени файла могут использоваться несколько шаблонов.
- Если указано несколько подстановочных знаков, в результирующий набор файлов будут включены все файлы, связанные со всеми соответствующими путями.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

См. [примеры запросов к нескольким файлам и папкам](query-folders-multiple-csv-files.md).

### <a name="parquet-file-format"></a>Формат PARQUET

Для запрашивания исходных данных в формате PARQUET используйте параметр FORMAT = 'PARQUET'.

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

См. [примеры запросов к файлам в формате PARQUET](query-parquet-files.md).

### <a name="additional-options-for-working-with-delimited-text"></a>Дополнительные параметры для текста с разделителями

Следующие дополнительные параметры используются для работы с CSV-файлами (текст с разделителями):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char'. Определяет символ в файле, который используется для экранирования самого себя и всех значений разделителей. Если за escape-символом следует значение, отличающееся от него самого или какого-либо из значений разделителей, при считывании этого значения escape-символ удаляется.
Параметр ESCAPE_CHAR будет применяться независимо от того, включен ли параметр FIELDQUOTE. Он не будет использоваться для экранирования символа кавычек. Символ кавычек нужно экранировать другим символом кавычек. Такой символ может использоваться в значении столбца только в том случае, если значение инкапсулировано с помощью символов кавычек.
- FIELDTERMINATOR ='field_terminator'. Определяет используемый признак конца поля. По умолчанию признаком конца поля считается запятая ( **,** ).
- ROWTERMINATOR ='row_terminator'. Определяет используемый признак конца строки. По умолчанию признаком конца строки считается символ новой строки ( **\r\n**).

### <a name="read-a-chosen-subset-of-columns"></a>Считывание выбранного подмножества столбцов

Чтобы указать столбцы для считывания, включите необязательное предложение WITH в инструкцию OPENROWSET.

- Если используются CSV-файлы данных, для считывания всех столбцов укажите имена и типы данных столбцов. Если нужно считать подмножество столбцов, используйте порядковые номера, чтобы выбрать столбцы из исходных файлов данных по порядковому номеру. Столбцы будут привязаны к порядковым обозначениям.
- Если используются файлы данных PARQUET, укажите имена столбцов, совпадающие с именами столбцов в исходных файлах данных. Столбцы будут привязаны к именам.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

См. примеры в разделе [Возвращение подмножества столбцов](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Вывод схемы

Если вы не указываете предложение WITH в инструкции OPENROWSET, служба попытается автоматически определить (вывести) схему из базовых файлов.

> [!NOTE]
> Сейчас это поддерживается только для формата PARQUET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

Убедитесь, что для оптимальной производительности используются [соответствующие выводимые типы данных](best-practices-sql-on-demand.md#check-inferred-data-types). 

### <a name="filename-function"></a>Функция filename

Эта функция возвращает имя файла, из которого получена строка. 

Для запрашивания определенных файлов используйте инструкции из раздела [Filename](query-specific-files.md#filename).

Тип возвращаемых данных — nvarchar(1024). Для оптимальной производительности всегда приводите результат функции filename к соответствующему типу данных. Если используется символьный тип данных, убедитесь, что используется соответствующая длина.

### <a name="filepath-function"></a>Функция filepath

Эта функция возвращает полный путь или часть пути:

- При вызове без параметра она возвращает полный путь, из которого была получена строка.
- При вызове с параметром она возвращает ту часть пути, которая соответствует подстановочному знаку в позиции, определенной в этом параметре. Например, при значении параметра 1 возвращается часть пути, соответствующая первому подстановочному знаку.

См. сведения в разделе [Filepath](query-specific-files.md#filepath).

Тип возвращаемых данных — nvarchar(1024). Для оптимальной производительности всегда приводите результат функции filepath к соответствующему типу данных. Если используется символьный тип данных, убедитесь, что используется соответствующая длина.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Работа со сложными типами, а также вложенными и повторяющимися структурами данных

Чтобы оптимизировать работу с данными, хранимыми во вложенных или повторяющихся типах данных (например, в файлах [PARQUET](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types)), в решении "SQL по запросу" добавлены перечисленные ниже расширения.

#### <a name="project-nested-or-repeated-data"></a>Вложенные или повторяющиеся данные в проекте

Для проецирования данных выполните инструкцию SELECT для файлов PARQUET, которые содержат столбцы вложенных типов данных. При выводе вложенные значения сериализуются в формат JSON и возвращаются с типом данных SQL varchar(8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

См. сведения о запрашивании вложенных типов данных в разделе [Вложенные или повторяющиеся данные в проекте](query-parquet-nested-types.md#project-nested-or-repeated-data).

#### <a name="access-elements-from-nested-columns"></a>Доступ к элементам из вложенных столбцов

Чтобы получить доступ к вложенным элементам из вложенных столбцов, таким как Struct, используйте точечную нотацию для конкатенации имен полей в строку пути. Передайте полученный путь в параметре column_name предложения WITH функции OPENROWSET.

Ниже приводится пример фрагмента с таким синтаксисом.

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

По умолчанию функция OPENROWSET сопоставляет имя исходного поля и путь к нему с именами столбцов в предложении WITH. С помощью предложения WITH можно получить доступ к элементам на разных уровнях вложенности в пределах одного исходного файла PARQUET.

**Возвращаемые значения**

- Функция возвращает скалярное значение, например int, decimal и varchar, из указанного элемента и по указанному пути для всех типов PARQUET, которые не находятся в группе вложенных типов.
- Если путь указывает на элемент вложенного типа, функция возвращает фрагмент JSON, который начинается с верхнего элемента указанного пути. Этот фрагмент JSON имеет тип varchar(8000).
- Если свойство не удается найти по указанному значению column_name, функция возвращает ошибку.
- Если свойство не удается найти по указанному значению column_path, в зависимости от настроенного [режима PATH](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), функция возвращает ошибку (строгий режим) или значение NULL (нестрогий режим).

См. примеры таких запросов в разделе [Доступ к элементам из вложенных столбцов](query-parquet-nested-types.md#access-elements-from-nested-columns).

#### <a name="access-elements-from-repeated-columns"></a>Доступ к элементам из повторяющихся столбцов

Чтобы получить доступ к элементам из повторяющегося столбца, например Array или Map, используйте функцию [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для каждого проецируемого скалярного элемента, предоставив ей следующие данные:

- Вложенный или повторяющийся столбец в качестве первого параметра.
- [Путь JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), который обозначает нужный элемент свойства, в качестве второго параметра.

Чтобы обратиться к элементам с нескалярным значением из повторяющегося столбца, используйте функцию [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) для каждого проецируемого нескалярного элемента, предоставив ей следующие данные:

- Вложенный или повторяющийся столбец в качестве первого параметра.
- [Путь JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), который обозначает нужный элемент свойства, в качестве второго параметра.

Фрагмент такого синтаксиса приводится ниже.

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

См. [примеры запросов для получения доступа к элементам из повторяющихся столбцов](query-parquet-nested-types.md#access-elements-from-repeated-columns).

## <a name="next-steps"></a>Дальнейшие действия

См. сведения о запрашивании файлов разных типов и создании представлений:

- [Запрашивание одного CSV-файла](query-single-csv-file.md)
- [Запрашивание файлов Parquet](query-parquet-files.md)
- [Запрашивание JSON-файлов](query-json-files.md)
- [Запрашивание вложенных типов Parquet](query-parquet-nested-types.md)
- [Запрашивание папок и нескольких CSV-файлов](query-folders-multiple-csv-files.md)
- [Использование метаданных файла в запросах](query-specific-files.md)
- [Создание и использование представлений](create-use-views.md)
