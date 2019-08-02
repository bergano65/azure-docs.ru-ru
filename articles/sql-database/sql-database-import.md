---
title: Импорт BACPAC-файла для создания базы данных SQL Azure | Документация Майкрософт
description: Создайте базу данных SQL Azure, импортировав BACPAC-файл.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: ca098eba8e0cbad0d0240bd7819a401c502a869d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568028"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Краткое руководство. Импорт BACPAC-файла в базу данных SQL Azure

С помощью файла [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) вы можете импортировать базу данных SQL Server в Базу данных SQL Azure. Вы можете импортировать данные из файла `BACPAC`, которые хранятся в хранилище BLOB-объектов Azure (только цен. категория "Стандартный"), или из локального хранилища в локальное расположение. Чтобы развернуть скорость импорта, предоставив дополнительные ресурсы и ускоренную скорость доступа к ним, масштабируйте базу данных до более высокого уровня службы и объема вычислительных ресурсов во время процесса импорта. После завершения импорта эти значения можно уменьшить.

> [!NOTE]
> Уровень совместимости импортированной базы данных основан на уровне совместимости базы данных-источника.
> [!IMPORTANT]
> После импорта базы данных вы можете работать с ней при текущем уровне совместимости (уровень 100 для базы данных AdventureWorks2008R2) или при более высоком уровне. Дополнительные сведения о последствиях и параметрах работы базы данных с определенным уровнем совместимости см. в разделе [ALTER DATABASE (Transact-SQL) Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Уровень совместимости ALTER DATABASE (Transact-SQL)). Кроме того, в статье [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) вы можете получить сведения о дополнительных настройках уровня базы данных, связанных с уровнем совместимости.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Импорт из BACPAC-файла с помощью портала Azure

[Портал Azure](https://portal.azure.com) поддерживает создание *только* отдельной базы данных в Базе данных SQL Azure и *только* из файла BACPAC, который хранится в хранилище BLOB-объектов Azure.

Миграция базы данных в [управляемый экземпляр](sql-database-managed-instance.md) из BACPAC-файла с помощью Azure PowerShell в настоящее время не поддерживается. Вместо этого используйте SQL Server Management Studio или SQLPackage.

> [!NOTE]
> Компьютеры, обрабатывающие запросы на импорт и экспорт, отправленные с помощью портал Azure или PowerShell, должны хранить BACPAC-файл, а также временные файлы, созданные платформой приложения уровня данных (DacFX). Необходимое дисковое пространство существенно различается в базах данных с одинаковым размером и может потребовать места на диске в 3 раза больше размера базы данных. Компьютеры, на которых работает запрос на импорт или экспорт, имеют только 450GB место на локальном диске. В результате некоторые запросы могут завершиться `There is not enough space on the disk`ошибкой. В этом случае решение заключается в запуске SqlPackage. exe на компьютере с достаточным местом на локальном диске. Мы советуем использовать [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) для импорта и экспорта баз данных, превышающих 150 ГБ, чтобы избежать этой проблемы.
 
1. Чтобы импортировать из файла BACPAC в новую отдельную базу данных с помощью портала Azure, откройте требуемую страницу сервера базы данных, а затем выберите **Импорт базы данных** на панели инструментов.  

   ![База данных import1](./media/sql-database-import/import1.png)

2. Сначала выберите учетную запись хранения и контейнер для файла BACPAC, а затем — сам файл BACPAC из которого необходимо выполнить импорт.
3. Укажите размер новой базы данных (обычно он такой же, как и у источника) и учетные данные для целевого сервера SQL Server. Список значений, доступных для новой базы данных SQL Azure, см. в статье [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![База данных import2](./media/sql-database-import/import2.png)

4. Нажмите кнопку **ОК**.

5. Чтобы отслеживать ход выполнения импорта, откройте страницу сервера базы данных. В разделе **Параметры** выберите **Журнал импорта и экспорта**. При успешном выполнении импорта отображается состояние **Завершено**.

   ![Состояние импорта базы данных](./media/sql-database-import/import-status.png)

6. Выберите **Базы данных SQL**, чтобы проверить активность базы данных на сервере базы данных, и убедитесь, что для новой базы данных задано состояние **В сети**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Импорт из BACPAC-файла с помощью SqlPackage

Импорт базы данных SQL Server с помощью программы командной строки [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) описывается в разделе [Параметры и свойства импорта](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). В состав SqlPackage входят последние версии [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, новую версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) можно скачать в Центре загрузки Майкрософт.

Вместо портала Azure рекомендуется использовать SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в Базу данных SQL Azure с помощью файлов `BACPAC` см. в блоге команды Помощника по Azure для пользователей SQL Server [здесь](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Рекомендуем использовать SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в Базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Приведенная ниже команда SqlPackage импортирует базу данных **AdventureWorks2008R2** из локального хранилища на сервер Базы данных SQL Azure, который называется **mynewserver20170403**. Эта команда создает базу данных **myMigratedDatabase** с уровнем служб **Премиум** и целевым уровнем служб **P6**. Подставьте соответствующие значения для своей среды.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Чтобы подключиться к серверу Базы данных SQL, управляя отдельной базой данных при включенном корпоративном брандмауэре, необходимо открыть порт 1433 в брандмауэре. Чтобы подключиться к управляемому экземпляру, необходимо обладать [подключением типа "точка — сеть"](sql-database-managed-instance-configure-p2s.md) или подключением Express Route.
>

В этом примере показано, как импортировать базу данных с помощью SqlPackage, используя универсальную проверку подлинности Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Импорт в отдельную базу данных из файла BACPAC с помощью PowerShell

> [!NOTE]
> В настоящее время [управляемый экземпляр](sql-database-managed-instance.md) не поддерживает миграцию базы данных в экземпляр базы данных из BACPAC-файла с помощью Azure PowerShell. Чтобы импортировать в управляемый экземпляр, используйте SQL Server Management Studio или SQLPackage.

> [!NOTE]
> Компьютеры, обрабатывающие запросы на импорт и экспорт, отправленные с помощью портала или PowerShell, должны хранить BACPAC-файл, а также временные файлы, созданные платформой приложения уровня данных (DacFX). Требуемое дисковое пространство значительно различается в баз данных с одинаковым размером и может занять до 3 раз из размера базы данных. Компьютеры, на которых работает запрос на импорт или экспорт, имеют только 450GB место на локальном диске. В результате некоторые запросы могут завершиться ошибкой "на диске недостаточно места". В этом случае решение заключается в запуске SqlPackage. exe на компьютере с достаточным местом на локальном диске. При импорте или экспорте баз данных, размер которых превышает 150 ГБ, используйте [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) , чтобы избежать этой проблемы.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны.

Используйте командлет [New-азсклдатабасеимпорт](/powershell/module/az.sql/new-azsqldatabaseimport) , чтобы отправить запрос на импорт базы данных в службу базы данных SQL Azure. Операция импорта может занять некоторое время в зависимости от размера базы данных.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Для проверки хода выполнения импорта можно использовать командлет [Get-азсклдатабасеимпортекспортстатус](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Если выполнить его немедленно после запроса, то обычно возвращается сообщение **Состояние: выполняется**. Отображение сообщения **Состояние: выполнен** означает, что импорт завершен.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Еще один пример сценария приведен в разделе [Импорт базы данных из BACPAC-файла](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Ограничения

Импорт базы данных в эластичный пул не поддерживается. Однако вы можете сначала выполнить импорт данных в отдельную базу данных, а затем переместить эту базу данных в эластичный пул.

## <a name="import-using-wizards"></a>Импорт с помощью мастера

Импорт также можно выполнить с помощью следующих мастеров:

- [мастер импорта приложения уровня данных в SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard);
- [мастер импорта и экспорта SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Следующие шаги

- Чтобы научиться подключаться к импортированной Базе данных SQL и отправлять к ней запросы, ознакомьтесь со статьей [Краткое руководство. Подключение к базе данных SQL Azure и создание запросов к ней с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md).
- Сведения о миграции из SQL Server в Базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-single-database-migrate.md).
- Чтобы узнать, как безопасно управлять ключами к хранилищу данных и подписанными URL-адресами и совместно их использовать, ознакомьтесь с [руководством по безопасности службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
