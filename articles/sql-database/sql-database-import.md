---
title: Импорт BACPAC-файла для создания базы данных SQL Azure | Документация Майкрософт
description: Создайте базу данных SQL Azure, импортировав BACPAC-файл.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 754f2845911307cdd698bff4aa3e891f5c1bcdbd
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234795"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Краткое руководство. Импорт BACPAC-файла в Базу данных SQL Azure

С помощью файла [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) вы можете импортировать базу данных SQL Server в Базу данных SQL Azure. Вы можете импортировать данные из файла `BACPAC`, которые хранятся в хранилище BLOB-объектов Azure (только цен. категория "Стандартный"), или из локального хранилища в локальное расположение. Чтобы развернуть скорость импорта, предоставив дополнительные ресурсы и ускоренную скорость доступа к ним, масштабируйте базу данных до более высокого уровня службы и вычислите размер во время процесса импорта. После завершения импорта эти значения можно уменьшить. 

> [!NOTE]
> Уровень совместимости импортированной базы данных основан на уровне совместимости базы данных-источника.
> [!IMPORTANT]
> После импорта базы данных вы можете работать с ней при текущем уровне совместимости (уровень 100 для базы данных AdventureWorks2008R2) или при более высоком уровне. Дополнительные сведения о последствиях и параметрах работы базы данных с определенным уровнем совместимости см. в разделе [ALTER DATABASE (Transact-SQL) Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Уровень совместимости ALTER DATABASE (Transact-SQL)). Кроме того, в статье [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) вы можете получить сведения о дополнительных настройках уровня базы данных, связанных с уровнем совместимости.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Импорт из BACPAC-файла с помощью портала Azure

Этот раздел содержит указания по созданию на [портале Azure](https://portal.azure.com) базы данных SQL Azure из файла `BACPAC`, расположенного в хранилище BLOB-объектов Azure. Портал поддерживает *только* импорт BACPAC-файла из хранилища BLOB-объектов Azure.

> [!NOTE]
> Сейчас [управляемый экземпляр](sql-database-managed-instance.md) не поддерживает миграцию базы данных в базу данных экземпляра из файла `BACPAC` с помощью портала Azure.

Чтобы импортировать в отдельную базу данных с помощью портала Azure, откройте страницу для сервера базы данных для отдельной базы данных, а затем выберите **Импорт базы данных** на панели инструментов.  

   ![Импорт базы данных](./media/sql-database-import/import.png)

Выберите учетную запись хранения, контейнер и файл `BACPAC`, который нужно импортировать. Укажите размер новой базы данных (обычно он такой же, как и у источника) и учетные данные для целевого сервера SQL Server. Список значений, доступных для новой базы данных SQL Azure, см. в статье [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

### <a name="monitor-imports-progress"></a>Отслеживание хода выполнения импорта

Чтобы отслеживать ход выполнения импорта, откройте страницу сервера базы данных. В разделе **Параметры** выберите **Журнал импорта и экспорта**. При успешном выполнении импорта отображается состояние **Завершено**.

Выберите **Базы данных SQL**, чтобы проверить активность базы данных на сервере базы данных, и убедитесь, что для новой базы данных задано состояние **В сети**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Импорт из BACPAC-файла с помощью SqlPackage

Импорт базы данных SQL Server с помощью программы командной строки [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) описывается в разделе [Параметры и свойства импорта](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). В состав SqlPackage входят последние версии [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, новую версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) можно скачать в Центре загрузки Майкрософт.

Вместо портала Azure рекомендуется использовать SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в Базу данных SQL Azure с помощью файлов `BACPAC` см. в блоге команды Помощника по Azure для пользователей SQL Server [здесь](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Приведенная ниже команда SqlPackage импортирует базу данных **AdventureWorks2008R2** из локального хранилища на сервер Базы данных SQL Azure, который называется **mynewserver20170403**. Эта команда создает базу данных **myMigratedDatabase** с уровнем служб **Премиум** и целевым уровнем обслуживания **P6**. Подставьте соответствующие значения для своей среды.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Чтобы подключиться к серверу Базы данных SQL, управляя отдельной базой данных при включенном корпоративном брандмауэре, необходимо открыть порт 1433 в брандмауэре. Чтобы подключиться к управляемому экземпляру, необходимо обладать [подключением типа "точка — сеть"](/sql-database-managed-instance-configure-p2s.md) или подключением Express Route.
>

В этом примере показано, как импортировать базу данных с помощью SqlPackage, используя универсальную проверку подлинности Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Импорт из BACPAC-файла с помощью PowerShell

Используйте командлет [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport), чтобы отправить запрос на импорт базы данных к службе Базы данных SQL Azure. Операция импорта может занять некоторое время в зависимости от размера базы данных.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Чтобы проверить ход выполнения импорта, используйте командлет [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). Если выполнить его немедленно после запроса, то обычно возвращается сообщение **Состояние: выполняется**. Отображение сообщения **Состояние: выполнен** означает, что импорт завершен.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Еще один пример сценария приведен в разделе [Импорт базы данных из BACPAC-файла](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Ограничения

Импорт базы данных в эластичный пул не поддерживается. Однако вы можете сначала выполнить импорт данных в отдельную базу данных, а затем переместить эту базу данных в эластичный пул.

## <a name="import-using-wizards"></a>Импорт с помощью мастера

Импорт также можно выполнить с помощью следующих мастеров:

- [мастер импорта приложения уровня данных в SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard);
- [мастер импорта и экспорта SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Дополнительная информация

- Чтобы научиться подключаться к импортированной Базе данных SQL и отправлять к ней запросы, ознакомьтесь со статьей [Краткое руководство. Подключение к базе данных SQL Azure и создание запросов к ней с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md).
- Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-single-database-migrate.md).
- Чтобы узнать, как безопасно управлять ключами к хранилищу данных и подписанными URL-адресами и совместно их использовать, ознакомьтесь с [руководством по безопасности службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
