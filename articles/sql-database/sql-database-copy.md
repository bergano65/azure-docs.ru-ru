---
title: Копирование базы данных
description: Создание транзакционно согласованной копии существующей базы данных SQL Azure на том же или на другом сервере.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: b3bc99d0fbdb551af0fb3711d74db537d3f9b1a5
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421342"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Копирование транзакционно согласованной копии базы данных Azure SQL

Azure SQL Database provides several methods for creating a transactionally consistent copy of an existing Azure SQL database ([single database](sql-database-single-database.md)) on either the same server or a different server. Базу данных SQL можно копировать с помощью портала Azure, PowerShell или T-SQL.

## <a name="overview"></a>Краткое описание

Копия базы данных представляет собой моментальный снимок исходной базы данных на момент запроса копирования. You can select the same server or a different server. Also you can choose to keep its service tier and compute size, or use a different compute size within the same service tier (edition). После завершения копирования копия становится полностью работоспособной и независимой базой данных. На этом этапе можно перейти на использование любой более поздней или более ранней версии. Именами входа, пользователями и разрешениями можно управлять независимо. The copy is created using the geo-replication technology and once seeding is completed the geo-replication link is automatically terminated. All the requirements for using geo-replication apply to the database copy operation. See [Active geo-replication overview](sql-database-active-geo-replication.md) for details.

> [!NOTE]
> [Создаваемые автоматически резервные копии базы данных](sql-database-automated-backups.md) используются при создании копии базы данных.

## <a name="logins-in-the-database-copy"></a>Имена входа в копии базы данных

При копировании базы данных на тот же сервер Базы данных SQL можете использовать для обеих баз данных одинаковые имена входа. Субъект безопасности, используемый для копирования базы данных, становится владельцем новой базы данных. В копируемую базу данных копируются все пользователи, их разрешения и идентификаторы безопасности (SID).  

При копировании базы данных на другой сервер Базы данных SQL субъект безопасности на новом сервере становится владельцем новой базы данных. Если вы используете [пользователей автономной базы данных](sql-database-manage-logins.md) для доступа к данным, это гарантирует, что база данных-источник и база данных-получатель всегда имеют одинаковые учетные данные пользователей, поэтому после завершения копирования к копии можно будет немедленно обратиться, используя прежние учетные данные.

Если вы используете [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), то можно полностью исключить потребность в управлении учетными данными в копии. Тем не менее при копировании базы данных на новый сервер доступ по имени для входа может перестать работать, так на новом сервере отсутствуют имена для входа. В статье [Настройка безопасности базы данных SQL Azure и управление ею для геовосстановления или отработки отказа](sql-database-geo-replication-security-config.md) описывается управление именами для входа при копировании базы данных на другой сервер Базы данных SQL.

С момента успешного завершения копирования и вплоть до сопоставления остальных пользователей войти в новую базу данных может только ее владелец, т. е. пользователь, инициировавший копирование. Сведения о разрешении имен для входа после завершения операции копирования см. в разделе [Копирование базы данных SQL Azure с помощью Transact-SQL](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Копирование базы данных на портале Azure

Чтобы скопировать базу данных с помощью портала Azure, откройте страницу базы данных и щелкните **Копировать**.

   ![Копирование базы данных](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Копирование базы данных с помощью PowerShell

To copy a database, use the following examples.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

For PowerShell, use the [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet.

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

The database copy is a asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the the target database using the [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

The database copy is a asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the the target database using the [az sql db delete](/cli/azure/sql/db#az-sql-db-delete) command.

* * *

Полный пример сценария см. в статье [Копирование базы данных SQL на новый сервер с помощью PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC roles to manage database copy

To create a database copy, you will need to be in the following roles

- владельца подписки или
- SQL Server Contributor role or
- Custom role on the source and target databases with following permission:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

To cancel a database copy, you will need to be in the following roles

- владельца подписки или
- SQL Server Contributor role or
- Custom role on the source and target databases with following permission:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

To manage database copy using Azure portal, you will also need the following permissions:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

If you want to see the operations under deployments in the resource group on the portal, operations across multiple resource providers including SQL operations, you will need these additional RBAC roles:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Копирование базы данных с помощью Transact-SQL

Войдите в базу данных master под именем для входа субъекта на уровне сервера или под именем для входа, которое использовалось для создания копируемой базы данных. Для успешного копирования базы данных имена для входа, не принадлежащие субъектам уровня сервера, должны быть членами роли dbmanager. Дополнительные сведения об именах для входа и подключении к серверу см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](sql-database-manage-logins.md).

Начните копирование исходной базы данных с помощью инструкции [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Данный оператор запускает процесс копирования базы данных. Так как копирование базы данных — процесс асинхронный, оператор CREATE DATABASE возвращается до завершения копирования базы данных.

### <a name="copy-a-sql-database-to-the-same-server"></a>Копирование Базы данных SQL на тот же сервер

Войдите в базу данных master под именем для входа субъекта на уровне сервера или под именем для входа, которое использовалось для создания копируемой базы данных. Для успешного копирования базы данных имена для входа, не принадлежащие субъектам уровня сервера, должны быть членами роли dbmanager.

Эта команда копирует Database1 в новую базу данных с именем Database2 на том же сервере. Операция копирования может занять некоторое время в зависимости от размера базы данных.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Копирование Базы данных SQL на другой сервер

Войдите в базу данных master на целевом сервере, т. е. на сервер Базы данных SQL, на котором необходимо создать базу данных. Для входа укажите имя пользователя и пароль владельца базы данных-источник на исходном сервере Базы данных SQL. Имя входа на целевом сервере также должно относиться к роли dbmanager или являться основным именем входа на уровне сервера.

Эта команда копирует Database1 на сервере server1 в новую базу данных с именем Database2 на сервере server2. Операция копирования может занять некоторое время в зависимости от размера базы данных.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Both servers' firewalls must be configured to allow inbound connection from the IP of the client issuing the T-SQL COPY command.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copy a SQL database to a different subscription

You can use the steps described in the previous section to copy your database to a SQL Database server in a different subscription. Make sure you use a login that has the same name and password as the database owner of the source database and it is a member of the dbmanager role or is the server-level principal login. 

> [!NOTE]
> The [Azure portal](https://portal.azure.com) does not support copy to a different subscription because Portal calls the ARM API and it uses the subscription certificates to access both servers involved in geo-replication.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Отслеживание хода операции копирования

Следить за ходом процесса копирования можно в представлениях sys.databases и sys.dm_database_copies. Во время копирования в столбце **state_desc** представления sys.databases для новой базы данных отображается значение **COPYING**.

* Если копирование завершается неудачей, в столбце **state_desc** представления sys.databases для новой базы данных отображается значение **SUSPECT**. Выполните инструкцию DROP для новой базы данных и повторите попытку позднее.
* Если копирование завершается успешно, в столбце **state_desc** представления sys.databases для новой базы данных отображается значение **ONLINE**. Это означает, что копирование завершено и новая база данных является обычной базой данных, которую можно изменять независимо от исходной.

> [!NOTE]
> Чтобы отменить копирование до его завершения, выполните в новой базе данных инструкцию [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx). Кроме того, процесс копирования отменяет также выполнение оператора DROP DATABASE в исходной базе данных.

> [!IMPORTANT]
> If you need to create a copy with a substantially smaller SLO than the source, the target database may not have sufficient resources to complete the seeding process and it can cause the copy operaion to fail. In this scenario use a geo-restore request to create a copy in a different server and/or a different region. See [Recover an Azure SQL database using database backups](sql-database-recovery-using-backups.md#geo-restore) for more informaion.

## <a name="resolve-logins"></a>Разрешение имен для входа

После того как новая база данных на целевом сервере будет в сети, сопоставьте пользователей из новой базы данных с именами входа на целевом сервере с помощью оператора [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx). Сведения о разрешении потерянных пользователей см. в разделе [Диагностика пользователей, утративших связь с учетной записью](https://msdn.microsoft.com/library/ms175475.aspx). Ознакомьтесь также со статьей [Как управлять безопасностью базы данных SQL после аварийного восстановления](sql-database-geo-replication-security-config.md).

Все пользователи в новой базе данных получают такие же разрешения, как и в исходной базе данных. Пользователь, инициировавший копирование базы данных, становится владельцем новой базы данных и получает новый идентификатор безопасности (SID). С момента успешного завершения копирования и вплоть до сопоставления остальных пользователей войти в новую базу данных может только ее владелец, т. е. пользователь, инициировавший копирование.

Дополнительные сведения об управлении пользователями и именами для входа при копировании базы данных на другой сервер Базы данных SQL см. в статье [Настройка безопасности базы данных SQL Azure и управление ею для геовосстановления или отработки отказа](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Ошибки копирования базы данных

При копировании базы данных в базе данных SQL Azure могут возникнуть следующие ошибки. Дополнительные сведения см. в статье [Копирование базы данных SQL Azure](sql-database-copy.md).

| Код ошибки | Серьезность | Описание |
| ---:| ---:|:--- |
| 40635 |16 |Клиент с IP-адресом %.&#x2a;ls временно отключен. |
| 40637 |16 |Возможность создания копии базы данных в настоящее время отключена. |
| 40561 |16 |Не удалось скопировать базу данных. Исходная или целевая база данных не существует. |
| 40562 |16 |Не удалось скопировать базу данных. Исходная база данных удалена. |
| 40563 |16 |Не удалось скопировать базу данных. Целевая база данных удалена. |
| 40564 |16 |Произошел сбой при копировании базы данных из-за внутренней ошибки. Удалите целевую базу данных и повторите попытку. |
| 40565 |16 |Не удалось скопировать базу данных. Допускается не более одной одновременной операции копирования базы данных из одного источника. Удалите целевую базу данных и повторите попытку позднее. |
| 40566 |16 |Произошел сбой при копировании базы данных из-за внутренней ошибки. Удалите целевую базу данных и повторите попытку. |
| 40567 |16 |Произошел сбой при копировании базы данных из-за внутренней ошибки. Удалите целевую базу данных и повторите попытку. |
| 40568 |16 |Не удалось скопировать базу данных. Исходная база данных стала недоступна. Удалите целевую базу данных и повторите попытку. |
| 40569 |16 |Не удалось скопировать базу данных. Целевая база данных стала недоступна. Удалите целевую базу данных и повторите попытку. |
| 40570 |16 |Произошел сбой при копировании базы данных из-за внутренней ошибки. Удалите целевую базу данных и повторите попытку позднее. |
| 40571 |16 |Произошел сбой при копировании базы данных из-за внутренней ошибки. Удалите целевую базу данных и повторите попытку позднее. |

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об именах для входа см. в статьях [Предоставление доступа к базе данных и управление им](sql-database-manage-logins.md) и [Настройка безопасности базы данных SQL Azure и управление ею для геовосстановления или отработки отказа](sql-database-geo-replication-security-config.md).
- Сведения об экспорте базы данных см. в статье [Экспорт базы данных SQL Azure или SQL Server в BACPAC-файл](sql-database-export.md).
