---
title: Заметки о выпуске базы данных SQL Azure | Документация Майкрософт
description: Узнайте о новых возможностях и улучшениях в службе базы данных SQL Azure и документации по базе данных SQL Azure.
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ed41ccea0754f3eeffdd0248bac567859db1492c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001509"
---
# <a name="sql-database-release-notes"></a>Заметки о выпуске базы данных SQL

В этой статье перечислены функции базы данных SQL, которые в настоящее время доступны в общедоступной предварительной версии. Сведения об обновлениях и улучшениях базы данных SQL см. в статье [обновления службы базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database). Обновления и улучшения других служб Azure см. в разделе [обновления служб](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Возможности общедоступной предварительной версии

### <a name="single-databasetabsingle-database"></a>[Отдельная база данных](#tab/single-database)

| Компонент | Подробнее |
| ---| --- |
| Ускоренное восстановление базы данных с отдельными базами данных и эластичными пулами | Дополнительные сведения см. в разделе [Ускорение восстановления базы данных](sql-database-accelerated-database-recovery.md).|
|Приблизительный счетчик DISTINCT|Дополнительные сведения см. в разделе [приблизительный подсчет DISTINCT](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Пакетный режим в rowstore (на уровне совместимости 150)|Дополнительные сведения см. [в разделе пакетный режим в rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Обнаружение и классификация данных  |Дополнительные сведения см. в разделе [база данных SQL Azure и обнаружение данных хранилища данных sql & Классификация](sql-database-data-discovery-and-classification.md).|
| Задания обработки эластичных баз данных | Дополнительные сведения см. в статье [Создание и настройка эластичных заданий и управление ими](elastic-jobs-overview.md). |
| Эластичные запросы | Дополнительные сведения см. в разделе [Общие сведения о эластичных запросах](sql-database-elastic-query-overview.md). |
| Эластичные транзакции | [Распределенные транзакции между облачными базами данных](sql-database-elastic-transactions-overview.md). |
|Обратная связь выделения памяти (режим строки) (на уровне совместимости 150)|Дополнительные сведения см. в разделе [обратная связь предоставления памяти (режим строки)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Редактор запросов в портал Azure |Дополнительные сведения см. в разделе [Использование редактора запросов SQL портал Azure для подключения и запроса данных](sql-database-connect-query-portal.md).|
| Службы R/машинное обучение с отдельными базами данных и эластичными пулами |Дополнительные сведения см. [в разделе службы машинного обучения в базе данных SQL Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Уровень бессерверных вычислений | Дополнительные сведения см. в разделе [несерверная база данных SQL (Предварительная версия)](sql-database-serverless.md).|
|Аналитика SQL|Дополнительные сведения см. в разделе [аналитика SQL Azure](../azure-monitor/insights/azure-sql.md).|
|Отложенная компиляция табличной переменной (на уровне совместимости 150)|Дополнительные сведения см. в разделе [табличная переменная Отложенная компиляция](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Управляемый экземпляр](#tab/managed-instance)

| Компонент | Подробнее |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Пулы экземпляров</a> | Удобный и экономичный способ переноса небольших экземпляров SQL в облако. |
| <a href="https://aka.ms/managed-instance-tde-byok">Прозрачное шифрование данных (TDE) с создание собственных ключей (BYOK)</a> |Дополнительные сведения см. [в разделе прозрачное шифрование данных Azure SQL с управляемыми клиентом ключами в Azure Key Vault: Поддержка](transparent-data-encryption-byok-azure-sql.md)создание собственных ключей.|
| <a href="https://aka.ms/managed-instance-failover-groups">Геораспределенные группы отработки отказа</a> | Сохраните копию экземпляра в другом регионе и убедитесь, что данные будут доступны даже в региональной ситуации. |
| <a href="https://aka.ms/managed-instance-aadlogins">Участники сервера Azure AD уровня экземпляра (имена для входа)</a> | Создайте имена входа на уровне сервера, используя инструкцию <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login из внешнего поставщика</a> . |
| [Репликация транзакций](sql-database-managed-instance-transactional-replication.md) | Репликация изменений из таблиц в другие базы данных, размещенные на управляемых экземплярах, отдельных базах данных или SQL Server экземплярах, или при изменении некоторых строк в других управляемых экземплярах или экземплярах SQL Server. Дополнительные сведения см. [в статье Настройка репликации в базе данных управляемого экземпляра базы данных SQL Azure](replication-with-sql-database-managed-instance.md). |
| Обнаружение угроз |Дополнительные сведения см. [в статье Настройка обнаружения угроз в управляемом экземпляре базы данных SQL Azure](sql-database-managed-instance-threat-detection.md).|
| Повторное создание удаленных баз данных с помощью управляемых экземпляров |Дополнительные сведения см. [в статье повторное создание удаленных баз данных в Azure SQL управляемый экземпляр](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="fixed-known-issues"></a>Исправлены известные проблемы

- **2019 августа** — автономные базы данных полностью поддерживаются в управляемом экземпляре.

## <a name="updates"></a>Обновления

Список обновлений и улучшений базы данных SQL см. в статье [обновления службы базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database).

Обновления и улучшения для всех служб Azure см. в разделе [обновления служб](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Участие в содержимом

Чтобы внести изменения в документацию по базе данных SQL Azure, ознакомьтесь с [руководством для участников](https://docs.microsoft.com/contribute/)документации.
