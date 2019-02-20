---
title: Настройка репликации в базе данных управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: Дополнительные сведения о настройке репликации транзакций в базе данных управляемого экземпляра Базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 038d8c919e68e68f886525a6c78139496edef8e1
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893024"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Настройка репликации в базе данных управляемого экземпляра Базы данных SQL Azure

Репликация транзакций позволяет реплицировать данные в базу данных управляемого экземпляра Базы данных SQL Azure из базы данных SQL Server или другой базы данных экземпляра. Репликацию транзакций вы также можете использовать для отправки изменений, внесенных в базу данных экземпляра в управляемом экземпляре Базы данных SQL Azure, в базу данных SQL Server, в отдельную базу данных в Базе данных SQL Azure, в базу данных в составе пула в эластичном пуле Базы данных SQL Azure. Эта репликация доступна в общедоступной предварительной версии в [управляемом экземпляре Базы данных SQL Azure](sql-database-managed-instance.md). В управляемом экземпляре может размещаться база данных издателя, распространителя и подписчика. Сведения о доступных конфигурациях см. [здесь](sql-database-managed-instance-transactional-replication.md#common-configurations).

## <a name="requirements"></a>Требования

Чтобы настроить управляемый экземпляр для функционирования в качестве издателя или распространителя, необходимо выполнить следующие требования.

- Чтобы сейчас управляемый экземпляр не был связан с георепликацией.

   >[!NOTE]
   >Отдельные базы данных и базы данных в составе пула в Базе данных SQL Azure могут быть только подписчиками.

- Все управляемые экземпляры должны находиться в той же виртуальной сети.

- При подключении используется аутентификация SQL между участниками репликации.

- Общий ресурс учетной записи хранения Azure для рабочей папки репликации.

- Порт 445 (исходящий TCP) должен быть открыт в правилах безопасности подсети управляемого экземпляра для доступа к общей папке Azure.

## <a name="features"></a>Функции

Поддерживает:

- Сочетание транзакционной репликации и репликации моментальных снимков локального и управляемого экземпляров SQL Server в Базе данных SQL Azure.
- Подписчики могут быть в локальных базах данных SQL Server, отдельных локальных базах данных в Базе данных SQL Azure или в базах данных в составе пула в эластичных пулах Базы данных SQL Azure.
- Односторонняя или двухсторонняя репликация.

Управляемый экземпляр в Базе данных SQL Azure не поддерживает следующие функции:

- обновляемые подписки;
- активная георепликация.

## <a name="configure-publishing-and-distribution-example"></a>Настройка примера публикации и распространения

1. На портале [создайте управляемый экземпляр Базы данных SQL Azure](sql-database-managed-instance-create-tutorial-portal.md).
2. [Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) для рабочей папки.

   Обязательно скопируйте ключи к хранилищу данных. Дополнительные сведения см. в статье [Создание учетной записи хранения](../storage/common/storage-account-manage.md#access-keys
).
3. Создайте экземпляр базы данных для издателя.

   В следующих сценариях замените `<Publishing_DB>` именем базы данных экземпляра.

4. Создайте пользователя базы данных с использованием аутентификации SQL для распространителя. Установите надежный пароль.

   В примерах сценариев ниже используйте `<SQL_USER>` и `<PASSWORD>` в качестве имени пользователя и пароля для этой учетной записи базы данных SQL Server.

5. [Установите подключение к Управляемому экземпляру Базы данных SQL](sql-database-connect-query-ssms.md).

6. Выполните следующий запрос, чтобы добавить распространителя и базу данных распространителя.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Чтобы издатель использовал указанную базу данных распространителя, обновите и запустите следующий запрос.

   Замените `<SQL_USER>` и `<PASSWORD>` на имя пользователя и пароль учетной записи SQL Server.

   Замените `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` значением своей учетной записи хранения.  

   Замените `<STORAGE_CONNECTION_STRING>` строкой подключения из вкладки **Ключи доступа** вашей учетной записи хранения Microsoft Azure.

   Обновив следующий запрос, запустите его.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Настройте издателя для репликации.

    В следующем запросе замените `<Publishing_DB>` именем базы данных издателя.

    Замените `<Publication_Name>` именем публикации.

    Замените `<SQL_USER>` и `<PASSWORD>` на имя пользователя и пароль учетной записи SQL Server.

    Обновив запрос, запустите его, чтобы создать публикацию.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Добавьте статью, подписку и агент принудительной подписки.

   Чтобы добавить эти объекты, обновите следующий сценарий.

   - Замените `<Object_Name>` именем объекта публикации.
   - Замените `<Object_Schema>` именем исходной схемы.
   - Замените другие параметры в угловых скобках `<>` значениями, используемыми в предыдущем сценарии.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>См. также

- [Репликация транзакций](sql-database-managed-instance-transactional-replication.md)
- [Общие сведения об Управляемом экземпляре](sql-database-managed-instance.md)
