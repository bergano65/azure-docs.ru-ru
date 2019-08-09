---
title: Настройка репликации в базе данных управляемого экземпляра базы данных SQL Azure | Документация Майкрософт
description: Дополнительные сведения о настройке репликации транзакций в базе данных Управляемого экземпляра Базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 3b76dc546b46718378d9b22ad80e17849eaf532d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884082"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Настройка репликации в базе данных Управляемого экземпляра Базы данных SQL Azure

Репликация транзакций позволяет реплицировать данные в базу данных Управляемого экземпляра Базы данных SQL Azure из базы данных SQL Server или другой базы данных экземпляра. 

Можно также использовать репликацию транзакций для отправки изменений, внесенных в базу данных экземпляра в управляемом экземпляре базы данных SQL Azure, в:

- База данных SQL Server.
- Отдельная база данных в базе данных SQL Azure.
- База данных poold в эластичном пуле базы данных SQL Azure.
 
Репликация транзакций доступна в общедоступной предварительной версии [управляемого экземпляра базы данных SQL Azure](sql-database-managed-instance.md). В управляемом экземпляре может размещаться база данных издателя, распространителя и подписчика. Сведения о доступных конфигурациях см. [здесь](sql-database-managed-instance-transactional-replication.md#common-configurations).

  > [!NOTE]
  > В этой статье описано, как настроить репликацию с помощью управляемого экземпляра базы данных Azure с начала до конца, начиная с создания группы ресурсов. Если вы уже развернули управляемые экземпляры, перейдите к [шагу 4](#4---create-a-publisher-database) , чтобы создать базу данных издателя, или [Шаг 6](#6---configure-distribution) , если у вас уже есть база данных издателя и подписчика, и все готово для начала настройки репликации.  

## <a name="requirements"></a>Требования

Настройка управляемого экземпляра для работы в качестве издателя и (или) распространителя требует:

- Чтобы сейчас управляемый экземпляр не был связан с георепликацией.
- Управляемый издателем экземпляр находится в той же виртуальной сети, что и распространитель и подписчик, либо между виртуальными сетями всех трех сущностей установлен [пиринг](../virtual-network/tutorial-connect-virtual-networks-powershell.md) виртуальных сетей. 
- При подключении используется аутентификация SQL между участниками репликации.
- Общий ресурс учетной записи хранения Azure для рабочей папки репликации.
- Порт 445 (исходящий трафик TCP) открыт в правилах безопасности NSG для доступа управляемых экземпляров к файловому ресурсу Azure. 


 > [!NOTE]
 > Отдельные базы данных и базы данных в пуле в Базе данных SQL Azure могут быть только подписчиками. 


## <a name="features"></a>Компоненты

Поддерживает:

- Сочетание транзакционной репликации и репликации моментальных снимков локального и управляемого экземпляров SQL Server в Базе данных SQL Azure.
- Подписчики могут находиться в локальных базах данных SQL Server, отдельных базах данных или управляемых экземплярах в базе данных SQL Azure или в базах данных пула в эластичных пулах базы данных SQL Azure.
- Односторонняя или двухсторонняя репликация.

Управляемый экземпляр в Базе данных SQL Azure не поддерживает следующие функции:

- [Обновляемые подписки](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Активная](sql-database-active-geo-replication.md) Георепликация с репликацией транзакций. Вместо активной георепликации используйте [группы автоматической](sql-database-auto-failover-group.md)отработки отказа, но обратите внимание, что публикацию необходимо [вручную удалить](sql-database-managed-instance-transact-sql-information.md#replication) из основного управляемого экземпляра и создать повторно на дополнительном управляемом экземпляре после отработки отказа.  
 
## <a name="1---create-a-resource-group"></a>1\. Создание группы ресурсов

Используйте [портал Azure](https://portal.azure.com) , чтобы создать группу ресурсов с именем `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2\. Создание управляемых экземпляров

Используйте [портал Azure](https://portal.azure.com) , чтобы создать два [управляемых экземпляра](sql-database-managed-instance-create-tutorial-portal.md) в одной виртуальной сети и подсети. Два управляемых экземпляра должны называться:

- `sql-mi-pub`
- `sql-mi-sub`

Кроме того, необходимо [настроить виртуальную машину Azure для подключения](sql-database-managed-instance-configure-vm.md) к управляемым экземплярам базы данных SQL Azure. 

## <a name="3---create-azure-storage-account"></a>3\. Создание учетной записи хранения Azure

[Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) для рабочего каталога, а затем создайте общую [папку](../storage/files/storage-how-to-create-file-share.md) в учетной записи хранения. 

Скопируйте путь к общей папке в формате:`\\storage-account-name.file.core.windows.net\file-share-name`

Скопируйте ключи доступа к хранилищу в формате:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Дополнительные сведения см. в разделе [Просмотр и копирование ключей доступа к хранилищу](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4\. Создание базы данных издателя

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

## <a name="5---create-a-subscriber-database"></a>5\. Создание базы данных подписчика

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

## <a name="6---configure-distribution"></a>6\. Настройка распространения

Подключитесь к `sql-mi-pub` управляемому экземпляру с помощью SQL Server Management Studio и выполните следующий код T-SQL для настройки базы данных распространителя. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7\. Настройка издателя для использования распространителя 

На управляемом экземпляре `sql-mi-pub`издателя измените выполнение запроса на режим [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) и выполните следующий код, чтобы зарегистрировать новый распространитель на издателе. 

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
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

Этот сценарий настраивает локальный издатель на управляемом экземпляре, добавляет связанный сервер и создает набор заданий для агент SQL Server. 

## <a name="8---create-publication-and-subscriber"></a>8\. Создание публикации и подписчика

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

## <a name="9---modify-agent-parameters"></a>9\. изменение параметров агента

В настоящее время управляемый экземпляр базы данных SQL Azure испытывает некоторые внутренние проблемы с подключением к агентам репликации. Хотя эта проблема решена, можно устранить проблему, чтобы увеличить значение времени ожидания входа для агентов репликации. 

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

После настройки репликации можно протестировать ее, вставив новые элементы на издателе и просмотрев изменения, распространяемые на подписчик. 

Выполните следующий фрагмент кода T-SQL для просмотра строк на подписчике:

```sql
select * from dbo.ReplTest
```

Выполните следующий фрагмент кода T-SQL, чтобы вставить дополнительные строки на издателе, а затем снова проверьте строки на подписчике. 

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

Вы можете очистить ресурсы Azure, [удалив ресурсы управляемого экземпляра из группы ресурсов](../azure-resource-manager/manage-resources-portal.md#delete-resources) , а затем удалив группу `SQLMI-Repl`ресурсов. 

   
## <a name="see-also"></a>См. также

- [Репликация транзакций](sql-database-managed-instance-transactional-replication.md)
- [Общие сведения об Управляемом экземпляре](sql-database-managed-instance.md)
