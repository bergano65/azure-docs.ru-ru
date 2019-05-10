---
title: Статья о известных, устранение неполадок распространенные проблемы и ошибки, связанные с помощью Azure Database Migration Service | Документация Майкрософт
description: Узнайте о том, как устранять распространенные известные проблемы/ошибки, связанные с помощью Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/09/2019
ms.openlocfilehash: 7b470c20397aac456d34d5e3b877c7d4126d8279
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465107"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Устранение распространенных ошибок и проблем Azure Database Migration Service

В этой статье описаны некоторые распространенные проблемы и ошибки, которые могут сталкиваться пользователям Azure Database Migration Service. В статье также содержатся сведения об устранении этих проблем и ошибок.

## <a name="migration-activity-in-queued-state"></a>Действие миграции в состоянии очереди

При создании новых действий над проектом Azure Database Migration Service, действия остаются в очереди, не ограничено.

| Причина:         | Способы устранения: |
| ------------- | ------------- |
| Эта проблема возникает при достижении максимальной емкости для текущих задач, которые одновременно запускать экземпляр Azure Database Migration Service. Любое новое действие помещается в очередь, пока не станет доступной емкости. | Проверки Data Migration Service, экземпляр имеет выполнение действий по проектам. Вы можете создавать новые действия, которые автоматически добавляются в очередь для выполнения. Любой существующий выполнения операции завершения, запускается следующее действие в очереди и ее состояние изменяется на рабочем состоянии автоматически. Не нужно предпринимать никаких дополнительных действий, чтобы начать миграцию действия в очереди.<br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Максимальное количество баз данных, выбранных для миграции

При создании действия для проекта миграции базы данных для перемещения базы данных SQL Azure или базе данных SQL управляемого экземпляра, возникает следующая ошибка:

* **Ошибка**: Ошибка при проверке параметров миграции», «errorDetail»: «более чем максимальное число "4" объекты «Базы данных» выбрана для миграции.»

| Причина:         | Способы устранения: |
| ------------- | ------------- |
| Эта ошибка отображается, если бы вы выбрали более чем четырех баз данных для действия миграций. В настоящее время каждое действие миграции ограничен четырех баз данных. | Выберите четыре или меньшее число баз данных в действие миграции. Если вам нужно выполнить миграцию до четырех баз данных в параллельном режиме, подготовьте другого экземпляра Azure Database Migration Service. В настоящее время каждая подписка поддерживает до двух экземпляров Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Ошибки для миграции MySQL в Azure MySQL со сбоями восстановления

При миграции из MySQL в базу данных Azure для MySQL с помощью Azure Database Migration Service, действие миграции завершается сбоем со следующей ошибкой:

* **Ошибка**: Ошибка: Ошибка миграции баз данных — задача «TaskID» было приостановлено из-за сбоев последовательных восстановления [n].

| Причина:         | Способы устранения: |
| ------------- | ------------- |
| Эта ошибка может возникать, когда пользователь, выполняющий миграцию отсутствует роль ReplicationAdmin и/или привилегии REPLICATION CLIENT, системе репликации и SUPER (версиях до MySQL 5.6.6).<br> <br><br><br> <br> <br> <br> <br> <br> <br> | Убедитесь, что [предварительных требований привилегии](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) для пользователя учетной записи настраиваются точно в экземпляре Azure MySQL. Например чтобы создать пользователя с именем «migrateuser» необходимый набор прав, применимы следующие действия:<br>1. CREATE USER migrateuser@'% "ОПРЕДЕЛЕНЫ, «secret»; <br>2. предоставьте все права доступа на db_name.* для migrateuser'@'% идентифицируется «secret»; Повторите этот шаг, чтобы предоставить доступ на несколько баз данных <br>3. Предоставление репликации slave *.* Чтобы migrateuser'@'% идентифицируется «secret»;<br>4. Предоставление репликации клиента на *.* Чтобы migrateuser'@'% идентифицируется «secret»;<br>5. Очистка права доступа; |

## <a name="error-when-attempting-to-stop-the-azure-database-migration-service-instance"></a>Ошибка при попытке остановки экземпляра Azure Database Migration Service

При остановке экземпляра Azure Database Migration Service, вы получите следующую ошибку:

* **Ошибка**: Не удалось остановить службу. Ошибка: {«error»: {«code»: «InvalidRequest», «сообщение»: "одно или несколько действий, выполняющихся в данный момент. Чтобы остановить службу, подождите, пока действия их завершения или остановите их вручную и повторите попытку. "}}

| Причина:         | Способы устранения: |
| ------------- | ------------- |
| Эта ошибка отображается, когда экземпляр службы, которую вы пытаетесь остановить включает в себя действия, которые по-прежнему работают или представить в проектах миграции. <br><br><br><br><br><br> | Убедитесь, что нет действий, выполняемых в экземпляре Azure Database Migration Service требуется остановить. Прежде чем пытаться остановить службу, может также удалить действий или проектов. Удаление проектов для очистки экземпляра службы миграции, удалив все выполняемые задачи сделайте следующее:<br>1. Install-Module-Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. SELECT-AzureRmSubscription - SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject-имя <projectName> - ResourceGroupName <rgName> - ServiceName <serviceName> - DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-from-sql-server-to-an-azure-sql-database-managed-instance"></a>Ошибка при восстановлении базы данных, при миграции из SQL Server к базе данных SQL управляемого экземпляра

При выполнении оперативная миграция из SQL Server в управляемый экземпляр базы данных SQL Azure, прямая миграция завершается сбоем со следующей ошибкой:

* **Ошибка**: Сбой восстановления, для операции идентификатор «operationId». Код «AuthorizationFailed», сообщение "клиент «clientId» с идентификатором объекта «objectId» не авторизован для выполнения действия «Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read» в области" /subscriptions/ идентификатор подписки ".".

| Причина:         | Способы устранения:    |
| ------------- | ------------- |
| Эта ошибка указывает, субъекта-приложения, которое используется для сети миграции из SQL Server в управляемый экземпляр базы данных SQL Azure не имеют разрешение «участие» для подписки. Некоторые вызовы API с управляемым экземпляром в настоящее время требуется это разрешение в подписке для операции восстановления. <br><br><br><br><br><br><br><br><br><br> | Используйте `Get-AzureADServicePrincipal` командлет PowerShell с `-ObjectId` доступны из сообщения об ошибке, чтобы получить список отображаемое имя используется идентификатор приложения.<br><br> Проверьте разрешения для этого приложения и гарантировать [роль "участник"](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) на уровне подписки. <br><br> Команды разработчиков службы миграции базы данных, Azure работает для ограничения требуемый доступ из текущих contribute роли в подписке. Если у вас есть бизнес-требование, которое не допускает использование участие роли, обратитесь в службу поддержки Azure для получения дополнительной поддержки. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Ошибка при удалении сетевой КАРТЫ, связанной с Azure Database Migration Service

При попытке удалить сетевой адаптер, связанный с Azure Database Migration Service, попытка удаления завершается сбоем со следующей ошибкой:

* **Ошибка**: Не удается удалить сетевого Адаптера, связанного в Azure Database Migration Service из-за службу DMS, используя сетевой КАРТЫ

| Причина:         | Способы устранения:    |
| ------------- | ------------- |
| Эта проблема возникает, когда экземпляр Azure Database Migration Service может по-прежнему присутствовать и использование сетевого адаптера. <br><br><br><br><br><br> | Чтобы удалить этот сетевой Адаптер, удалите экземпляр службы DMS, автоматически удаляет сетевой Адаптер, используемый службой.<br><br> **Важно!** Убедитесь, что удаляемый экземпляр Azure Database Migration Service имеет не выполняются действия.<br><br> После удаления всех проектов и действий, связанные с экземпляром Azure Database Migration Service можно удалить экземпляр службы. Сетевой Адаптер, используемый экземпляром службы автоматически очищаются в процессе удаления службы. |

## <a name="connection-error-when-using-expressroute"></a>Ошибка подключения при использовании ExpressRoute

При попытке подключения к источнику в мастере проектов Azure Database Migration service, подключение завершается после длительного времени ожидания, если источник использует ExpressRoute для подключения к.

| Причина:         | Способы устранения:    |
| ------------- | ------------- |
| При использовании [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [требует](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) подготовки три конечные точки службы в подсети виртуальной сети, связанные со службой:<br> --Конечная точка служебной шины<br> --Конечная точка хранилища<br> --Целевой конечной точки базы данных (например, конечная точка SQL, конечная точка Cosmos DB)<br><br><br><br> | [Включить](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) конечные точки службы, необходимые для подключения ExpressRoute между источником и Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-database-for-mysql"></a>Ошибка времени ожидания при переносе базы данных MySQL в базу данных Azure для MySQL

При переносе базы данных MySQL в базу данных Azure для экземпляра MySQL с помощью Azure Database Migration Service, происходит сбой со следующей ошибкой времени ожидания:

    * **Ошибка**: Ошибка: Ошибка миграции баз данных - не удалось загрузить файл - не удалось запустить процесс загрузки для файла "n" RetCode: SQL_ERROR SqlState: HY000 NativeError: Сообщения о 1205: [MySQL] [ODBC Driver] [mysqld] блокировки время ожидания превышено; Попробуйте перезапустить транзакции

| Причина:         | Способы устранения:    |
| ------------- | ------------- |
| Эта ошибка возникает при сбое миграции из-за времени ожидания блокировки ожидания во время миграции.<br><br> | Попробуйте увеличить значение параметра сервера **«innodb_lock_wait_timeout»**. Наибольшее допустимое значение равно 1073741824. |

## <a name="additional-known-issues"></a>Другие известные проблемы

* [Известные проблемы и ограничения при сетевых миграциях в Базу данных SQL Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Известные проблемы и переноса ограничения, связанные с интерактивной миграции к базе данных Azure для MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Известные проблемы и переноса ограничения, связанные с интерактивной миграции к базе данных Azure для PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [PowerShell службы миграции базы данных Azure](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)
* [Как настроить параметры сервера в базе данных Azure для MySQL с помощью портала Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* [Предварительные требования для с помощью Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
* [Часто задаваемые вопросы о службе Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq)
