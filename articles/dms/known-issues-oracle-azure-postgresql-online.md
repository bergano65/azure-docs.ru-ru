---
title: 'Известные проблемы: Миграция из Oracle в Базу данных Azure для PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Сведения об известных проблемах и ограничениях для миграции из Oracle в Базу данных Azure для PostgreSQL при использовании одного сервера и службы Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 05/20/2020
ms.openlocfilehash: 2cf8ff446fe3441fc039ef3c2afef6308224666f
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701217"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Известные проблемы и ограничения при сетевых миграциях из Oracle в Базу данных Azure для PostgreSQL (один сервер)

В следующих разделах описываются известные проблемы и ограничения, связанные с сетевыми миграциями из Oracle в Базу данных Azure для PostgreSQL на одном сервере.

## <a name="oracle-versions-supported-as-a-source-database"></a>Версии Oracle, поддерживаемые в качестве исходной базы данных

Azure Database Migration Service поддерживает подключение к следующим системам.

- Oracle версий 10g, 11g или 12c.
- Oracle Enterprise, Standard, Express или Personal Edition.

Azure Database Migration Service не поддерживает подключение к базам данных с контейнерами для нескольких арендаторов.

## <a name="postgresql-versions-supported-as-a-target-database"></a>Версии PostgreSQL, поддерживаемые в качестве целевой базы данных

Azure Database Migration Service поддерживает миграцию в Базу данных Azure для PostgreSQL для одного сервера версий 9.5, 9.6, 10 и 11. Актуальные сведения о поддерживаемых версиях Базы данных Azure для PostgreSQL для одного сервера см. в [этой статье](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

## <a name="datatype-limitations"></a>Ограничения типа данных

Следующие типы данных **не переносятся**.

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Вложенные таблицы;
- Определяемые пользователем типы данных
- Примечания
- Виртуальные столбцы
- Материализованные представления на основе столбца ROWID

Кроме того, пустые столбцы BLOB/CLOB сопоставляются в целевом объекте со значением NULL.

## <a name="lob-limitations"></a>Ограничения больших объектов

- Если включен режим больших объектов ограниченного размера, все пустые большие объекты из источника Oracle реплицируются как значения NULL.
- Длинные имена объектов (свыше 30 байт) не поддерживаются.
- Данные в столбцах типа LONG и LONG RAW не могут быть длиннее 64 КБ. Все данные, превышающие ограничение в 64 КБ, усекаются.
- Только для Oracle версии 12: любые изменения в столбцах больших объектов не поддерживаются (не переносятся).
- Операции UPDATE для столбцов типа XMLTYPE и LOB не поддерживаются (не переносятся).

## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения

- Пользователь должен иметь права администратора базы данных на сервере Oracle.
- Изменения данных, внесенные в результате операций секционирования (ADD, DROP, EXCHANGE и TRUNCATE), не переносятся и могут вызвать следующие ошибки.
  - Для операций ADD обновления и удаления в добавленных данных могут возвращать предупреждение "0 затронутых строк".
  - Для операций DROP и TRUNCATE новые операции вставки могут привести к ошибкам "дубликаты".
  - Для операций EXCHANGE могут возникать ошибки с предупреждениями "0 затронутых строк" и "дубликаты".
- Таблицы, имена которых содержат апострофы, не могут быть реплицированы.
