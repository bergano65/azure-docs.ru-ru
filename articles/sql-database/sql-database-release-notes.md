---
title: Заметки о выпуске
description: Узнайте о новых возможностях и улучшениях в службе базы данных SQL Azure и документации по базе данных SQL Azure.
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: sstein
ms.openlocfilehash: 27a62223970b0f697465ce9aa050f3fccbcae464
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106429"
---
# <a name="sql-database-release-notes"></a>Заметки о выпуске базы данных SQL

В этой статье перечислены функции базы данных SQL, которые в настоящее время доступны в общедоступной предварительной версии. Сведения об обновлениях и улучшениях базы данных SQL см. в статье [обновления службы базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database). Обновления и улучшения других служб Azure см. в разделе [обновления служб](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Возможности общедоступной предварительной версии

### <a name="single-database"></a>[Отдельная база данных](#tab/single-database)

| Компонент | Сведения |
| ---| --- |
| Новые поколения оборудования серии серия fsv2 и M| Дополнительные сведения см. в разделе [поколения оборудования](sql-database-service-tiers-vcore.md#hardware-generations).|
| Ускоренное восстановление базы данных с отдельными базами данных и эластичными пулами | Дополнительные сведения см. в разделе [Ускорение восстановления базы данных](sql-database-accelerated-database-recovery.md).|
|Приблизительный подсчет различных объектов|Дополнительные сведения см. в разделе [приблизительный подсчет DISTINCT](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Пакетный режим в rowstore (на уровне совместимости 150)|Дополнительные сведения см. [в разделе пакетный режим в rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Обнаружение и классификация данных  |Дополнительные сведения см. в разделе [база данных SQL Azure и обнаружение данных хранилища данных sql & классификация](sql-database-data-discovery-and-classification.md).|
| Задания обработки эластичных баз данных | Дополнительные сведения см. в статье [Создание и настройка эластичных заданий и управление ими](elastic-jobs-overview.md). |
| Эластичные запросы | Дополнительные сведения см. в разделе [Общие сведения о эластичных запросах](sql-database-elastic-query-overview.md). |
| Эластичные транзакции | [Распределенные транзакции между облачными базами данных](sql-database-elastic-transactions-overview.md). |
|Обратная связь выделения памяти (режим строки) (на уровне совместимости 150)|Дополнительные сведения см. в разделе [обратная связь предоставления памяти (режим строки)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Редактор запросов в портал Azure |Дополнительные сведения см. в разделе [Использование редактора запросов SQL портал Azure для подключения и запроса данных](sql-database-connect-query-portal.md).|
| Службы R/машинное обучение с отдельными базами данных и эластичными пулами |Дополнительные сведения см. [в разделе службы машинного обучения в базе данных SQL Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Аналитика SQL|Дополнительные сведения см. в разделе [аналитика SQL Azure](../azure-monitor/insights/azure-sql.md).|
|Отложенная компиляция табличной переменной (на уровне совместимости 150)|Дополнительные сведения см. в разделе [табличная переменная Отложенная компиляция](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Управляемый экземпляр](#tab/managed-instance)

| Компонент | Сведения |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Пулы экземпляров</a> | Удобный и экономичный способ переноса небольших экземпляров SQL в облако. |
| <a href="https://aka.ms/managed-instance-aadlogins">Участники сервера Azure AD уровня экземпляра (имена для входа)</a> | Создайте имена входа на уровне сервера, используя инструкцию <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login из внешнего поставщика</a> . |
| [Репликация транзакций](sql-database-managed-instance-transactional-replication.md) | Репликация изменений из таблиц в другие базы данных, размещенные на управляемых экземплярах, отдельных базах данных или SQL Server экземплярах, или при изменении некоторых строк в других управляемых экземплярах или экземплярах SQL Server. Дополнительные сведения см. [в статье Настройка репликации в базе данных управляемого экземпляра базы данных SQL Azure](replication-with-sql-database-managed-instance.md). |
| Обнаружение угроз |Дополнительные сведения см. [в статье Настройка обнаружения угроз в управляемом экземпляре базы данных SQL Azure](sql-database-managed-instance-threat-detection.md).|
| Долгосрочное хранение резервных копий | Дополнительные сведения см. [в статье Настройка долгосрочного резервного копирования в управляемом экземпляре базы данных SQL Azure](sql-database-managed-instance-long-term-backup-retention-configure.md). | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>Управляемый экземпляр — новые функции и известные проблемы

### <a name="managed-instance-h2-2019-updates"></a>Обновления управляемого экземпляра H2 2019

- [Конфигурация подсети с разавтоматизированной службой](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Безопасный и удобный способ управления конфигурацией подсети, в которой контролируется трафик данных, а управляемый экземпляр обеспечивает непрерывный поток трафика управления.
- [Прозрачное шифрование данных (TDE) с создание собственных ключей (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) позволяет применять собственный ключ (BYOK) для защиты данных в неактивных целях и позволяет организациям отделить обязанности по управлению ключами и данными.
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

### <a name="known-issues"></a>Известные проблемы

|Проблема  |Дата обнаружения  |Status  |Дата разрешения  |
|---------|---------|---------|---------|
|[Разрешения для группы ресурсов, которые не применяются к Управляемый экземпляр](#permissions-on-resource-group-not-applied-to-managed-instance)|Фев 2020|Есть обходной путь||
|[Ограничение отработки отказа вручную с помощью портала для групп отработки отказа](#limitation-of-manual-failover-via-portal-for-failover-groups)|Янв 2020|Есть обходной путь||
|[Ролям агента SQL требуются явные разрешения на выполнение для имен входа, отличных от sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Есть обходной путь||
|[Задания агента SQL Server могут быть прерваны с помощью перезапуска процесса агента](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Обходной путь отсутствует|Мар 2020|
|[Имена входа и пользователи AAD не поддерживаются в SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|2019 ноября|Обходной путь отсутствует||
|[Ограничения памяти выполняющейся в памяти OLTP не применяются](#in-memory-oltp-memory-limits-are-not-applied)|Октябрь 2019|Есть обходной путь||
|[При попытке удаления непустого файла возвращена неверная ошибка](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Октябрь 2019|Есть обходной путь||
|[Изменение уровня служб и операций создания экземпляра заблокировано текущим восстановлением базы данных](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Есть обходной путь||
|[Resource Governor на уровне служб критически важный для бизнеса может потребоваться перенастроить после отработки отказа](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Есть обходной путь||
|[После обновления уровня службы необходимо повторно инициализировать диалоговые окна межбазовых Service Broker](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Авг 2019|Есть обходной путь||
|[Имперсонификатион типов входа Azure AD не поддерживается](#impersonification-of-azure-ad-login-types-is-not-supported)|Июл 2019|Обходной путь отсутствует||
|[@queryпараметр не поддерживается в sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Апр 2019|Обходной путь отсутствует||
|[После географической отработки отказа необходимо перенастроить репликацию транзакций](#transactional-replication-must-be-reconfigured-after-geo-failover)|Мар 2019|Обходной путь отсутствует||
|[Во время операции восстановления используется временная база данных](#temporary-database-is-used-during-restore-operation)||Есть обходной путь||
|[Структура и содержимое базы данных TEMPDB создано повторно](#tempdb-structure-and-content-is-re-created)||Обходной путь отсутствует||
|[Превышение дискового пространства с небольшими файлами баз данных](#exceeding-storage-space-with-small-database-files)||Есть обходной путь||
|[Значения GUID, отображаемые вместо имен баз данных](#guid-values-shown-instead-of-database-names)||Есть обходной путь||
|[Журналы ошибок не сохраняются](#error-logs-arent-persisted)||Обходной путь отсутствует||
|[Область транзакций в двух базах данных в одном и том же экземпляре не поддерживается](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Есть обходной путь|Март 2020 г.|
|[Модули CLR и связанные серверы иногда не могут ссылаться на локальный IP-адрес.](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Есть обходной путь||
|Согласованность базы данных не проверена с помощью инструкции DBCC CHECKDB после восстановления базы данных из хранилища BLOB-объектов Azure.||"Разрешено"|2019 ноября|
|Восстановление базы данных на момент времени с уровня критически важный для бизнеса на уровень общего назначения не будет выполняться, если база данных-источник содержит объекты OLTP в памяти.||"Разрешено"|Октябрь 2019|
|Database Mailная функция с внешними (не Azure) почтовыми серверами с использованием безопасного подключения||"Разрешено"|Октябрь 2019|
|Автономные базы данных не поддерживаются в управляемом экземпляре||"Разрешено"|Авг 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Разрешения для группы ресурсов, не примененные к управляемому экземпляру

Роль RBAC Управляемый экземпляр участника, применяемая к группе ресурсов (RG), не применяется к Управляемый экземпляр и не действует.

**Обходное решение**. Настройте роль участника управляемый экземпляр для пользователей на уровне подписки.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Ограничение отработки отказа вручную с помощью портала для групп отработки отказа

Если группа отработки отказа охватывает экземпляры в разных подписках Azure или группах ресурсов, отработка отказа вручную не может быть инициирована из основного экземпляра в группе отработки отказа.

**Обходное решение**. Инициируйте отработку отказа с помощью портала из экземпляра Geo-Secondary.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Ролям агента SQL требуются явные разрешения на выполнение для имен входа, отличных от sysadmin

Если имена входа, отличные от системного администратора, добавляются к любой из [предопределенных ролей базы данных агента SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles), существует проблема, в которой для работы этих имен входа должны быть предоставлены явные разрешения на выполнение. В случае возникновения этой проблемы появляется сообщение об ошибке "недопустимое разрешение на выполнение объекта <object_name будет показано> (Microsoft SQL Server, ошибка: 229)".

**Решение**. После добавления имен входа в одну из предопределенных ролей базы данных агента SQL: SQLAgentUserRole, SQLAgentReaderRole или SQLAgentOperatorRole для каждого имени входа, добавленного в эти роли, выполняет приведенный ниже скрипт T-SQL для явного предоставления разрешений EXECUTE для перечисленных хранимых процедур.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Задания агента SQL Server могут быть прерваны с помощью перезапуска процесса агента

Агент SQL создает новый сеанс при каждом запуске задания, постепенно увеличивая потребление памяти. Чтобы избежать попадания в пределы внутренней памяти, которая может блокировать выполнение запланированных заданий, процесс агента будет перезапущен после достижения порогового значения потребления памяти. Это может привести к прерыванию выполнения заданий, выполняемых в момент перезапуска.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Ограничения памяти выполняющейся в памяти OLTP не применяются

Критически важный для бизнеса уровня служб не будет правильно применять [максимальные ограничения памяти для оптимизированных для памяти объектов](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) в некоторых случаях. Управляемый экземпляр может позволить рабочей нагрузке использовать больше памяти для операций OLTP в памяти, что может повлиять на доступность и стабильность экземпляра. Запросы выполняющейся в памяти OLTP, которые достигли ограничений, могут не завершаться сбоем немедленно. Эта проблема будет исправлена в ближайшее время. Запросы, в которых используется больше памяти OLTP в памяти, будут завершаться сбоем быстрее, если достигнут [предел](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space).

**Обходное решение.** [отслеживайте использование хранилища выполняющейся в памяти OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) с помощью [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) , чтобы убедиться, что Рабочая нагрузка не использует больше доступной памяти. Увеличьте ограничения памяти, зависящие от числа виртуальных ядер, или оптимизируйте рабочую нагрузку для использования меньшего объема памяти.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>При попытке удаления непустого файла возвращена неверная ошибка

SQL Server и Управляемый экземпляр [не позволяют пользователю удалять непустые файлы](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Если попытаться удалить непустой файл данных с помощью `ALTER DATABASE REMOVE FILE` инструкции, то эта ошибка `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` не будет немедленно возвращена. Управляемый экземпляр будет пытаться удалить файл, и операция завершится ошибкой после 30 мин с `Internal server error`.

Инструкции по **решению**: удалите содержимое файла с помощью `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` команды. Если это единственный файл в файловой группе, необходимо удалить данные из таблицы или секции, связанной с этой файловой группой, перед сжатием файла и при необходимости загрузить эти данные в другую таблицу или секцию.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Изменение уровня служб и операций создания экземпляра заблокировано текущим восстановлением базы данных

Непрерывная `RESTORE` инструкция, процесс миграции данных и встроенное восстановление на момент времени будут блокировать обновление уровня служб или изменение размера существующего экземпляра и создание новых экземпляров до завершения процесса восстановления. Процесс восстановления блокирует эти операции с управляемыми экземплярами и пулами экземпляров в той же подсети, где выполняется процесс восстановления. Экземпляры в пулах экземпляров не затрагиваются. Операции создания или изменения уровня служб не будут завершаться сбоем или превышено время ожидания — они будут продолжены после завершения или отмены процесса восстановления.

**Решение**. Дождитесь завершения процесса восстановления или отмените процесс восстановления, если операция создания или обновления уровня службы имеет более высокий приоритет.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor на уровне служб критически важный для бизнеса может потребоваться перенастроить после отработки отказа

Функция [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) , которая позволяет ограничить ресурсы, назначенные пользовательской рабочей нагрузке, может неправильно классифицировать определенную рабочую нагрузку пользователя после отработки отказа или изменения уровня обслуживания, инициированного пользователем (например, изменение максимального Виртуальное ядро или максимального размера хранилища экземпляра).

**Workaround**Инструкции по решению `ALTER RESOURCE GOVERNOR RECONFIGURE` : периодически запускайте или как часть задания агента SQL, которое выполняет задачу SQL при запуске экземпляра, если используется [Resource Governor](/sql/relational-databases/resource-governor/resource-governor).

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>После обновления уровня службы необходимо повторно инициализировать диалоговые окна межбазовых Service Broker

Межбазовые Service Broker диалоговые окна перестают предоставлять сообщения службам в других базах данных после изменения уровня служб. Сообщения **не теряются** , и их можно найти в очереди отправителя. Любое изменение размера хранилища виртуальных ядер или экземпляра в Управляемый экземпляр приведет к изменению `service_broke_guid` значения в представлении [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) для всех баз данных. Все `DIALOG` созданные с помощью инструкции [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) , которая ссылается на брокеры служб в другой базе данных, прекращает доставку сообщений целевой службе.

**Обходной путь:** Перед обновлением уровня службы и повторной их инициализацией после этого отключите все действия, использующие диалоговые окна межбазовых Service Broker. Если остались сообщения, которые не доставляются после изменения уровня службы, прочтите сообщения из очереди источника и повторно отправьте их в целевую очередь.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Имперсонификатион типов входа Azure AD не поддерживается

Олицетворение с использованием `EXECUTE AS USER` или `EXECUTE AS LOGIN` из следующих субъектов AAD не поддерживается:
-    Пользователи AAD с псевдонимами. В этом случае `15517`возвращается следующая ошибка.
- Имена входа и пользователей AAD, основанные на приложениях AAD или субъектах-службах. В этом случае `15517` возвращаются следующие ошибки и `15406`.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryпараметр не поддерживается в sp_send_db_mail

`@query` Параметр в процедуре [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) не работает.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>После географической отработки отказа необходимо перенастроить репликацию транзакций

Если репликация транзакций включена для базы данных в группе автоматической отработки отказа, администратор управляемого экземпляра должен очистить все публикации на старом первичном ресурсе и перенастроить их на новом первичном ресурсе после отработки отказа в другой регион. Дополнительные сведения см. в разделе [репликация](sql-database-managed-instance-transact-sql-information.md#replication) .

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Имена входа и пользователи AAD не поддерживаются в SSDT

SQL Server Data Tools не полностью поддерживают имена входа и пользователей Azure Active Directory.

### <a name="temporary-database-is-used-during-restore-operation"></a>Во время операции восстановления используется временная база данных

При восстановлении базы данных в Управляемый экземпляр Служба восстановления сначала создаст пустую базу данных с нужным именем, чтобы выделить имя экземпляра. Через некоторое время эта база данных будет удалена и восстановлена фактическая база данных. База данных, которая находится в состоянии *восстановления* , будет иметь случайное значение идентификатора GUID вместо имени. После завершения процесса восстановления временное имя будет изменено на нужное `RESTORE` имя, указанное в инструкции. На начальном этапе пользователь может получить доступ к пустой базе данных и даже создать таблицы или загрузить данные в эту базу данных. Эта временная база данных будет удалена, когда служба восстановления запустит второй этап.

Инструкции по решению: не **пытайтесь**получить доступ к восстанавливаемой базе данных до тех пор, пока не будет показано, что восстановление завершено.

### <a name="tempdb-structure-and-content-is-re-created"></a>Структура и содержимое базы данных TEMPDB создано повторно

`tempdb` База данных всегда разделяется на 12 файлов данных, и структура файла не может быть изменена. Максимальный размер для файла нельзя изменить, и новые файлы нельзя добавить в `tempdb`. `Tempdb`всегда создается повторно как пустая база данных при запуске или отработки отказа экземпляра, а любые изменения, `tempdb` внесенные в, не сохраняются.

### <a name="exceeding-storage-space-with-small-database-files"></a>Превышение дискового пространства с небольшими файлами баз данных

`CREATE DATABASE`инструкции `ALTER DATABASE ADD FILE`, и `RESTORE DATABASE` могут завершиться ошибкой, так как экземпляр может достигнуть ограничения хранилища Azure.

Каждый управляемый экземпляр общего назначения имеет до 35 ТБ хранилища, зарезервированного для дискового пространства Azure уровня "Премиум". Каждый файл базы данных размещается на отдельном физическом диске. Поддерживаются диски размером 128 ГБ, 256 ГБ, 512 ГБ, 1 ТБ или 4 ТБ. Неиспользуемое пространство на диске не заряжено, но общая сумма размеров дисков Azure уровня "Премиум" не может превышать 35 ТБ. В некоторых случаях управляемый экземпляр, для которого не требуется 8 ТБ, может превысить ограничение в 35 ТБ на размер хранилища из-за внутренней фрагментации.

Например, управляемый экземпляр общего назначения может иметь один большой файл размером 1,2 ТБ, размещенный на диске объемом 4 ТБ. У него также может быть 248 файлов с размером 1 ГБ, каждый из которых размещается на отдельных дисках емкостью 128 ГБ. В этом примере:

- общий размер выделенного дискового хранилища составляет 1 x 4 ТБ + 248 x 128 ГБ = 35 ТБ;
- общий объем зарезервированного пространства для баз данных в экземпляре составляет 1 x 1,2 ТБ + 248 x 1 ГБ = 1,4 ТБ.

В этом примере показано, что в некоторых обстоятельствах при определенных распределениях файлов управляемый экземпляр может достичь предельного размера в 35 ТБ, зарезервированного для подключенного диска Azure уровня "Премиум", если вы не ожидаете его.

В этом примере существующие базы данных продолжают работать и могут увеличиваться без каких бы то ни было проблем, пока новые файлы не добавляются. Новые базы данных не могут быть созданы или восстановлены, так как не хватает места для новых дисков, даже если общий размер всех баз данных не достигает предельного размера экземпляра. Ошибка, возвращаемая в этом случае, не ясно.

[Количество оставшихся файлов можно узнать](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) с помощью системных представлений. Если вы достигли этого ограничения, попытайтесь [удалить некоторые файлы меньшего размера с помощью инструкции DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) или переключитесь на [уровень критически важный для бизнеса, который не имеет этого ограничения](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Значения GUID, отображаемые вместо имен баз данных

Несколько системных представлений, счетчиков производительности, сообщений об ошибках, XEvents и записей в журнале ошибок отображают идентификаторы базы данных GUID вместо фактических имен базы данных. Не полагайтесь на эти идентификаторы GUID, так как они заменяются фактическими именами баз данных в будущем.

**Обходное решение**. Используйте представление sys. databases для разрешения фактического имени базы данных из имени физической базы данных, указанного в виде идентификаторов базы данных GUID.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Журналы ошибок не сохраняются

Журналы ошибок, доступные в управляемом экземпляре, не сохраняются, и их размер не включается в максимальный предел хранилища. Если происходит отработка отказа, журналы ошибок могут автоматически удаляться. В журнале ошибок могут содержаться пробелы, так как Управляемый экземпляр были перемещены несколько раз на несколько виртуальных машин.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Область транзакций в двух базах данных в одном и том же экземпляре не поддерживается

**(Разрешено в марте 2020)** `TransactionScope` Класс в .NET не работает, если два запроса отправляются в две базы данных в одном экземпляре в одной и той же области транзакций:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Обходной путь (не требуется с 2020 марта):** Используйте [SqlConnection. чанжедатабасе (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) для использования другой базы данных в контексте соединения вместо двух соединений.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Модули CLR и связанные серверы иногда не могут ссылаться на локальный IP-адрес.

Модули CLR, размещенные в управляемом экземпляре, а также на связанных серверах или распределенных запросах, которые ссылаются на текущий экземпляр, иногда не могут разрешить IP-адрес локального экземпляра. Это временная ошибка.

**Обходной путь:** По возможности используйте контекстные соединения в модуле CLR.

## <a name="updates"></a>Обновления

Список обновлений и улучшений базы данных SQL см. в статье [обновления службы базы данных SQL](https://azure.microsoft.com/updates/?product=sql-database).

Обновления и улучшения для всех служб Azure см. в разделе [обновления служб](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Участие в разработке документации

Чтобы внести изменения в документацию по базе данных SQL Azure, ознакомьтесь с [руководством для участников](https://docs.microsoft.com/contribute/)документации.
