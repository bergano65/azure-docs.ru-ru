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
ms.openlocfilehash: fdf3dc56575a45ad0c9e716054184ba2691133ba
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831708"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Анализ сложных типов данных в Azure синапсе Analytics

Эта статья относится к файлам и контейнерам Parquet в [ссылке синапсе для Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). В нем объясняется, как пользователи могут использовать Spark или SQL для чтения или преобразования данных со сложными схемами, такими как массивы или вложенные структуры. Следующий пример выполнен с одним документом, но может легко масштабироваться на миллиарды документов с помощью Spark или SQL. Код, прилагаемый к этой статье, использует PySpark (Python).

## <a name="use-case"></a>Вариант использования

Сложные типы данных все чаще встречаются и представляют трудности для инженеров по анализу данных, так как при анализе вложенной схемы и массивов обычно используются длительные и сложные SQL-запросы. Кроме того, может быть сложно переименовать или привести тип данных «вложенные столбцы». Кроме того, при работе с глубокими вложенными объектами возникают проблемы с производительностью.

Специалистам по обработке данных необходимо понять, как эффективно обрабатывать сложные типы данных и сделать их доступными для всех.

В следующем примере синапсе Spark используется для чтения и преобразования объектов в плоскую структуру через кадры данных. Синапсе SQL Server используется для прямого запроса таких объектов и возврата этих результатов в виде обычной таблицы.

## <a name="what-are-arrays-and-nested-structures"></a>Что такое массивы и вложенные структуры?

Следующий объект поступает из [app Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). В этом объекте есть вложенные структуры и массивы, содержащие вложенные структуры.

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
При печати схемы фрейма данных объекта ( **DF**) с помощью команды `df.printschema` отображается следующее представление:

* Желтый цвет представляет вложенную структуру
* Зеленый цвет представляет массив с двумя элементами

[![Источник схемы](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**, **_TS**и **_etag** были добавлены в систему по мере того, как документ был получен в Azure Cosmos DB хранилище транзакций.

В рамке данных выше учитывается 5 столбцов и 1 строка. После преобразования проверенный кадр данных будет содержать 13 столбцов и 2 строки в табличном формате.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Сведение вложенных структур и развертывание массивов с помощью Apache Spark

С помощью синапсе Spark можно легко преобразовать вложенные структуры в столбцы и элементы массива в несколько строк. Для реализации можно использовать следующие шаги.

[![Шаги преобразования Spark](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**Шаг 1**. определение функции для плоской вложенной схемы. Эту функцию можно использовать без изменений. Создайте ячейку в [записной книжке PySpark](quickstart-apache-spark-notebook.md) , используя следующую функцию:

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

**Шаг 2**. Использование функции для выравнивания вложенной схемы кадра данных (**DF**) в новый кадр данных `df_flat` :

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Функция отображения должна возвращать 10 столбцов и 1 строку. Массив и его вложенные элементы все еще существуют.

**Шаг 3**. Преобразуйте массив `context_custom_dimensions` в кадре данных `df_flat` в новый `df_flat_explode` . В следующем коде мы также определим, какой столбец выбрать:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Функция отображения должна возвращать 10 столбцов и 2 строки. Следующим шагом является сведение вложенных схем с помощью функции, определенной на шаге 1.

**Шаг 4**. Использование функции для преобразования вложенной схемы фрейма данных `df_flat_explode` в новый кадр данных `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Функция отображения должна отображать 13 столбцов и 2 строки.

Функция `printSchema` кадра данных `df_flat_explode_flat` возвращает следующий результат:

[![Последняя схема](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Чтение массивов и вложенных структур непосредственно с помощью SQL Server

Запросы и создание представлений и таблиц на таких объектах можно реализовать без SQL Server.

Во первых, в зависимости от способа хранения данных пользователи должны использовать следующую таксономию. Все, что показано в верхнем регистре, зависит от варианта использования:

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |"Parquet" (ADLSg2)|
| Н'ендпоинт = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; база данных = DATABASENAME; коллекция = COLLECTIONNAME; регион = регионтокуери ', секрет = ' йоурсекрет ' |"CosmosDB" (ссылка синапсе)|


> [!NOTE]
> SQL Server не поддерживает ссылку на синапсе для Azure Cosmos и службы AAD PassThrough. Эта возможность в настоящее время находится в предварительной версии ссылки синапсе.

Замените каждое поле следующим образом:
* "Ваша операция выше" = строка подключения к источнику данных, к которому вы подключаетесь
* ' Ваш тип выше ' = формат, используемый для подключения к источнику

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

Существует два разных типа операций:

Первый тип операции указывается в следующей строке кода, которая определяет столбец `contextdataeventTime` с именем, который ссылается на вложенный элемент: Context. Data. eventTime 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

В этой строке будет определен столбец с именем Контекстдатаевенттиме, который ссылается на элемент вложения: Context>Data>eventTime

Второй тип операции использует `cross apply` для создания новых строк для каждого элемента в массиве, а затем с определяет каждый вложенный объект, аналогичный первому пункту маркированного списка: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Если массив содержит 5 элементов с 4 вложенными структурами, то SQL Server не будет возвращать 5 строк и 4 столбца. SQL Server не может выполнять запросы на месте, сопоставлять массив в двух строках и отображать все вложенные структуры в столбцах.

## <a name="next-steps"></a>Дальнейшие шаги

* [Узнайте, как запросить ссылку синапсе для Azure Cosmos DB с помощью Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Запрос Parquet вложенных типов](./sql/query-parquet-nested-types.md) 
