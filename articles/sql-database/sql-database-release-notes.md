---
title: Заметки о выпуске
description: Узнайте о новых функциях и усовершенствованиях в службе баз данных Azure S'L и в документации базы данных Azure S'L
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: 9fa93af72c2869efd7b6d2f1e8b96b0e667f8b16
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607548"
---
# <a name="sql-database-release-notes"></a>Примечания к выпуску базы данных S'L

В этой статье перечислены функции базы данных S'L, которые в настоящее время находятся в открытом предварительном просмотре. Для обновления и усовершенствования [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database)базы данных s'L см. Для получения обновлений и улучшений в других службах Azure [см.](https://azure.microsoft.com/updates)

## <a name="features-in-public-preview"></a>Особенности в публичном предварительном просмотре

### <a name="single-database"></a>[Отдельная база данных](#tab/single-database)

| Компонент | Сведения |
| ---| --- |
| Новые поколения оборудования серии Fsv2 и M-серии| Для получения информации, см [Аппаратные поколения](sql-database-service-tiers-vcore.md#hardware-generations).|
| [Частная ссылка Azure](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Private Link упрощает архитектуру сети и обеспечивает связь между конечными точками в Azure, сохраняя данные в сети Azure, тем самым устраняя доступ к Интернету. Private Link также позволяет создавать и предоставлять собственные услуги в Azure. |
| Ускоренное восстановление базы данных с помощью единых баз данных и эластичных пулов | Для получения [информации, см Ускоренное восстановление базы данных](sql-database-accelerated-database-recovery.md).|
|Приблизительный подсчет различных объектов|Для получения информации, [см.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)|
|Режим пакетов на Rowstore (под уровнем совместимости 150)|Для получения информации [см. режим пакетов на Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Обнаружение и классификация данных  |Для получения информации [&](sql-database-data-discovery-and-classification.md)см.|
| Задания обработки эластичных баз данных | Для получения информации [см. Создать, настроить и управлять эластичными заданиями.](elastic-jobs-overview.md) |
| Эластичные запросы | Для получения информации смотрите [обзор запроса Elastic](sql-database-elastic-query-overview.md). |
| Эластичные транзакции | [Распределенные транзакции по облачным базам данных.](sql-database-elastic-transactions-overview.md) |
|Обратная связь с памятью Гранта (режим строки) (под уровнем совместимости 150)|Для получения информации, см [Память Грант Обратная связь (режим строки)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Редактор запросов на портале Azure |Для получения информации посетите [редактор запроса портала Azure для подключения и запроса данных.](sql-database-connect-query-portal.md)|
| R-сервисы / машинное обучение с едиными базами данных и эластичными пулами |Для получения информации можно ознакомиться на [службах машинного обучения в базе данных Azure S'L.](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)|
|Аналитика SQL|Для получения информации, [см.](../azure-monitor/insights/azure-sql.md)|
|Переменная переменная отложенная компиляция таблицы (под уровнем совместимости 150)|Для получения информации [см. Таблица Переменная Отложенная компиляция](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instance"></a>[Управляемая инстанция](#tab/managed-instance)

| Компонент | Сведения |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Пулы экземпляров</a> | Удобный и экономичный способ переноса небольших экземпляров S'L в облако. |
| <a href="https://aka.ms/managed-instance-aadlogins">Принципы серверов Azure AD уровня инстанций (логины)</a> | Создавайте логины на уровне сервера с помощью заявления <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER.</a> |
| [Транзакционная репликация](sql-database-managed-instance-transactional-replication.md) | Воспроизвести изменения из таблиц в другие базы данных, размещенные в управляемых инстанциях, единичных базах данных или в экземплярах сервера S'L, или обновите таблицы, когда некоторые строки изменены в других управляемых инстанциях или экземпляре сервера S'L. Для получения информации [см. Репликацию настройки в управляемой базой данных данных данных azure S'L.](replication-with-sql-database-managed-instance.md) |
| Обнаружение угроз |Для получения информации [см. Наверждем обнаружение угроз в управляемом экземпляре базы данных Azure S'L Database.](sql-database-managed-instance-threat-detection.md)|

---

## <a name="managed-instance---new-features-and-known-issues"></a>Управляемый экземпляр - новые функции и известные проблемы

### <a name="managed-instance-h2-2019-updates"></a>Управляемый экземпляр H2 2019 обновления

- [Конфигурация подсети с помощью сервиса](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Безопасный и удобный способ управления конфигурацией подсети, где вы управляете трафиком данных при управляемом экземпляре, обеспечивает непрерывный поток трафика управления
- [Прозрачное шифрование данных (TDE) с Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) позволяет принести свой собственный ключ (BYOK) сценарий для защиты данных в состоянии покоя и позволяет организациям отделить обязанности по управлению ключами и данными.
- [Группы автоматического отказа](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) позволяют воспроизвести все базы данных от основного экземпляра до вторичного экземпляра в другом регионе.
- Наверстируйте поведение управляемого экземпляра с [помощью глобальных флагов трассировки.](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)

### <a name="managed-instance-h1-2019-updates"></a>Управляемый экземпляр H1 2019 обновления

Следующие функции включены в модели развертывания управляемых экземпляров в H1 2019:
  - Поддержка подписки с <a href="https://aka.ms/sql-mi-visual-studio-subscribers">ежемесячным кредитом Azure для подписчиков Visual Studio</a> и увеличение [региональных лимитов.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
  - Поддержка <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">SharePoint 2016 и SharePoint 2019</a>, а также <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a>.
  - Создавайте экземпляры с <a href="https://aka.ms/managed-instance-collation">коллажами уровня сервера</a> и <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">часовыми поясами</a> по вашему выбору.
  - Управляемые экземпляры теперь защищены <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">встроенным брандмауэром.</a>
  - Настройка экземпляров для использования [общедоступных конечных точек,](sql-database-managed-instance-public-endpoint-configure.md) [прокси переопределить](sql-database-connectivity-architecture.md#connection-policy) соединение, чтобы получить лучшую производительность сети, <a href="https://aka.ms/four-cores-sql-mi-update">4 vCores на Gen5 аппаратного поколения</a> или настроить <a href="https://aka.ms/managed-instance-configurable-backup-retention">резервное копирование удержания до 35 дней</a> для точки в срок восстановления. Долгосрочное сохранение резервного копирования (до 10 лет) по-прежнему не включено, так что вы можете использовать <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">только для копирования резервные копии</a> в качестве альтернативы.
  - Новые функции позволяют <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">геовосстановить базу данных в другой центр обработки данных с помощью PowerShell,</a> [переименовать базу данных,](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) [удалить виртуальный кластер.](sql-database-managed-instance-delete-virtual-cluster.md)
  - Новая встроенная [роль вкладчика инстанций](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) позволяет разделить обязанности (SoD) в соответствии с принципами безопасности и соблюдать корпоративные стандарты.
  - Управляемый экземпляр доступен в следующих регионах правительства Azure в GA (US Gov Texas, US Gov Arizona), а также в Китае North 2 и China East 2. Он также доступен в следующих общественных регионах: Австралия Центральной, Австралия Центральной 2, Бразилия на юге, Франция на юге, ОАЭ Центральной, ОАЭ Севера, северной части Африки, южная Африка Запад.

### <a name="known-issues"></a>Известные проблемы

|Проблема  |Дата обнаружена  |Состояние  |Дата решена  |
|---------|---------|---------|---------|
|[Разрешения на группу ресурсов, не применяемые к Управляемой инстанции](#permissions-on-resource-group-not-applied-to-managed-instance)|Февраль 2020 г.|Имеет обходной путь||
|[Ограничение ручного сбоя через портал для групп неудач](#limitation-of-manual-failover-via-portal-for-failover-groups)|Январь 2020 г.|Имеет обходной путь||
|[Роли агента S'L нуждаются в явных разрешениях EXECUTE для входа в систему, не относясь к sysadmin](#in-memory-oltp-memory-limits-are-not-applied)|Декабрь 2019 г.|Имеет обходной путь||
|[Рабочие места агента Пос-L могут быть прерваны перезагрузкой процесса агента](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Декабрь 2019 г.|Без обхода|Март 2020 г.|
|[Логины и пользователи AAD не поддерживаются в SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|Ноябрь 2019 года|Без обхода||
|[Ограничения памяти OLTP в памяти в памяти не применяются](#in-memory-oltp-memory-limits-are-not-applied)|Октябрь 2019 г.|Имеет обходной путь||
|[Неправильная ошибка возвращается при попытке удалить файл, который не пуст](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Октябрь 2019 г.|Имеет обходной путь||
|[Изменение уровня службы и создание операций экземпляров блокируется текущим восстановлением базы данных](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|сентябрь 2019 г.|Имеет обходной путь||
|[Ресурсный управляющий на уровне бизнес-критических услуг, возможно, потребуется перенастроить после сбоя](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|сентябрь 2019 г.|Имеет обходной путь||
|[Диалоги по перекрестной базе данных Service Broker должны быть переинализованы после обновления уровня обслуживания](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Авг 2019|Имеет обходной путь||
|[Олицетворение типов входа Azure AD не поддерживается](#impersonification-of-azure-ad-login-types-is-not-supported)|Июл 2019|Без обхода||
|[@queryпараметр не поддерживается в sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Апрель 2019|Без обхода||
|[Транзакционная репликация должна быть перенастроена после геоотказа](#transactional-replication-must-be-reconfigured-after-geo-failover)|Март 2019 г.|Без обхода||
|[Временная база данных используется во время операции RESTORE](#temporary-database-is-used-during-restore-operation)||Имеет обходной путь||
|[Структура и контент TEMPDB воссозданы](#tempdb-structure-and-content-is-re-created)||Без обхода||
|[Превышение дискового пространства с небольшими файлами баз данных](#exceeding-storage-space-with-small-database-files)||Имеет обходной путь||
|[Значения GUID, отображаемые вместо названий баз данных](#guid-values-shown-instead-of-database-names)||Имеет обходной путь||
|[Ошибки не сохраняются](#error-logs-arent-persisted)||Без обхода||
|[Область транзакций на двух базах данных в одном экземпляре не поддерживается](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Имеет обходной путь||
|[Модули CLR и связанные серверы иногда не могут ссылаться на локальный IP-адрес](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Имеет обходной путь||
|Согласованность базы данных не проверена с помощью DBCC CHECKDB после восстановления базы данных из Azure Blob Storage.||"Разрешено"|Ноябрь 2019 года|
|Восстановление базы данных Point-in-time от уровня Бизнес критического до общего назначения не будет успешным, если исходная база данных содержит объекты OLTP-памяти в памяти.||"Разрешено"|Октябрь 2019 г.|
|Функция "Почта базы данных" с внешними (не-Azure) почтовыми серверами с использованием безопасного соединения||"Разрешено"|Октябрь 2019 г.|
|Содержащиеся базы данных, не поддерживаемые в управляемом экземпляре||"Разрешено"|Авг 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Разрешения на группу ресурсов, не применяемые к управляемому экземпляру

Роль управляемого вкладчика RBAC при применении к группе ресурсов (RG) не применяется к Управляемой инстанции и не имеет эффекта.

**Обходной путь**: Настройка Управляемой роли вкладчика инстанций для пользователей на уровне подписки.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Ограничение ручного сбоя через портал для групп неудач

Если группа failover охватывает экземпляры в разных группах подписчиков Azure или группах ресурсов, сбой вручную не может быть инициирован из основного экземпляра в группе failover.

**Обход :** Инициировать сбой через портал из гео-вторичного экземпляра.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Роли агента S'L нуждаются в явных разрешениях EXECUTE для входа в систему, не относясь к sysadmin

Если логины, не связанные с sysadmin, добавляются к какой-либо из [ролей фиксированной базы данных s'L Agent,](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)существует проблема, в которой явные разрешения EXECUTE должны быть предоставлены мастеру, хранящим процедуры для работы этих логинов. В случае возникновения этой проблемы будет показано сообщение об ошибке "Разрешение EXECUTE было отказано на объекте <object_name> (Microsoft S'L Server, Ошибка: 229)" будет отображаться.

**Обход:** Как только вы добавляете логины к одной из функций фиксированной базы данных агента S'LL: S'LAgentUserRole, S'LAgentReaderRole или S'LAgentOperatorRole, для каждой из входов, добавленных в эти роли, ниже t-S'L скрипт явно предоставляет сяорна разрешения на сохраненные процедуры, перечисленные.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Рабочие места агента Пос-L могут быть прерваны перезагрузкой процесса агента

СЗЛ Агент создает новую сессию каждый раз, когда задание начинается, постепенно увеличивая потребление памяти. Чтобы избежать попадания на внутренний предел памяти, который блокирует выполнение запланированных заданий, процесс агента будет перезапущен, как только его потребление памяти достигнет порога. Это может привести к прерыванию выполнения заданий, работающих в момент перезагрузки.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Ограничения памяти OLTP в памяти в памяти не применяются

Бизнес Критический сервис-уровень не будет правильно применять [максимальные ограничения памяти для объектов, оптимизированных памятью](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) в некоторых случаях. Управляемый экземпляр может позволить рабочей нагрузке использовать больше памяти для операций In-memory OLTP, что может повлиять на доступность и стабильность экземпляра. Запросы OLTP в памяти, достигающие пределов, могут не выйти из строя немедленно. Эта проблема будет исправлена в ближайшее время. Запросы, которые используют больше памяти in-memory OLTP, сбой быстрее, если они достигают [пределов.](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)

**Обход:** [Мониторинг использования хранилища в памяти OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) с помощью [студии управления серверами S'L,](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) чтобы гарантировать, что рабочая нагрузка не использует больше, чем доступная память. Увеличьте лимиты памяти, зависят от количества vCore, или оптимизируйте рабочую нагрузку, чтобы использовать меньше памяти.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Неправильная ошибка возвращается при попытке удалить файл, который не пуст

СЗЛ Сервер /Управляемый instance [не позволяет пользователю отказаться от файла, который не пуст.](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites) Если вы попытаетесь удалить непустой файл данных с помощью `ALTER DATABASE REMOVE FILE` оператора, ошибка `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` не будет немедленно возвращена. Управляемый Instance будет продолжать пытаться отказаться от файла `Internal server error`и операция не удастся после 30минут с .

**Обход:** Удалить содержимое файла `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` с помощью команды. Если это единственный файл в группе файлов, вам необходимо удалить данные из таблицы или раздела, связанные с этой группой файлов, прежде чем уменьшить файл, и дополнительно загрузить эти данные в другую таблицу / раздел.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Изменение уровня службы и создание операций экземпляров блокируется текущим восстановлением базы данных

Текущее `RESTORE` заявление, процесс миграции службы обработки данных и встроенное время восстановления времени блокируют обновление уровня службы или изменение размера существующего экземпляра и создание новых экземпляров до завершения процесса восстановления. Процесс восстановления блокирует эти операции в управляемых экземплярах и пулах экземпляров в той же подсети, где работает процесс восстановления. Экземпляры в экземплярах не затрагиваются. Создание или изменение операций уровня обслуживания не потерпит неудачу или тайм-аут - они будут продолжены после завершения или отмены процесса восстановления.

**Обход:** Подождите, пока процесс восстановления не завершится, или отмените процесс восстановления, если работа создания или обновления уровня обслуживания имеет более высокий приоритет.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Ресурсный управляющий на уровне бизнес-критических услуг, возможно, потребуется перенастроить после сбоя

Функция [«Губернатор ресурсов»,](/sql/relational-databases/resource-governor/resource-governor) позволяющая ограничить ресурсы, назначенные рабочей нагрузке пользователя, может неправильно классифицировать некоторые рабочие нагрузки пользователя после сбоя или инициированного пользователем изменения уровня обслуживания (например, изменение размера хранилища max vCore или max instance).

**Обход : Выполняйте** `ALTER RESOURCE GOVERNOR RECONFIGURE` периодически или в рамках работы агента S'L, выполняющей задачу S'L при запуске экземпляра при использовании [ресурсного управляющего.](/sql/relational-databases/resource-governor/resource-governor)

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Диалоги по перекрестной базе данных Service Broker должны быть переинализованы после обновления уровня обслуживания

Диалоги службы по перекрестной базе данных службы брокера перестанут доставлять сообщения службам в других базах данных после изменения работы уровня обслуживания. Сообщения **не потеряны,** и они могут быть найдены в очереди отправителя. Любое изменение vCores или размера хранилища экземпляров в Управляемом экземпляре приведет к `service_broke_guid` изменению значения в [представлении sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) для всех баз данных. Любой созданный `DIALOG` с помощью [заявления BEGIN DIALOG,](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) которое ссылается на сервисных брокеров в другой базе данных, перестанет доставлять сообщения в целевую службу.

**Обходной путь:** Остановите любое действие, использующее диалоговые диалоги по перекрестным базам данных Service Broker, прежде чем обновлять уровень обслуживания, и повторно инициализируете их после. Если есть оставшиеся сообщения, которые не будут доставлены после изменения уровня обслуживания, прочитайте сообщения из исходной очереди и отправьте их в целевую очередь.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Олицетворение типов входа Azure AD не поддерживается

Олицетворение `EXECUTE AS USER` `EXECUTE AS LOGIN` с использованием или следующих принципов AAD не поддерживается:
-   Псевдоним AAD пользователей. В этом случае `15517`возвращается следующая ошибка.
- AAD логины и пользователи на основе aAD приложений или основ услуг. В этом случае `15517` возвращаются `15406`следующие ошибки и .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryпараметр не поддерживается в sp_send_db_mail

Параметр `@query` в [процедуре sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) не работает.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Транзакционная репликация должна быть перенастроена после геоотказа

Если транзакционная репликация включена в базе данных в группе автоматического отказа, администратор управляемого экземпляра должен очистить все публикации на старом элементаре и перенастроить их на новый основной после сбоя в другой области. Более подробная информация приводит к [ремиктии.](sql-database-managed-instance-transact-sql-information.md#replication)

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>Логины и пользователи AAD не поддерживаются в SSDT

Инструменты данных серверов s'L не полностью поддерживают логины каталогов Azure Active и пользователей.

### <a name="temporary-database-is-used-during-restore-operation"></a>Временная база данных используется во время операции RESTORE

При восстановлении базы данных в Управляемой инстанции служба восстановления сначала создаст пустую базу данных с желаемым именем для выделения имени в экземпляре. Через некоторое время эта база данных будет удалена и будет начато восстановление фактической базы данных. База данных, наминое в *состоянии восстановления,* будет временно иметь случайное значение GUID вместо имени. Временное имя будет изменено на `RESTORE` желаемое имя, указанное в выписке после завершения процесса восстановления. На начальном этапе пользователь может получить доступ к пустой базе данных и даже создавать таблицы или загружать данные в эту базу данных. Эта временная база данных будет удалена при запуске службы восстановления второй фазы.

**Обход**: Не получите доступ к базе данных, которую вы восстанавливаете, пока не увидите, что восстановление завершено.

### <a name="tempdb-structure-and-content-is-re-created"></a>Структура и контент TEMPDB воссозданы

База `tempdb` данных всегда разделена на 12 файлов данных, и структура файла не может быть изменена. Максимальный размер файла не может быть изменен, и `tempdb`новые файлы не могут быть добавлены в . `Tempdb`всегда воссоздан как пустая база данных, когда экземпляр запускается `tempdb` или завершается, и любые внесенные изменения не будут сохранены.

### <a name="exceeding-storage-space-with-small-database-files"></a>Превышение дискового пространства с небольшими файлами баз данных

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, `RESTORE DATABASE` и операторы могут выйти из строя, потому что экземпляр может достичь предела хранилища Azure.

Каждый управляемый экземпляр Общего назначения имеет до 35 ТБ хранилища, зарезервированных для пространства Azure Premium Disk. Каждый файл базы данных размещается на отдельном физическом диске. Поддерживаются диски размером 128 ГБ, 256 ГБ, 512 ГБ, 1 ТБ или 4 ТБ. Неиспользованное пространство на диске не взимается, но общая сумма размеров Azure Premium Disk не может превышать 35 ТБ. В некоторых случаях управляемый экземпляр, которому не нужно 8 ТБ в общей сложности, может превысить 35-й предел По размеру хранилища из-за внутренней фрагментации.

Например, в управляемом экземпляре общего назначения может быть один большой файл размером 1,2 ТБ, размещенный на 4-ТБ-диске. Он также может иметь 248 файлов с 1 ГБ размер каждого, которые размещены на отдельных 128-ГБ дисков. В этом примере:

- общий размер выделенного дискового хранилища составляет 1 x 4 ТБ + 248 x 128 ГБ = 35 ТБ;
- общий объем зарезервированного пространства для баз данных в экземпляре составляет 1 x 1,2 ТБ + 248 x 1 ГБ = 1,4 ТБ.

Этот пример показывает, что при определенных обстоятельствах из-за определенного распространения файлов управляемый экземпляр может достичь предела 35 ТБ, который зарезервирован для присоединенного премиум-диска Azure, когда вы не можете ожидать этого.

В этом примере существующие базы данных продолжают работать и могут расти без каких-либо проблем до тех пор, пока не будут добавлены новые файлы. Новые базы данных не могут быть созданы или восстановлены, поскольку не хватает места для новых дисков, даже если общий размер всех баз данных не достигает предела размера экземпляра. Ошибка, которая возвращается в этом случае, не ясна.

Количество [оставшихся файлов](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) можно определить с помощью системных представлений. Если вы достигнете этого предела, попробуйте [очистить и удалить некоторые из небольших файлов с помощью dBCC SHRINKFILE заявление](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) или переключиться на бизнес [критический уровень, который не имеет этого предела.](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)

### <a name="guid-values-shown-instead-of-database-names"></a>Значения GUID, отображаемые вместо названий баз данных

Несколько системных представлений, счетчиков производительности, сообщений об ошибках, XEvents и записей в журнале ошибок отображают идентификаторы базы данных GUID вместо фактических имен базы данных. Не полагайтесь на эти идентификаторы GUID, поскольку в будущем они будут заменены фактическими именами баз данных.

**Обходной путь**: Используйте представление sys.databases для разрешения фактического имени базы данных из физического имени базы данных, указанного в форме идентификаторов базы данных GUID

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Ошибки не сохраняются

Ошибки журналов, доступных в управляемом экземпляре, не сохраняются, а их размер не включен в максимальный лимит хранения. Ошибки журналы могут быть автоматически удалены, если происходит сбой. В истории журнала ошибок могут быть пробелы, поскольку Managed Instance несколько раз перемещался на нескольких виртуальных машинах.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Область транзакций на двух базах данных в одном экземпляре не поддерживается

Класс `TransactionScope` в .NET не работает, если два запроса отправляются в две базы данных в пределах одного и того же экземпляра в рамках одной и той же области транзакций:

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

Хотя этот код работает с данными в одном и том же экземпляре, он требует MSDTC.

**Обходной путь:** Используйте [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) для использования другой базы данных в контексте соединения вместо двух соединений.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Модули CLR и связанные серверы иногда не могут ссылаться на локальный IP-адрес

Модули CLR, размещенные в управляемом экземпляре и связанные серверы или распределенные запросы, которые ссылаются на текущий экземпляр, иногда не могут решить IP локального экземпляра. Это временная ошибка.

**Обходной путь:** По возможности используйте контекстные соединения в модуле CLR.

## <a name="updates"></a>Обновления

Для получения списка обновлений и [SQL Database service updates](https://azure.microsoft.com/updates/?product=sql-database)улучшений базы данных S'L см.

Для обновления и улучшений всех [Service updates](https://azure.microsoft.com/updates)служб Azure см.

## <a name="contribute-to-content"></a>Участие в разработке документации

Чтобы внести свой вклад в документацию [Docs Contributor Guide](https://docs.microsoft.com/contribute/)базы данных Azure S'L, см.
