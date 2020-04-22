---
title: Запрос таблиц Spark с помощью SQL по запросу (предварительная версия)
description: Общие сведения о запросе таблиц Spark с помощью SQL по запросу (предварительная версия)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420078"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>Запрос таблиц Spark с помощью Azure Synapse Analytics и SQL по запросу (предварительная версия)

SQL по запросу (предварительная версия) может автоматически синхронизировать метаданные из пулов Spark в рабочую область Synapse (предварительная версия). Для каждой базы данных, имеющейся в пулах Spark (предварительная версия), будет создана база данных SQL по запросу. Для каждой таблицы Spark, основанной на Parquet или CSV, в базе данных SQL по запросу создается внешняя таблица. Таким образом, вы можете завершить работу пулов Spark и по-прежнему запрашивать таблицы Spark из SQL по запросу.

Если таблица секционирована в Spark, файлы в хранилище упорядочиваются по папкам. SQL по запросу будет использовать для запроса метаданные секции и только соответствующие папки и файлы.

Синхронизация метаданных автоматически настраивается для каждого пула Spark, подготовленного в рабочей области Azure Synapse. Вы можете сразу же начать выполнение запросов к таблицам Spark.

Каждая таблица Spark представлена внешней таблицей в схеме dbo, которая соответствует базе данных SQL по запросу. Для запросов к таблицам Spark выполните запрос, нацеленный на внешнюю таблицу [spark_table]. Перед выполнением примера убедитесь, что у вас есть правильный [доступ к учетной записи хранения](develop-storage-files-storage-access-control.md), в которой находятся файлы.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Сопоставление типов данных Spark с типами данных SQL

| Тип данных Spark | Тип данных SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | INT                         |
| LongType        | BIGINT                      |
| FloatType       | real                        |
| DoubleType      | FLOAT                       |
| DecimalType     | Decimal                     |
| TimestampType   | datetime2                   |
| DateType        | Дата                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (в JSON)** |
| MapType         | varchar(max)* (в JSON)** |
| StructType      | varchar(max)* (в JSON)** |

\* Используемые параметры сортировки — Latin1_General_100_BIN2_UTF8.

** ArrayType, MapType и StructType представлены в виде JSON.



## <a name="next-steps"></a>Дальнейшие действия

Перейдите к статье [Управление доступом к хранилищу](develop-storage-files-storage-access-control.md), чтобы узнать больше о контроле доступа к хранилищу.
