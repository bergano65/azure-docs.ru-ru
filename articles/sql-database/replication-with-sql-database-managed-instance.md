---
title: Настройка репликации в базу данных управляемых экземпляров
description: Научитесь настраивать транзакционную репликацию между издателем/дистрибьютором и абонентом управляемых экземпляров.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299079"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Настройка репликации в базе данных Управляемого экземпляра Базы данных SQL Azure

Репликация транзакций позволяет реплицировать данные в базу данных Управляемого экземпляра Базы данных SQL Azure из базы данных SQL Server или другой базы данных экземпляра. 

В этой статье показано, как настроить репликацию между издателем/дистрибьютором управляемого экземпляра и абонентом управляемого экземпляра. 

![Репликация между двумя управляемыми экземплярами](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Можно также использовать транзакционную репликацию для нажатия изменений, внесенных в базу данных экземпляров в управляемом экземпляре базы данных Azure S'L, чтобы:

- База данных сервера S'L.
- Единая база данных в базе данных Azure S'L.
- Объединенная база данных в эластичном пуле базы данных Azure S'L.
 
Транзакционная репликация находится в открытом предварительном просмотре на [управляемом экземпляре базы данных Azure S'L.](sql-database-managed-instance.md) В управляемом экземпляре может размещаться база данных издателя, распространителя и подписчика. Просмотрите [конфигурации репликации транзакций](sql-database-managed-instance-transactional-replication.md#common-configurations) для доступных конфигураций.

  > [!NOTE]
  > - Эта статья предназначена для руководства пользователем в настройке репликации с управляемым экземпляром базы данных Azure от начала конца до конца, начиная с создания группы ресурсов. Если вы уже успели развернуть экземпляры, перейдите к [шагу 4,](#4---create-a-publisher-database) чтобы создать базу данных издателя, или [Шаг 6,](#6---configure-distribution) если у вас уже есть база данных издателя и абонента, и готовы начать настройку репликации.  
  > - Эта статья настраивает издателя и дистрибьютора на один и тот же управляемый экземпляр. Чтобы разместить дистрибьютора на отдельном экземпляре [Configure replication between an MI publisher and an MI distributor](sql-database-managed-instance-configure-replication-tutorial.md)manged, см. 

## <a name="requirements"></a>Требования

Настройка управляемого экземпляра для работы в качестве издателя и/или дистрибьютора требует:

- То, что управляемый издателем экземпляр находится в той же виртуальной сети, что и дистрибьютор и абонент, или [vNet пиринг](../virtual-network/tutorial-connect-virtual-networks-powershell.md) был создан между виртуальными сетями всех трех сущностей. 
- При подключении используется аутентификация SQL между участниками репликации.
- Общий ресурс учетной записи хранения Azure для рабочей папки репликации.
- Порт 445 (исходящий TCP) открыт в правилах безопасности NSG для управляемых экземпляров для доступа к доле файла Azure.  Если вы столкнулись с ошибкой "не удалось подключиться к лазурной памяти имя учетной записи хранения \<> с ошибкой os 53", вам нужно будет добавить исходящие правила в NSG соответствующего подсети управляемых экземпляров S'L.


 > [!NOTE]
 > Отдельные базы данных и базы данных в пуле в Базе данных SQL Azure могут быть только подписчиками. 


## <a name="features"></a>Компоненты

Поддерживаются следующие возможности:

- Сочетание транзакционной репликации и репликации моментальных снимков локального и управляемого экземпляров SQL Server в Базе данных SQL Azure.
- Абоненты могут находиться в базах данных на территории сервера, отдельных базах данных/управляемых экземплярах в базе данных Azure S'L или объединенных базах данных в эластичных пулах базы данных Azure S'L Database.
- Односторонняя или двухсторонняя репликация.

Управляемый экземпляр в Базе данных SQL Azure не поддерживает следующие функции:

- [Updatable подписки](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Активная георепликация](sql-database-active-geo-replication.md) с транзакционной репликацией. Вместо активной георепликации используйте [группы автоматического](sql-database-auto-failover-group.md)отказа, но обратите внимание, что публикация должна быть [вручную удалена](sql-database-managed-instance-transact-sql-information.md#replication) из основного управляемого экземпляра и воссоздана на вторичном управляемом экземпляре после сбоя.  
 
## <a name="1---create-a-resource-group"></a>1 - Создание группы ресурсов

Используйте [портал Azure](https://portal.azure.com) для создания группы ресурсов с именем. `SQLMI-Repl`  

## <a name="2---create-managed-instances"></a>2 - Создание управляемых экземпляров

Используйте [портал Azure](https://portal.azure.com) для создания двух [управляемых экземпляров](sql-database-managed-instance-create-tutorial-portal.md) в одной виртуальной сети и подсети. Например, назовите два управляемых экземпляра:

- `sql-mi-pub`(наряду с некоторыми символами для рандомизации)
- `sql-mi-sub`(наряду с некоторыми символами для рандомизации)

Также необходимо [настроить VM Azure для подключения](sql-database-managed-instance-configure-vm.md) к управляемым экземплярам базы данных Azure S'L. 

## <a name="3---create-azure-storage-account"></a>3 - Создание учетной записи хранения Azure

[Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) для рабочей папки, а затем создайте в этой учетной записи [общую папку](../storage/files/storage-how-to-create-file-share.md). 

Скопируйте путь к общей папке в формате `\\storage-account-name.file.core.windows.net\file-share-name`.

Например, `\\replstorage.file.core.windows.net\replshare`.

Копирование ключей доступа к хранилищу в формате:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Например, `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`.

См. сведения о том, как [управлять ключами доступа к учетной записи хранения](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 - Создание базы данных издателя

Подключитесь `sql-mi-pub` к управляемому экземпляру с помощью студии управления серверами S'L и запустите следующий код Transact-S'L (T-S'L) для создания базы данных издателя:

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

## <a name="5---create-a-subscriber-database"></a>5 - Создание абонентской базы данных

Подключитесь `sql-mi-sub` к управляемому экземпляру с помощью студии управления серверами S'L и запустите следующий код T-S'L для создания пустой базы данных подписчиков:

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

## <a name="6---configure-distribution"></a>6 - Распределение настройки

Подключитесь `sql-mi-pub` к управляемому экземпляру с помощью студии управления серверами S'L и запустите следующий код T-S'L для настройки базы данных дистрибутива. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Настройка издателя для использования дистрибьютора 

В управляемом `sql-mi-pub`экземпляре издателя измените выполнение запроса в режим [S'LCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) и запустите следующий код для регистрации нового дистрибьютора с вашим издателем. 

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
   > Обязательно используйте только backslashes ()`\`для file_storage параметра. Использование символа прямой косой черты (`/`) может привести к ошибке при подключении к общей папке. 

Этот скрипт настраивает локального издателя на управляемый экземпляр, добавляет связанный сервер и создает набор заданий для серверного агента S'L. 

## <a name="8---create-publication-and-subscriber"></a>8 - Создание публикации и абонента

Используя режим [S'LCMD,](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) запустите следующий скрипт T-S'L, чтобы включить репликацию для вашей базы данных, и настроить репликацию между издателем, дистрибьютором и абонентом. 

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
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Изменение параметров агента

В настоящее время управляемый экземпляр базы данных Azure S'L испытывает некоторые проблемы с бэкэндом с подключением к агентам репликации. В то время как эта проблема решается, обходной путь для увеличения значения тайм-аута входа для агентов репликации. 

Запустите следующую команду T-S'L на издателя, чтобы увеличить тайм-аут входа: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Повторите следующую команду T-S'SL, чтобы установить тайм-аут входа обратно в значение по умолчанию, если это необходимо:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Перезапустите все три агента, чтобы применить эти изменения. 

## <a name="10---test-replication"></a>10 - Репликация тестов

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

Чтобы отказаться от публикации, запустите следующую команду T-S'L:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Чтобы удалить опцию репликации из базы данных, запустите следующую команду T-S'L:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Чтобы отключить публикацию и распространение, запустите следующую команду T-S'L:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Можно очистить ресурсы Azure, [удалив управляемые ресурсы экземпляра из группы ресурсов](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) и затем удалив группу `SQLMI-Repl`ресурсов. 

   
## <a name="see-also"></a>См. также

- [Репликация транзакций](sql-database-managed-instance-transactional-replication.md)
- [Учебник: Настройка транзакционной репликации между издателем MI и абонентом сервера S'L](sql-database-managed-instance-configure-replication-tutorial.md)
- [Общие сведения об Управляемом экземпляре](sql-database-managed-instance.md)
