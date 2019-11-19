---
title: Заметки о выпуске
description: Узнайте о новых возможностях и улучшениях в службе базы данных SQL Azure и документации по базе данных SQL Azure.
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: 5c70d1d7d62b41965d4c81e8bbe0e5b2dc3cbb92
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166257"
---
# <a name="sql-database-release-notes"></a>Заметки о выпуске базы данных SQL

В этой статье перечислены функции базы данных SQL, которые в настоящее время доступны в общедоступной предварительной версии. Сведения об обновлениях и улучшениях базы данных SQL см. в статье [обновления службы базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database). Обновления и улучшения других служб Azure см. в разделе [обновления служб](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Возможности общедоступной предварительной версии

### <a name="single-databasetabsingle-database"></a>[Отдельная база данных](#tab/single-database)

| Функция | Подробная информация |
| ---| --- |
| Новые поколения оборудования серии серия fsv2 и M| Дополнительные сведения см. в разделе [поколения оборудования](sql-database-service-tiers-vcore.md#hardware-generations).|
| [Частная ссылка Azure](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Служба "Приватный канал" упрощает сетевую архитектуру и гарантирует безопасность подключения между конечными точками в Azure благодаря тому, что данные хранятся в сети Azure и не попадают в общедоступный Интернет. Кроме того, эта служба позволяет создавать и предоставлять собственные службы в Azure. |
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
|Аналитика SQL|Дополнительные сведения см. в разделе [аналитика SQL Azure](../azure-monitor/insights/azure-sql.md).|
|Отложенная компиляция табличной переменной (на уровне совместимости 150)|Дополнительные сведения см. в разделе [табличная переменная Отложенная компиляция](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Управляемый экземпляр](#tab/managed-instance)

| Функция | Подробная информация |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">Конфигурация подсети с разавтоматизированной службой</a> | Безопасный и удобный способ управления конфигурацией подсети. |
| <a href="/azure/sql-database/sql-database-instance-pools">Пулы экземпляров</a> | Удобный и экономичный способ переноса небольших экземпляров SQL в облако. |
| <a href="https://aka.ms/managed-instance-tde-byok">Прозрачное шифрование данных (TDE) с создание собственных ключей (BYOK)</a> |Дополнительные сведения см. [в статье прозрачное шифрование данных Azure SQL с управляемыми клиентом ключами в Azure Key Vault: создание собственных ключей поддержки](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-aadlogins">Участники сервера Azure AD уровня экземпляра (имена для входа)</a> | Создайте имена входа на уровне сервера, используя инструкцию <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login из внешнего поставщика</a> . |
| [Репликация транзакций](sql-database-managed-instance-transactional-replication.md) | Репликация изменений из таблиц в другие базы данных, размещенные на управляемых экземплярах, отдельных базах данных или SQL Server экземплярах, или при изменении некоторых строк в других управляемых экземплярах или экземплярах SQL Server. Дополнительные сведения см. [в статье Настройка репликации в базе данных управляемого экземпляра базы данных SQL Azure](replication-with-sql-database-managed-instance.md). |
| Обнаружение угроз |Дополнительные сведения см. [в статье Настройка обнаружения угроз в управляемом экземпляре базы данных SQL Azure](sql-database-managed-instance-threat-detection.md).|
| Повторное создание удаленных баз данных с помощью управляемых экземпляров |Дополнительные сведения см. [в статье повторное создание удаленных баз данных в Azure SQL управляемый экземпляр](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>новые функции;

### <a name="managed-instance-h2-2019-updates"></a>Обновления управляемого экземпляра H2 2019

- [Группы автоматической отработки отказа](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) позволяют реплицировать все базы данных с первичного экземпляра на дополнительный экземпляр в другом регионе.
- Настройте поведение управляемого экземпляра с помощью [глобальных флагов трассировки](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Обновления управляемого экземпляра H1 2019

Следующие функции включены в модели развертывания управляемого экземпляра в H1 2019:
  - Поддержка подписок с <a href="https://aka.ms/sql-mi-visual-studio-subscribers">ежемесячным кредитом Azure для подписчиков Visual Studio</a> и повышенными [региональными ограничениями](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Поддержка <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">SharePoint 2016 и SharePoint 2019</a>, а также <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a>.
  - Создайте экземпляры с <a href="https://aka.ms/managed-instance-collation">параметрами сортировки на уровне сервера</a> и <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">часовым поясом</a> по своему усмотрению.
  - Управляемые экземпляры теперь защищены <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">встроенным брандмауэром</a>.
  - Настройте экземпляры на использование [общедоступных конечных точек](sql-database-managed-instance-public-endpoint-configure.md), [Переопределение прокси-сервера](sql-database-connectivity-architecture.md#connection-policy) , чтобы повысить производительность сети, <a href="https://aka.ms/four-cores-sql-mi-update">4 виртуальных ядер на го поколения поколение оборудования</a> или <a href="https://aka.ms/managed-instance-configurable-backup-retention">настройте срок хранения резервных копий до 35 дней</a> для восстановления на момент времени. Долгосрочное хранение резервных копий (до 10 лет) по-прежнему не включено, поэтому в качестве альтернативы можно использовать <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">резервные копии только для копирования</a> .
  - Новые возможности позволяют выполнять <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">геовосстановление базы данных в другом центре обработки данных с помощью PowerShell</a>, [переименования базы данных](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [удаления виртуального кластера](sql-database-managed-instance-delete-virtual-cluster.md).
  - Новая встроенная [роль участника «экземпляр](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) » обеспечивает соответствие требованиям к разделению (разделения обязанностей) с учетом принципов безопасности и соответствия стандартам предприятия.
  - Управляемый экземпляр доступен в следующих регионах Azure для государственных организаций (US Gov (Техас), US Gov (Аризона)), а также в Северный Китай 2 и Восточный Китай 2. Она также доступна в следующих общедоступных регионах: Центральная Австралия, Центральная Австралия 2, Южная Бразилия, Юго-Восточная часть Франции, Центральная ОАЭ, Северная Народно-Восточная, Южная Африка, Северная Африка, Юго-Африканская Республика.

## <a name="fixed-known-issues"></a>Исправлены известные проблемы

- **2019 августа** — автономные базы данных полностью поддерживаются в управляемом экземпляре.
- **2019** . Встроенное восстановление базы данных на момент времени с уровня критически важный для бизнеса на уровень общего назначения не будет выполнено, если база данных-источник содержит объекты OLTP в памяти.
- **Ноя 2019** — проверка согласованности базы данных производится с помощью `DBCC CHECKDB` после восстановления базы данных из хранилища BLOB-объектов Azure.

## <a name="updates"></a>обновлениях

Список обновлений и улучшений базы данных SQL см. в статье [обновления службы базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database).

Обновления и улучшения для всех служб Azure см. в разделе [обновления служб](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Участие в содержимом

Чтобы внести изменения в документацию по базе данных SQL Azure, ознакомьтесь с [руководством для участников](https://docs.microsoft.com/contribute/)документации.
