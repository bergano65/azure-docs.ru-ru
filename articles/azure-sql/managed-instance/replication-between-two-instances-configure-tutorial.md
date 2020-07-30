---
title: Настройка репликации между управляемыми экземплярами
titleSuffix: Azure SQL Managed Instance
description: В этом учебнике описывается настройка репликации транзакций между издателем и распространителем Azure SQL Управляемый экземпляр и подписчиком SQL Управляемый экземпляр.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: cd476d3210263268627541eb40c50048f0eddd1b
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422918"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Учебник. Настройка репликации между двумя управляемыми экземплярами

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Репликация транзакций позволяет реплицировать данные из одной базы данных в другую, размещенную либо в SQL Server, либо в [Azure SQL управляемый экземпляр](sql-managed-instance-paas-overview.md) (общедоступная Предварительная версия). SQL Управляемый экземпляр может быть издателем, распространителем или подписчиком в топологии репликации. Доступные конфигурации см. в разделе [конфигурации репликации транзакций](replication-transactional-overview.md#common-configurations) .

> [!NOTE]
> В этой статье описывается использование [репликации транзакций](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) в Azure SQL управляемый экземпляр. Он не связан с [группами отработки отказа](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group), компонентом управляемый экземпляр Azure SQL, позволяющим создавать полные доступные для чтения реплики отдельных экземпляров. При настройке [репликации транзакций с группами отработки отказа](replication-transactional-overview.md#with-failover-groups)необходимо учитывать дополнительные моменты.

В этом учебнике описывается настройка одного управляемого экземпляра в качестве издателя и распространителя, а также второго управляемого экземпляра в качестве подписчика.  

![Репликация между двумя управляемыми экземплярами](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

  > [!NOTE]
  > - Эта статья предназначена для опытных пользователей в настройке репликации с применением SQL Управляемый экземпляр от End к концу, начиная с создания группы ресурсов. Если вы уже развернули управляемые экземпляры, перейдите к [шагу 4](#4---create-a-publisher-database) , чтобы создать базу данных издателя, или [Шаг 6](#6---configure-distribution) , если у вас уже есть база данных издателя и подписчика, и все готово для начала настройки репликации.  
  > - В этой статье описывается настройка издателя и распространителя в одном управляемом экземпляре. Сведения о размещении распространителя на отдельном управляемом экземпляре см. в руководстве по [настройке репликации транзакций в Azure SQL управляемый экземпляр и SQL Server](replication-two-instances-and-sql-server-configure-tutorial.md). 

## <a name="requirements"></a>Требования

Настройка Управляемый экземпляр SQL для работы в качестве издателя и (или) распространителя требует:

- , Что управляемый экземпляр издателя находится в той же виртуальной сети, что и распространитель, и подписчика, или [пиринг виртуальной сети](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) настроен между виртуальными сетями всех трех сущностей. 
- При подключении используется аутентификация SQL между участниками репликации.
- Общий ресурс учетной записи хранения Azure для рабочего каталога репликации.
- Порт 445 (исходящий трафик TCP) открыт в правилах безопасности NSG для доступа управляемых экземпляров к файловому ресурсу Azure.  При возникновении ошибки необходимо `failed to connect to azure storage \<storage account name> with os error 53` Добавить правило исходящего трафика в NSG подходящей подсети SQL управляемый экземпляр.

## <a name="1---create-a-resource-group"></a>1. Создание группы ресурсов

Используйте [портал Azure](https://portal.azure.com) , чтобы создать группу ресурсов с именем `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2. Создание управляемых экземпляров

Используйте [портал Azure](https://portal.azure.com) , чтобы создать два [управляемых экземпляра SQL](instance-create-quickstart.md) в одной виртуальной сети и подсети. Например, назовите два управляемых экземпляра:

- `sql-mi-pub`(вместе с некоторыми символами для случайного выбора)
- `sql-mi-sub`(вместе с некоторыми символами для случайного выбора)

Кроме того, необходимо [настроить виртуальную машину Azure для подключения](connect-vm-instance-configure.md) к управляемым экземплярам. 

## <a name="3---create-an-azure-storage-account"></a>3. Создание учетной записи хранения Azure

[Создайте учетную запись хранения Azure](/azure/storage/common/storage-create-storage-account#create-a-storage-account) для рабочей папки, а затем создайте в этой учетной записи [общую папку](../../storage/files/storage-how-to-create-file-share.md). 

Скопируйте путь к общей папке в формате `\\storage-account-name.file.core.windows.net\file-share-name`.

Например, `\\replstorage.file.core.windows.net\replshare`.

Скопируйте ключи доступа к хранилищу в формате:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Например, `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`.

См. сведения о том, как [управлять ключами доступа к учетной записи хранения](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4. Создание базы данных издателя

Подключитесь к `sql-mi-pub` управляемому экземпляру с помощью SQL Server Management Studio и выполните следующий код Transact-SQL (T-SQL) для создания базы данных издателя:

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

## <a name="5---create-a-subscriber-database"></a>5. Создание базы данных подписчика

Подключитесь к `sql-mi-sub` управляемому экземпляру с помощью SQL Server Management Studio и выполните следующий код T-SQL, чтобы создать базу данных пустого подписчика:

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

## <a name="6---configure-distribution"></a>6. Настройка распространения

Подключитесь к `sql-mi-pub` управляемому экземпляру с помощью SQL Server Management Studio и выполните следующий код T-SQL для настройки базы данных распространителя.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7. Настройка издателя для использования распространителя

В Управляемый экземпляр издателя SQL `sql-mi-pub` измените выполнение запроса на режим [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) и выполните следующий код, чтобы зарегистрировать новый распространитель на издателе.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Не забудьте использовать в качестве параметра file_storage только символы обратной косой черты ( `\` ). Использование символа прямой косой черты (`/`) может привести к ошибке при подключении к общей папке.

Этот сценарий настраивает локальный издатель на управляемом экземпляре, добавляет связанный сервер и создает набор заданий для агента SQL Server.

## <a name="8---create-publication-and-subscriber"></a>8. Создание публикации и подписчика

Используя режим [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) , выполните следующий скрипт T-SQL, чтобы включить репликацию для базы данных, и настройте репликацию между издателем, распространителем и подписчиком.

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


-- Configure your log reader agent
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
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9. изменение параметров агента

В Azure SQL Управляемый экземпляр в настоящее время возникают некоторые серверные проблемы с подключением к агентам репликации. Хотя эта проблема решена, решение заключается в увеличении значения времени ожидания входа для агентов репликации.

Выполните следующую команду T-SQL на издателе, чтобы увеличить время ожидания входа:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Выполните следующую команду T-SQL еще раз, чтобы задать для времени ожидания входа значение по умолчанию, если это необходимо сделать:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Перезапустите все три агента, чтобы применить эти изменения.

## <a name="10---test-replication"></a>10. Тестирование репликации

Настроенную репликацию можно протестировать, добавив новые элементы в издатель и убедившись, что изменения распространяются на подписчик.

Выполните следующий фрагмент кода T-SQL, чтобы просмотреть строки в подписчике:

```sql
select * from dbo.ReplTest
```

Выполните следующий фрагмент кода T-SQL, чтобы вставить в издатель дополнительные строки, а затем снова проверьте строки в подписчике.

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

Чтобы удалить параметр репликации из базы данных, выполните следующую команду T-SQL:

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

Вы можете очистить ресурсы Azure, [удалив ресурсы SQL управляемый экземпляр из группы ресурсов](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) , а затем удалив группу ресурсов `SQLMI-Repl` . 

## <a name="next-steps"></a>Дальнейшие действия

Кроме того, вы можете получить дополнительные сведения о [репликации транзакций с помощью Azure sql управляемый экземпляр](replication-transactional-overview.md) или настроить репликацию между [издателем и распространителем SQL управляемый экземпляр и подписчиком SQL на виртуальной машине Azure](replication-two-instances-and-sql-server-configure-tutorial.md). 
