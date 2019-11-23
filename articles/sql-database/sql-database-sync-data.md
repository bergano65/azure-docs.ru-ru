---
title: Синхронизация данных
description: В этом обзоре представлена синхронизация данных SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 1ee2efbb8aebfc2f1a94c89edef6166898946d8a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422523"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Синхронизация данных в нескольких облачных и локальных базах данных с помощью синхронизации данных SQL

Синхронизация данных SQL — это служба, созданная на основе Базы данных SQL Azure, которая позволяет в двунаправленном режиме синхронизировать выбранные данные в нескольких базах данных SQL и экземплярах SQL Server.

> [!IMPORTANT]
> Azure SQL Data Sync does not support Azure SQL Database Managed Instance at this time.

## <a name="when-to-use-data-sync"></a>Когда следует использовать синхронизацию данных

Data Sync is useful in cases where data needs to be kept updated across several Azure SQL databases or SQL Server databases. Ниже приведены основных варианты использования синхронизации данных.

- **Гибридная синхронизация данных.** Вы можете обеспечить синхронизацию данных между локальными базами данных и базами данных SQL Azure, чтобы включить гибридные приложения. Эта возможность может привлечь клиентов, которые рассматривают переход в облако и собираются поместить некоторые из своих приложений в Azure.
- **Распределенные приложения**. Во многих случаях полезно разделить разные рабочие нагрузки на разные базы данных. Например, если у вас есть большая рабочая база данных, но необходимо также выполнять рабочую нагрузку для составления отчетов и анализа этих данных, удобно иметь вторую базу данных для этих целей. Такой подход сводит к минимуму влияние на производительность производственной рабочей нагрузки. Чтобы синхронизировать эти две базы данных, можно использовать службу синхронизации данных.
- **Globally Distributed Applications:** Many businesses span several regions and even several countries/regions. Чтобы свести к минимуму задержки в сети, рекомендуется хранить свои данные в регионе, ближайшем к вам. Используя синхронизацию данных, можно легко обеспечить синхронизацию баз данных в регионах по всему миру.

Data Sync isn't the preferred solution for the following scenarios:

| Сценарий | Рекомендуемые решения |
|----------|----------------------------|
| Аварийное восстановление | [Геоизбыточные резервные копии Azure](sql-database-automated-backups.md) |
| масштаб чтения; | [Используйте реплики, доступные только для чтения, чтобы распределять рабочие нагрузки запросов, доступных только для чтения (предварительная версия)](sql-database-read-scale-out.md) |
| ETL (OLTP в OLAP); | [Фабрика данных Azure](https://azure.microsoft.com/services/data-factory/) или [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| перенос из локальной базы данных SQL Server в базу данных SQL Azure. | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Общие сведения о службе "Синхронизация данных SQL"

В основе синхронизации данных лежит понятие группы синхронизации. Группа синхронизации — это группа баз данных, которые требуется синхронизировать.

Служба синхронизации данных использует звездообразную топологию для синхронизации данных. Одну из баз данных в группе синхронизации необходимо определить как центральную базу данных. Остальные базы данных являются рядовыми базами данных. Синхронизация происходит только между центральной базой данных и отдельными членами.

- **Центральной базой данных** должна быть база данных SQL Azure.
- **Рядовыми базами данных** могут быть базы данных SQL, локальные базы данных SQL Server или экземпляры SQL Server на виртуальных машинах Azure.
- The **Sync Database** contains the metadata and log for Data Sync. The Sync Database has to be an Azure SQL Database located in the same region as the Hub Database. База данных синхронизации создается клиентом и принадлежит ему.

> [!NOTE]
> Если в качестве рядовой вы используете локальную базу данных, необходимо [установить и настроить локальный агент синхронизации](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Синхронизация данных между базами данных](media/sql-database-sync-data/sync-data-overview.png)

Группа синхронизации имеет следующие свойства.

- **Схема синхронизации**: описывает, какие данные синхронизируются.
- **Направление синхронизации**: синхронизация может быть двунаправленной или выполняться в одном направлении. То есть синхронизация может выполняться *из центральной базы данных в рядовую*, *из рядовой в центральную базу данных* или в двух направлениях.
- **Интервал синхронизации** определяет, как часто выполняется синхронизация.
- **Политика устранения конфликтов** — это политика уровня группы, которая может задавать режим *Выигрывает концентратор* или *Member wins* (Выигрывает член).

## <a name="how-does-data-sync-work"></a>Как работает синхронизация данных?

- **Отслеживание изменения данных**. Служба синхронизации данных отслеживают изменения при помощи триггеров вставки, обновления и удаления. Изменения записываются во вспомогательную таблицу в пользовательской базе данных. Note that BULK INSERT doesn't fire triggers by default. If FIRE_TRIGGERS isn't specified, no insert triggers execute. Добавьте параметр FIRE_TRIGGERS, чтобы служба синхронизации данных могла отслеживать эти вставки. 
- **Синхронизация данных**. Служба синхронизации данных разработана на основе звездообразной модели. Центральная база данных синхронизируется с каждым членом по отдельности. Изменения в центральной базе данных передаются в член, после чего изменения из члена передаются в центральную базу данных.
- **Устранение конфликтов**. Служба синхронизации данных предоставляет два параметра для устранения конфликтов, *Выигрывает концентратор* и *Member wins* (Выигрывает член).
  - Если выбран параметр *Выигрывает концентратор*, то изменения в центральной базе данных всегда записываются поверх изменений в члене.
  - Если выбран параметр *Member wins* (Выигрывает член), то изменения в члене всегда записываются поверх изменений в центральной базе данных. Если имеется несколько членов, то конечное значение зависит от того, какой член синхронизируется первым.

## <a name="compare-data-sync-with-transactional-replication"></a>Сравнение синхронизации данных с репликацией транзакций

| | Синхронизация данных | Репликация транзакций |
|---|---|---|
| Преимущества | — Поддержка режима "активный — активный"<br/>— Двусторонняя передача данных между локальной базой данных и службой "База данных SQL Azure" | — Низкая задержка<br/>— Согласованность транзакций<br/>— Повторное использование существующей топологии после миграции |
| Недостатки | — Задержка 5 мин или дольше<br/>— Отсутствует согласованность транзакций<br/>— Большее влияние на производительность | — Не поддерживается публикация из отдельной базы данных SQL Azure или базы данных в пуле.<br/>— Дорогое обслуживание |

## <a name="get-started-with-sql-data-sync"></a>Начало работы с синхронизацией данных SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Настройка синхронизации данных на портале Azure

- [Настройка синхронизации данных SQL Azure](sql-database-get-started-sql-data-sync.md).
- Агент синхронизации данных: [Агент синхронизации данных для синхронизации данных SQL Azure](sql-database-data-sync-agent.md).

### <a name="set-up-data-sync-with-powershell"></a>Настройка синхронизации данных с помощью PowerShell

- [Использование PowerShell для синхронизации данных между несколькими базами данных SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Использование PowerShell для синхронизации данных между базой данных SQL Azure и локальной базой данных SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Рекомендации по синхронизации данных

- [Best practices for SQL Data Sync (Preview)](sql-database-best-practices-data-sync.md) (Рекомендации по синхронизации данных SQL (предварительная версия))

### <a name="did-something-go-wrong"></a>Что-то пошло не так?

- [Устранение неполадок с синхронизацией данных SQL Azure (предварительная версия)](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Согласованность и производительность

### <a name="eventual-consistency"></a>Итоговая согласованность

Since Data Sync is trigger-based, transactional consistency isn't guaranteed. Microsoft guarantees that all changes are made eventually and that Data Sync doesn't cause data loss.

### <a name="performance-impact"></a>Влияние на производительность

Служба синхронизация данных использует триггеры вставки, обновления и удаления для отслеживания изменений. Она создает вспомогательные таблицы в пользовательской базе данных для отслеживания изменений. Действия по отслеживанию изменений оказывают влияние на рабочую нагрузку базы данных. Оцените текущий уровень служб и повысьте его, если потребуется.

Подготовка и отзыв во время создания, обновление или удаления группы синхронизации могут повлиять на производительность базы данных.

## <a name="sync-req-lim"></a> Требования и ограничения

### <a name="general-requirements"></a>Общие требования

- Каждая таблица должна иметь первичный ключ. Не изменяйте значение первичного ключа ни в какой строке. Если это необходимо сделать, удалите строку и создайте ее повторно с новым значением первичного ключа.

> [!IMPORTANT]
> Changing the value of an existing primary key will result in the following faulty behavior:
> - Data between hub and member can be lost even though sync does not report any issue.
> - Sync can fail because the tracking table has a non-existing row from source due to the primary key change.

- Необходимо включить изоляцию моментального снимка. Дополнительные сведения см. в статье [Изоляция моментального снимка в SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Общие ограничения

- A table can't have an identity column that isn't the primary key.
- A primary key can't have the following data types: sql_variant, binary, varbinary, image, xml.
- При использовании типов данных time, datetime, datetime2, datetimeoffset для первичного ключа нужно учитывать, что поддерживается точность только до секунды.
- The names of objects (databases, tables, and columns) can't contain the printable characters period (.), left square bracket ([), or right square bracket (]).
- Azure Active Directory authentication isn't supported.
- Tables with same name but different schema (for example, dbo.customers and sales.customers) aren't supported.
- Columns with User Defined Data Types aren't supported

#### <a name="unsupported-data-types"></a>Неподдерживаемые типы данных

- FileStream;
- UDT SQL или CLR;
- XMLSchemaCollection (поддерживается XML);
- Cursor, RowVersion, Timestamp, Hierarchyid.

#### <a name="unsupported-column-types"></a>Неподдерживаемые типы столбцов

При синхронизации данных столбцы только для чтения и созданные системой столбцы не синхронизируются. Пример.

- Вычисляемые столбцы.
- Созданные системой столбцы для темпоральных таблиц.

#### <a name="limitations-on-service-and-database-dimensions"></a>Ограничения характеристик служб и баз данных

| **Характеристики**                                                  | **Ограничение**              | **Возможное решение**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Максимальное количество групп синхронизации, которым может принадлежать любая база данных       | 5                      |                             |
| Максимальное количество конечных точек в отдельной группе синхронизации              | 30                     |                             |
| Максимальное количество локальных конечных точек в отдельной группе синхронизации | 5                      | Создайте несколько групп синхронизации. |
| Имена баз данных, таблиц, схем и столбцов                       | Имя может содержать до 50 знаков. |                             |
| Таблицы в группе синхронизации                                          | 500                    | Создайте несколько групп синхронизации. |
| Столбцы в таблице в группе синхронизации                              | 1000                   |                             |
| Размер строки данных в таблице                                        | 24 МБ                  |                             |
| Минимальный интервал синхронизации                                           | 5 мин              |                             |

> [!NOTE]
> В единственной группе синхронизации может находится до 30 конечных точек. Если имеется несколько групп синхронизации, общее количество конечных точек во всех группах синхронизации не может превышать 30. Если база данных принадлежит к нескольким группам синхронизации, она считается как несколько конечных точек, а не как одна.

## <a name="faq-about-sql-data-sync"></a>Вопросы и ответы о синхронизации данных SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Сколько стоит использование службы синхронизации данных SQL?

There's no charge for the SQL Data Sync service itself. However, you still collect data transfer charges for data movement in and out of your SQL Database instance. Дополнительные сведения см. на странице [цен на базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Какие регионы поддерживают синхронизацию данных?

Синхронизация данных SQL доступна во всех регионах:

### <a name="is-a-sql-database-account-required"></a>Требуется ли учетная запись Базы данных SQL?

Да. Для размещения центральной базы данных необходимо иметь учетную запись базы данных SQL.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Можно ли использовать функцию синхронизации данных только для синхронизации между локальными базами данных SQL Server?

Не напрямую. Вы можете опосредованно выполнять синхронизацию между локальными базами данных SQL Server. Это можно сделать, создав центральную базу данных в Azure, а затем добавив локальные базы данных в группу синхронизации.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Можно ли использовать службу синхронизации данных для синхронизации между Базами данных SQL, которые принадлежат к разным подпискам?

Да. Вы можете синхронизировать данные между базами данных SQL, которые принадлежат к группам ресурсов, связанным с разными подписками.

- Если подписки принадлежат к одному клиенту и у вас есть разрешения на все подписки, то на портале Azure можно настроить группу синхронизации.
- В противном случае необходимо использовать PowerShell, чтобы добавить участников синхронизации, которые принадлежат к другой подписке.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Can I use Data Sync to sync between SQL Databases that belong to different clouds (like Azure Public Cloud and Azure China 21Vianet)

Да. Вы можете использовать службу синхронизации данных для синхронизации между Базами данных SQL, которые принадлежат к разным облакам. Для этого с помощью PowerShell необходимо добавить участников синхронизации, которые принадлежат к разным подпискам.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Можно ли использовать синхронизацию данных для заполнения данных из рабочей базы данных в пустую базу данных с последующей синхронизацией?

Да. Вручную создайте схему в новой базе данных из оригинала с помощью скрипта. После создания схемы добавьте таблицы в группу синхронизации, чтобы скопировать данные и обеспечить их синхронизацию.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Следует ли использовать Синхронизацию данных SQL для резервного копирования и восстановления баз данных?

It isn't recommended to use SQL Data Sync to create a backup of your data. You can't back up and restore to a specific point in time because SQL Data Sync synchronizations are not versioned. Furthermore, SQL Data Sync does not back up other SQL objects, such as stored procedures, and doesn't do the equivalent of a restore operation quickly.

Один из рекомендуемых способов резервного копирования описан в статье [Копирование базы данных SQL Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Можно ли с помощью службы "Синхронизация данных" синхронизировать зашифрованные таблицы и столбцы?

- Если в базе данных используется Always Encrypted, можно синхронизировать только те таблицы и столбцы, которые *не* зашифрованы. Синхронизировать зашифрованные столбцы невозможно, так как служба синхронизации данных не может расшифровать данные.
- Если к столбцу применяется шифрование на уровне столбцов (CLE), то его можно синхронизировать, если размер строки не превышает 24 МБ (это максимальный размер). Служба синхронизации данных обрабатывает столбец, зашифрованный с помощью ключа, как обычные двоичные данные. Чтобы расшифровать данные других участников синхронизации, необходимо иметь тот же сертификат, что и они.

### <a name="is-collation-supported-in-sql-data-sync"></a>Поддерживаются ли в Синхронизации данных SQL параметры сортировки?

Да. Синхронизация данных SQL поддерживает параметры сортировки в следующих сценариях.

- If the selected sync schema tables aren't already in your hub or member databases, then when you deploy the sync group, the service automatically creates the corresponding tables and columns with the collation settings selected in the empty destination databases.
- Если синхронизируемые таблицы уже существуют как в центральной базе данных, так и в рядовых базах данных, синхронизация данных SQL требует, чтобы столбцы первичного ключа имели одинаковые параметры сортировки в центральной базе данных и рядовых базах данных, для успешного развертывания группы синхронизации. Для столбцов, отличных от столбцов первичного ключа, на параметры сортировки нет ограничений.

### <a name="is-federation-supported-in-sql-data-sync"></a>Поддерживается ли федерация в Синхронизации данных SQL?

Корневая база данных федерации может использоваться в службе синхронизации данных SQL без каких-либо ограничений. You can't add the Federated Database endpoint to the current version of SQL Data Sync.

## <a name="next-steps"></a>Дальнейшие действия

### <a name="update-the-schema-of-a-synced-database"></a>Обновление схемы синхронизированной базы данных

Вам нужно обновить схему базы данных в группе синхронизации? Schema changes aren't automatically replicated. Некоторые решения описаны в следующих статьях:

- [Автоматическая репликация изменений схемы при синхронизации данных SQL Azure](sql-database-update-sync-schema.md)
- [Использование PowerShell для обновления схемы синхронизации в существующей группе синхронизации](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Мониторинг и устранение неисправностей

Is SQL Data Sync doing as expected? Сведения об отслеживании действий и устранении неполадок см. в следующих статьях:

- [Monitor Azure SQL Data Sync with Azure Monitor logs](sql-database-sync-monitor-oms.md)
- [Устранение неполадок с синхронизацией данных SQL Azure (предварительная версия)](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Подробнее о базе данных SQL Azure

Дополнительные сведения о Базе данных SQL Azure см. в следующих статьях:

- [Обзор Базы данных SQL](sql-database-technical-overview.md)
- [Управление жизненным циклом базы данных](https://msdn.microsoft.com/library/jj907294.aspx)
