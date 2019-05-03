---
title: Настройка репликации в базе данных управляемого экземпляра базы данных SQL Azure | Документация Майкрософт
description: Дополнительные сведения о настройке репликации транзакций в базе данных управляемого экземпляра Базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: c72c4d21f948d6d6c4d1d4598efa0e13de9705a6
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926198"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Настройка репликации в базе данных управляемого экземпляра Базы данных SQL Azure

Репликация транзакций позволяет реплицировать данные в базу данных управляемого экземпляра Базы данных SQL Azure из базы данных SQL Server или другой базы данных экземпляра. 

Также можно принудительно отправить изменения, внесенные в базу данных экземпляра в управляемый экземпляр базы данных SQL Azure для с помощью репликации транзакций:

- База данных SQL Server.
- Отдельной базы данных в базе данных SQL Azure.
- Базы данных в пул эластичных баз данных базы данных SQL Azure.
 
Репликация транзакций находится в общедоступной предварительной версии на [базы данных SQL управляемого экземпляра](sql-database-managed-instance.md). В управляемом экземпляре может размещаться база данных издателя, распространителя и подписчика. Сведения о доступных конфигурациях см. [здесь](sql-database-managed-instance-transactional-replication.md#common-configurations).

  > [!NOTE]
  > Эта статья предназначена для пользователя при настройке репликации с базой данных Azure управляемый экземпляр на от начала до конца, начиная с создания группы ресурсов. Если вы уже существует управляемый развернутых экземпляров, перейти к шагу [шаг 4](#4---create-a-publisher-database) Создание базы данных издателя, или [шаг 6](#6---configure-distribution) Если уже имеется база данных издателя и подписчика и все готово для начала Настройка репликации.  

## <a name="requirements"></a>Требования

Для настройки управляемого экземпляра в качестве издателя и/или распространителя необходимо:

- Чтобы сейчас управляемый экземпляр не был связан с георепликацией.
- Что издателем управляемый экземпляр находится в той же виртуальной сети, что распространителем и подписчиком, или [пиринг](../virtual-network/tutorial-connect-virtual-networks-powershell.md) было установлено между виртуальными сетями из всех трех сущностей. 
- При подключении используется аутентификация SQL между участниками репликации.
- Общий ресурс учетной записи хранения Azure для рабочей папки репликации.
- В правилах безопасности группы безопасности сети для управляемых экземпляров, для доступа к файловым ресурсом Azure открыт порт 445 (исходящий TCP-ПОРТ). 


 > [!NOTE]
 > Отдельные базы данных и базы данных в пуле в Базе данных SQL Azure могут быть только подписчиками. 


## <a name="features"></a>Функции

Поддерживает:

- Сочетание транзакционной репликации и репликации моментальных снимков локального и управляемого экземпляров SQL Server в Базе данных SQL Azure.
- Подписчики могут находиться в локальные базы данных SQL Server, одной базы данных и управляемых экземпляров в базе данных SQL Azure или в составе пула баз данных в эластичные пулы базы данных SQL Azure.
- Односторонняя или двухсторонняя репликация.

Управляемый экземпляр в Базе данных SQL Azure не поддерживает следующие функции:

- [Обновляемые подписки](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Активная георепликация](sql-database-active-geo-replication.md) и [группы автоматической отработки отказа](sql-database-auto-failover-group.md) не следует использовать при настройке репликации транзакций.
 
## <a name="1---create-a-resource-group"></a>1 - Создание группы ресурсов

Используйте [портала Azure](https://portal.azure.com) создать группу ресурсов с именем `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 - Создание управляемых экземпляров

Используйте [портала Azure](https://portal.azure.com) для создания двух [управляемые экземпляры](sql-database-managed-instance-create-tutorial-portal.md) на той же виртуальной сети и подсети. Два управляемых экземплярах должна называться:

- `sql-mi-pub`
- `sql-mi-sub`

Также необходимо будет [Настройка виртуальной Машины Azure для подключения](sql-database-managed-instance-configure-vm.md) к базе данных SQL Azure управляемые экземпляры. 

## <a name="3---create-azure-storage-account"></a>3 - Создание учетной записи хранения Azure

[Создайте учетную запись хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) для рабочего каталога, а затем создать [общую](../storage/files/storage-how-to-create-file-share.md) из учетной записи хранения. 

Скопируйте путь к общей папке в формате: `\\storage-account-name.file.core.windows.net\file-share-name`

Скопируйте ключи доступа к хранилищу в формате: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Дополнительные сведения см. в разделе [Просмотр и копирование ключей доступа к хранилищу](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4 - Создание базы данных издателя

Подключиться к вашей `sql-mi-pub` управляемый экземпляр с помощью SQL Server Management Studio и выполните следующий код Transact-SQL (T-SQL) для создания базы данных издателя:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - Создание базы данных подписчика

Подключиться к вашей `sql-mi-sub` управляемый экземпляр с помощью SQL Server Management Studio и выполните следующий код T-SQL для создания пустой подписчика базы данных:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - Настройка распространения

Подключиться к вашей `sql-mi-pub` управляемый экземпляр с помощью SQL Server Management Studio и выполните следующий код T-SQL для настройки вашей базы данных распространителя. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Настройка издателя для использования распространителя 

Издателя управляемый экземпляр `sql-mi-pub`, выполнение запроса на изменение [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) режим и выполните следующий код, чтобы зарегистрировать новый распространителя с издателем. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)';
```

Этот скрипт настраивает локальный издатель в управляемом экземпляре, добавляет связанный сервер и создает набор заданий агента SQL Server. 

## <a name="8---create-publication-and-subscriber"></a>8 - Создание публикации и подписчика

С помощью [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) режим, выполните следующий сценарий T-SQL для включения репликации для базы данных и настроить репликацию между издателя, распространителя и подписчика. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – изменение параметров агента

Управляемый экземпляр базы данных SQL Azure в настоящее время возникли некоторые проблемы серверной части с подключением с агентами репликации. Хотя эта проблема не будет к которому выполняется обращение обращаться, решение, чтобы увеличить значение времени ожидания входа для агентов репликации. 

Выполните следующую команду T-SQL на издателе, чтобы увеличить время ожидания входа: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Выполните следующую команду T-SQL еще раз, чтобы снова установить время ожидания входа по умолчанию, если для вас для этого:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Перезапустите все три агента, чтобы применить эти изменения. 

## <a name="10---test-replication"></a>10 - тестовая репликация

После настройки репликации можно проверить его путем вставки новых элементов на издателе и просмотре затем изменения распространяются на подписчик. 

Выполните приведенный ниже фрагмент T-SQL, чтобы просмотреть строки на подписчике.

```sql
select * from dbo.ReplTest
```

Запуск в следующем фрагменте T-SQL, чтобы вставить дополнительные строки на издателе и проверка строк на подписчике. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить публикацию, выполните следующую команду T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Чтобы удалить параметр репликации базы данных, выполните следующую команду T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Чтобы отключить публикацию и распространение, выполните следующую команду T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Можно выполнить очистку ресурсов Azure с [Удаление управляемого экземпляра ресурсов из группы ресурсов](../azure-resource-manager/manage-resources-portal.md#delete-resources) и затем удалить группу ресурсов `SQLMI-Repl`. 

   
## <a name="see-also"></a>См. также

- [Репликация транзакций](sql-database-managed-instance-transactional-replication.md)
- [Общие сведения об Управляемом экземпляре](sql-database-managed-instance.md)
