---
title: Использование определяемых пользователем схем
description: Советы по использованию определяемых пользователем схем T-SQL для разработки решений в пуле синапсе SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fc5e035215e7cabd02861c6ee2498cadd1ef0534
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213369"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Определяемые пользователем схемы в пуле синапсе SQL
Эта статья посвящена предоставлению нескольких советов по использованию определяемых пользователем схем T-SQL для разработки решений в пуле синапсе SQL.

## <a name="schemas-for-application-boundaries"></a>Схемы для границ приложений

В традиционных хранилищах данных часто используются отдельные базы данных, чтобы создать границы приложений на основе рабочей нагрузки, домену или безопасности. 

Например, в традиционном SQL Server хранилище данных может включать промежуточную базу данных, базу данных хранилища данных и некоторые базы данных киоска данных. В этой топологии каждая база данных действует как Рабочая нагрузка и граница безопасности в архитектуре.

В отличие от этого, пул SQL выполняет всю рабочую нагрузку хранилища данных в пределах одной базы данных. Межбазовые подключения к базам данных не разрешены. Пул SQL ждет, что все таблицы, используемые хранилищем, хранятся в одной базе данных.

> [!NOTE]
> Пул SQL не поддерживает запросы перекрестной базы данных любого типа. Следовательно, реализации хранилища данных, использующих этот шаблон, будет необходимо пересмотреть.
> 
> 

## <a name="recommendations"></a>Рекомендации
Ниже приведены рекомендации по консолидации рабочих нагрузок, безопасности, домена и функциональных границ с помощью определяемых пользователем схем.

- Используйте одну базу данных пула SQL для выполнения всей рабочей нагрузки хранилища данных.
- Объедините существующую среду хранилища данных для использования одной базы данных пула SQL.
- Используйте **определяемые пользователем схемы** , чтобы создать границы, ранее реализуемые с помощью баз данных.

Если определяемые пользователем схемы не использовались ранее, то у вас есть чистый планшет. Используйте старое имя базы данных в качестве базиса для определяемых пользователем схем в базе данных пула SQL.

Если схемы уже используются, есть несколько вариантов:

- Удалите устаревшие имена схем и начните заново.
- Сохраняйте устаревшие имена схем, предварительно добавив имя прежней схемы в имя таблицы.
- Сохранить прежние имена схем, реализовав представления таблицы в дополнительной схеме, чтобы воссоздать старую структуру схемы.

> [!NOTE]
> На первый взгляд вариант 3 может показаться наиболее привлекательным. Однако черт прячется в деталях. Представления доступны только для чтения в пуле SQL. Любое изменение данных или таблицы потребуется выполнять в базовой таблице. Кроме того, вариант 3 добавляет в систему уровень представлений. Вы можете обдумать это, если в вашей архитектуре уже используются представления.
> 
> 

### <a name="examples"></a>Примеры:
Реализуйте определяемые пользователем схемы на основе имен баз данных:

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Используйте устаревшие имена схем, добавив их в имя таблицы заранее. Используйте схемы для границы рабочей нагрузки:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Используйте представления для сохранения устаревших имен схем:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Любое изменение в стратегии схем влечет за собой пересмотр модели безопасности базы данных. Во многих случаях вы можете упростить модель безопасности, назначив разрешения на уровне схемы. Если требуются более детализированные разрешения, можно использовать роли базы данных.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).

