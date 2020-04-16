---
title: Схемы, определяемые пользователем, в рамках Synapse S'L
description: В приведенных ниже разделах вы найдете различные советы по использованию схем, определяемых пользователями T-S'L, для разработки решений с возможностью Synapse S'L в Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428710"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Схемы, определяемые пользователем, в рамках Synapse S'L

В приведенных ниже разделах вы найдете различные советы по использованию схем, определяемых пользователями T-S'L, для разработки решений в рамках Synapse S'L.

## <a name="schemas-for-application-boundaries"></a>Схемы для границ приложений

Традиционная архитектура аналитики часто использует отдельные базы данных для создания границ приложений на основе рабочей нагрузки, домена или безопасности. Например, традиционная инфраструктура аналитики серверов S'L Server может включать в себя промежуточную базу данных, базу данных аналитики и базы данных data mart. В этой топологии каждая база данных работает как рабочая нагрузка и граница безопасности в архитектуре.

Вместо этого, Synapse S'L запускает всю аналитическую рабочую нагрузку в одной базе данных. Соединения кросс-базы данных не допускаются. Синапсе S'L ожидает, что все таблицы, используемые складом, будут храниться в одной базе данных.

> [!NOTE]
> Пулы S'L не поддерживают запросы перекрестных баз данных любого рода. Следовательно, реализация аналитики, использующие эту модель, должна быть пересмотрена. По требованию (предварительный просмотр) S'L поддерживает перекрестные запросы базы данных.

## <a name="user-defined-schema-recommendations"></a>Рекомендации по схемам, определяемым пользователем

Включены рекомендации по консолидации рабочих нагрузок, безопасности, домена и функциональных границ с помощью пользовательских схем:

- Используйте одну базу данных для выполнения всей рабочей нагрузки аналитики.
- Консолидируйте существующую среду аналитики, чтобы использовать одну базу данных.
- Используйте **определяемые пользователем схемы** , чтобы создать границы, ранее реализуемые с помощью баз данных.

Если схемы, определяемые пользователем, не использовались ранее, то у вас есть чистый лист. Используйте старое имя базы данных в качестве основы для схем, определяемых пользователем, в базе данных Synapse S'L.

Если схемы уже использованы, то у вас есть несколько вариантов:

- Удалить прежние имена схем и создать новые.
- Храните имена устаревшей схемы, предварительно дождавшись названия устаревшей схемы, к названию таблицы
- Сохранить имена устаревшей схемы, реализовав представления по таблице в дополнительной схеме, которая воссоздает старую структуру схемы.

> [!NOTE]
> При первом осмотре вариант 3 может показаться наиболее привлекательным выбором. Представления читаются только в Synapse S'L. Любое изменение данных или таблицы потребуется выполнять в базовой таблице. Кроме того, вариант 3 добавляет в систему уровень представлений. Вы можете дать этому некоторые дополнительные мысли, если вы уже используете представления в архитектуре.
> 
> 

### <a name="examples"></a>Примеры

Реализация пользовательских схем на основе имен баз данных.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Храните имена устаревшей схемы, предварительно доверив их, к названию таблицы. Используйте схемы для создания границы рабочих нагрузок.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Сохранить имена устаревшей схемы с помощью представлений.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Любое изменение стратегии схемы требует пересмотра модели безопасности базы данных. Во многих случаях можно упростить модель безопасности, назначив разрешения на уровне схемы.

Если требуется больше детальных разрешений, можно использовать роли базы данных. Для получения дополнительной информации [Manage database roles and users](../../analysis-services/analysis-services-database-users.md) о роли в базе данных см.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные советы по разработке можно узнать на [примере разработки Synapse S'L.](develop-overview.md)
