---
title: Статья об известных проблемах и ограничениях миграции при оперативной миграции из Oracle в базу данных Azure для PostgreSQL — один сервер | Документация Майкрософт
description: Сведения об известных проблемах и ограничениях миграции при оперативной миграции из Oracle в базу данных Azure для PostgreSQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/03/2019
ms.openlocfilehash: bc37c7cb11b973f89695b5631ec9569ff6b94608
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71801792"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Известные проблемы и ограничения миграции при оперативной миграции из Oracle в базу данных Azure для PostgreSQL — один сервер

Известные проблемы и ограничения, связанные с миграцией из Oracle в базу данных Azure для PostgreSQL-Single Server, описаны в следующих разделах.

## <a name="oracle-versions-supported-as-a-source-database"></a>Версии Oracle, поддерживаемые в качестве базы данных-источника

Azure Database Migration Service поддерживает подключение к:

- Oracle версии 10G, 11c и 12c.
- Oracle Enterprise, Standard, Express и Personal Edition.

Azure Database Migration Service не поддерживает подключение к базам данных контейнеров с несколькими клиентами (Кдбс).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Версии PostgreSQL, поддерживаемые в качестве целевой базы данных

Azure Database Migration Service поддерживает миграцию в базу данных Azure для PostgreSQL-Single Server версии 9,5, 9,6, 10 и 11. Актуальные сведения о поддержке версий в базе данных Azure для PostgreSQL-Single Server см. в статье [Поддерживаемые версии базы данных PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) .

## <a name="datatype-limitations"></a>Ограничения типа данных

Следующие типы **не** будут перенесены:

- BFILE
- ROWID
- ЗНАЧ
- UROWID
- АНИДАТА
- SDO_GEOMETRY
- Вложенные таблицы
- Определяемые пользователем типы данных
- Примечания
- Виртуальные столбцы
- Материализованные представления, основанные на столбце ROWID

Кроме того, пустые столбцы BLOB/CLOB сопоставляются со значением NULL в целевом объекте.

## <a name="lob-limitations"></a>Ограничения больших объектов

- Если включен режим ограниченного размера (LOB), пустые большие объекты в источнике Oracle реплицируются как значения NULL.
- Длинные имена объектов (свыше 30 байт) не поддерживаются.
- Данные в ДЛИННом и ДЛИННом необработанном столбце не могут превышать 64 КБ. Все данные, превышающие 64 КБ, будут обрезаны.
- В Oracle 12 любые изменения в столбцах LOB не поддерживаются (перенесены).
- Обновления столбцов XMLTYPE и LOB не поддерживаются (перенесены).

## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения

- Клиенты должны использовать СИСДБ для подключения к Oracle.
- Изменения данных, внесенные в результате операций секционирования и подраздела (Добавление, удаление, Обмен и УСЕЧЕНИЕ), не будут перенесены и могут вызвать следующие ошибки:
  - Для операций добавления операции обновления и удаления для добавленных данных могут возвращать предупреждение "0 затронутых строк".
  - При операциях удаления и УСЕЧЕНИЯ новые операции вставки могут привести к ошибкам "повторений".
  - Для операций EXCHANGE могут возникать ошибки предупреждения "0 строк" и "повторение".
- Таблицы, имена которых содержат апострофы, не могут быть реплицированы.
