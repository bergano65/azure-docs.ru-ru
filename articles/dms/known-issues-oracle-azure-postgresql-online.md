---
title: "Известные проблемы: Переход из Oracle в базу данных Azure для PostgreS'L"
titleSuffix: Azure Database Migration Service
description: Узнайте об известных проблемах и ограничениях миграции с помощью онлайн-миграций с Oracle в базу данных Azure для сервера PostgreS-L- Single с помощью службы миграции базы данных Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235244"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Известные проблемы/ограничения миграции с миграцией в Интернет е-на-Ез до Azure DB для сервера PostgreS-L-Single

Известные проблемы и ограничения, связанные с миграцией в Интернет с Oracle в базу данных Azure для сервера PostgreS-L-Single, описаны в следующих разделах.

## <a name="oracle-versions-supported-as-a-source-database"></a>Версии Oracle, поддерживаемые в качестве исходной базы данных

Миграционная служба базы данных Azure поддерживает подключение к:

- Oracle версия 10g, 11g, и 12c.
- Oracle Enterprise, Standard, Express и Personal Edition.

Миграционная служба лазурных данных не поддерживает подключение к многотентным контейнерным базам данных (CDB).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Версии PostgreS'L, поддерживаемые в качестве целевой базы данных

Миграционная служба базы данных Azure поддерживает миграцию в базу данных Azure для версии серверов PostgreS-L-Single 9.5, 9.6, 10 и 11. Ознакомиться со статьей [«Поддерживаемые версии баз данных PostgreS'L»](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) можно получить в базе данных Azure Database для сервера PostgreS-L.L.

## <a name="datatype-limitations"></a>Ограничения типа данных

Следующие типы данных **не** будут мигрировать:

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

Кроме того, пустые столбцы BLOB/CLOB отображаются на НУЛНайле на цели.

## <a name="lob-limitations"></a>Ограничения больших объектов

- При включении lob-режима ограниченного размера пустые LOB в источнике Oracle реплицируются как значения NULL.
- Длинные имена объектов (более 30 байтов) не поддерживаются.
- Данные в колонках LONG и LONG RAW не могут превышать 64 k. Любые данные за 64k будут усечены.
- Только в Oracle 12 любые изменения в lob-столбцах не поддерживаются (мигрируют).
- UPDATEs к столбику XMLTYPE и LOB не поддерживаются (мигрировали).

## <a name="known-issues-and-limitations"></a>Известные проблемы и ограничения

- Клиенты должны использовать SYSDBA для подключения к Oracle.
- Изменения данных, возникающие в результате операций раздела/подраздела (ADD, DROP, EXCHANGE и TRUNCATE), не будут переноситься и могут привести к следующим ошибкам:
  - Для операций ADD обновления и удаления на добавленных данных могут вернуть предупреждение "0 строк пострадавших".
  - Для операций DROP и TRUNCATE новые вставки могут привести к ошибкам «дубликатов».
  - Для операций EXCHANGE могут возникать как предупреждения "0 строк" и ошибки "дубликаты".
- Таблицы, имена которых содержат апострофы, не могут быть воспроизведены.
