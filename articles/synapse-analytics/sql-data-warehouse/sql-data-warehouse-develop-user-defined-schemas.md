---
title: Использование пользовательских схем
description: Советы по использованию схем, определяемых пользователями T-S'L, для разработки решений в пуле Synapse S'L.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633453"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Схемы, определяемые пользователем, в пуле Synapse S'L
В этой статье основное внимание уделяется предоставлению нескольких советов по использованию схем, определяемых пользователями T-S'L, для разработки решений в пуле Synapse S'L.

## <a name="schemas-for-application-boundaries"></a>Схемы для границ приложений

В традиционных хранилищах данных часто используются отдельные базы данных, чтобы создать границы приложений на основе рабочей нагрузки, домену или безопасности. 

В качестве примера можно привести традиционный склад данных s'L Server, включающего промежуточную базу данных, базу данных для хранилища данных и некоторые базы данных. В этой топологии каждая база данных работает как рабочая нагрузка и граница безопасности в архитектуре.

В отличие от этого, пул S'L запускает всю рабочую нагрузку хранилища данных в одной базе данных. Соединения кросс-базы данных не допускаются. Пул с помощью пула S'L ожидает, что все таблицы, используемые складом, будут храниться в одной базе данных.

> [!NOTE]
> Пул спомощьи не поддерживает запросы перекрестных баз данных любого рода. Следовательно, реализации хранилища данных, использующих этот шаблон, будет необходимо пересмотреть.
> 
> 

## <a name="recommendations"></a>Рекомендации
Ниже приведены рекомендации по консолидации рабочих нагрузок, безопасности, домена и функциональных границ с помощью пользовательских схем:

- Для выполнения всей рабочей нагрузки хранилища данных используйте одну базу данных s'L.
- Консолидируйте существующую среду хранилища данных, чтобы использовать одну базу данных пула S'L.
- Используйте **определяемые пользователем схемы** , чтобы создать границы, ранее реализуемые с помощью баз данных.

Если схемы, определяемые пользователем, не использовались ранее, то у вас есть чистый лист. Используйте старое имя базы данных в качестве основы для схем, определяемых пользователем, в базе данных пула S'L.

Если схемы уже использованы, то у вас есть несколько вариантов:

- Удалите имена устаревших схем и начните заново.
- Сохранить имена устаревшей схемы путем предварительного ожидания имени устаревшей схемы на имя таблицы.
- Сохранить прежние имена схем, реализовав представления таблицы в дополнительной схеме, чтобы воссоздать старую структуру схемы.

> [!NOTE]
> На первый взгляд вариант 3 может показаться наиболее привлекательным. Однако черт прячется в деталях. Виды читаются только в бассейне S'L. Любое изменение данных или таблицы потребуется выполнять в базовой таблице. Кроме того, вариант 3 добавляет в систему уровень представлений. Вы можете обдумать это, если в вашей архитектуре уже используются представления.
> 
> 

### <a name="examples"></a>Примеры:
Реализация пользовательских схем на основе имен баз данных:

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

Храните имена устаревших схем, предварительно доверив их к названию таблицы. Используйте схемы для границы рабочей нагрузки:

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

Храните имена устаревших схем с помощью представлений:

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
> Любое изменение в стратегии схем влечет за собой пересмотр модели безопасности базы данных. Во многих случаях можно упростить модель безопасности, назначив разрешения на уровне схемы. Если требуется больше детальных разрешений, то можно использовать роли базы данных.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные советы по разработке см. в статье [Проектные решения и методики программирования для хранилища данных SQL](sql-data-warehouse-overview-develop.md).

