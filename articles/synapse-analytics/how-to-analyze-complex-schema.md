---
title: Анализ схемы с помощью массивов и вложенных структур
description: Анализ массивов и вложенных структур с помощью Apache Spark и SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908029"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Анализ сложных типов данных в синапсе

Эта статья относится к файлам и контейнерам Parquet в **Azure синапсе Link для Azure Cosmos DB**. В нем объясняется, как пользователи могут использовать Spark или SQL для чтения или преобразования данных со сложной схемой, такой как массивы или вложенные структуры. Приведенный ниже пример выполняется с помощью одного документа, но может легко масштабироваться в документах миллиардов с помощью Spark или SQL. В приведенном ниже коде используется PySpark (Python).

## <a name="use-case"></a>Вариант использования

При использовании современных типов данных сложные типы данных часто являются общими для управления и представляют трудности для инженеров по обработке данных. Анализ вложенной схемы и массивов представляет некоторые трудности.
* Сложные для написания запросов SQL
* Сложно переименование или приведение типа данных вложенных столбцов
* Проблемы с производительностью при глубоком вложении объектов

Специалистам по обработке данных необходимо понять, как эффективно обрабатывать эти типы данных и сделать их доступными для всех.

В приведенном ниже примере синапсе Spark будет использоваться для чтения и преобразования объектов через кадры данных в плоскую структуру. Синапсе SQL Server используется для запроса непосредственно таких объектов и возврата этих результатов в виде обычной таблицы.

## <a name="what-are-arrays-and-nested-structures"></a>Что такое массивы и вложенные структуры?

Следующий объект поступает из App Insights. В этом объекте есть вложенные структуры, но также массивы, содержащие вложенные структуры.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Пример схемы массивов и вложенных структур
При печати схемы кадра данных этого объекта (имя **DF**) с помощью команды **DF. принтсчема**отображается следующее представление:

* желтый цвет представляет вложенную структуру
* зеленый цвет представляет массив с двумя элементами

[![Источник схемы](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts и _etag были добавлены в систему по мере того, как документ был получен в Azure Cosmos DB хранилище транзакций.

В рамке данных выше учитывается 5 столбцов и 1 строка. После преобразования проверенный кадр данных будет содержать 13 столбцов и 2 строки в табличном формате.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Сведение вложенных структур и развертывание массивов с помощью Apache Spark

Благодаря синапсе Spark преобразование вложенных структур в столбцы и элементы массива в несколько строк не представляет сложностей. Приведенные ниже действия могут использоваться всеми для собственной реализации.

[![Шаги преобразования Spark](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**Шаг 1**. определение функции для преобразования вложенной схемы в плоскую. Эту функцию можно использовать без изменений. Создайте ячейку в записной книжке Pyspark с этой функцией:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**Шаг 2**. Использование функции для выравнивания вложенной схемы **DF** кадра данных в новый кадр данных **df_flat**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Функция отображения должна возвращать 10 столбцов и 1 строку. Массив и его вложенные элементы все еще существуют.

**Шаг 3**. Теперь можно преобразовать массив **context_custom_dimensions** в кадр данных **df_flat** в новый **df_flat_explode**данных. В приведенном ниже коде мы также определим, какой именно столбец мы выбрали:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Функция отображения должна возвращать следующий результат: 10 столбцов и 2 строки. Следующим шагом является сведение вложенных схем с помощью функции, определенной на шаге 1.

**Шаг 4**. Использование функции для выравнивания вложенной схемы кадра данных **df_flat_explode** в новый кадр данных **df_flat_explode_flat**:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Функция отображения должна отображать 13 столбцов и 2 строки:

Функция Принтсчема фрейма данных df_flat_explode_flat возвращает следующий результат:

[![Последняя схема](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Чтение массивов и вложенных структур непосредственно с помощью SQL Server

Запросы, создание представлений и таблиц на таких объектах возможно благодаря SQL Server.

Прежде всего, в зависимости от способа хранения данных пользователи должны использовать следующую таксономию. Все ВЕРХНие РЕГИСТРы относятся к Вашему варианту использования:

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| Н'ендпоинт = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; база данных = DATABASENAME; коллекция = COLLECTIONNAME; регион = регионтокуери, секрет = ' йоурсекрет ' |"CosmosDB" (ссылка синапсе)|



**SQL Server** не поддерживает ссылку на службу Link синапсе для Azure для Azure Cosmos DB и для транзитной передачи AAD. Эта возможность в настоящее время находится в предварительной версии ссылки синапсе.

Замените ниже:
* В строке подключения к источнику данных, к которому вы подключаетесь, используется строка, УКАЗАНная выше.
* "Ваш тип выше" по формату, используемому для подключения к источнику

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Существует два различных типа операций:
* В следующей строке кода будет определен столбец с именем Контекстдатаевенттиме, который ссылается на вложенный элемент: Context. Data. eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

В этой строке будет определен столбец с именем Контекстдатаевенттиме, который ссылается на элемент вложения: Context>Data>eventTime

* **CROSS APPLY** используется для создания новых строк для каждого элемента в массиве, а затем с определяет каждый вложенный объект, аналогичный первому элементу маркированного списка: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Если массив содержит 5 элементов с 4 вложенными структурами, то SQL Server не будет возвращать 5 строк и 4 столбца.

SQL Server не может выполнять запросы на месте, сопоставлять массив в двух строках и отображать все вложенные структуры в столбцах.

## <a name="next-steps"></a>Дальнейшие шаги

* [Узнайте, как запросить ссылку Azure синапсе для Azure Cosmos DB с помощью Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Запрос Parquet вложенных типов](./sql/query-parquet-nested-types.md) 