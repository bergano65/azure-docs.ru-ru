---
title: Импорт BACPAC-файла для создания базы данных SQL Azure | Документация Майкрософт
description: Создайте базу данных SQL Azure, импортировав BACPAC-файл.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/05/2018
ms.openlocfilehash: 6753666f1747c95ad3486444ed41e3cad0b8e905
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084182"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>Краткое руководство. Импорт BACPAC-файла в новую базу данных SQL Azure

Вы можете перенести базу данных SQL Server в базу данных SQL Azure с помощью файла [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (ZIP-файл с расширением `.bacpac`, содержащий метаданные и данные базы данных). BACPAC-файл можно импортировать из хранилища BLOB-объектов Azure (только уровня "Стандартный") или из локального хранилища. Чтобы обеспечить максимальную скорость импорта, рекомендуем указать более высокий уровень служб и объем вычислительных ресурсов (например, P6), а затем, после успешного импорта, понизить его. Уровень совместимости импортированной базы данных основан на уровне совместимости базы данных-источника.

> [!IMPORTANT]
> После импорта базы данных вы можете работать с ней при текущем уровне совместимости (уровень 100 для базы данных AdventureWorks2008R2) или при более высоком уровне. Дополнительные сведения о последствиях и параметрах работы базы данных с определенным уровнем совместимости см. в разделе [ALTER DATABASE (Transact-SQL) Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Уровень совместимости ALTER DATABASE (Transact-SQL)). Кроме того, в статье [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) вы можете получить сведения о дополнительных настройках уровня базы данных, связанных с уровнем совместимости.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Импорт из BACPAC-файла с помощью портала Azure

Этот раздел содержит указания по созданию на [портале Azure](https://portal.azure.com) базы данных SQL Azure из BACPAC-файла, расположенного в хранилище BLOB-объектов Azure. Портал поддерживает импорт *только* BACPAC-файла из хранилища BLOB-объектов Azure.

> [!NOTE]
> [Управляемый экземпляр Базы данных SQL Azure](sql-database-managed-instance.md) поддерживает импорт из BACPAC-файла и с помощью других методов, указанных в этой статье. Миграция с помощью портала Azure сейчас не поддерживается.

Чтобы импортировать базу данных с помощью портала Azure, откройте страницу для логического сервера, на котором будет выполняться импорт, и на панели инструментов выберите **Импорт базы данных**.  

   ![Импорт базы данных](./media/sql-database-import/import.png)

Выберите учетную запись хранения, контейнер и BACPAC-файл, который нужно импортировать. Укажите размер новой базы данных (обычно он такой же, как и у источника) и учетные данные для целевого сервера SQL Server. 

### <a name="monitor-imports-progress"></a>Отслеживание хода выполнения импорта

Чтобы отслеживать ход выполнения импорта, откройте страницу логического сервера импортируемой базы данных. Прокрутите ее вниз до раздела **Параметры** и выберите **журнал импорта и экспорта**. При успешном выполнении импорта отображается состояние **Завершено**.

Выберите **Базы данных SQL**, чтобы проверить активность базы данных на сервере, и убедитесь, что для новой базы данных задано состояние **В сети**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Импорт из BACPAC-файла с помощью SqlPackage

Импорт базы данных SQL с помощью программы командной строки [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) описывается в разделе о [параметрах и свойствах операции импорта](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, новую версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) можно скачать в Центре загрузки Майкрософт.

Рекомендуем использовать SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Приведенная ниже команда SqlPackage импортирует базу данных **AdventureWorks2008R2** из локального хранилища на логический сервер Базы данных SQL Azure, который называется **mynewserver20170403**. Эта команда создает базу данных **myMigratedDatabase** с уровнем служб **Премиум** и целевым уровнем обслуживания **P6**. Подставьте соответствующие значения для своей среды.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Логический сервер базы данных SQL Azure прослушивает порт 1433. Чтобы подключиться к логическому серверу не из пределов корпоративного брандмауэра, необходимо открыть этот порт в брандмауэре.
>

В этом примере показано, как импортировать базу данных с помощью SqlPackage, используя универсальную проверку подлинности Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Импорт из BACPAC-файла с помощью PowerShell

Используйте командлет [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport), чтобы отправить запрос на импорт базы данных к службе Базы данных SQL Azure. Операция импорта может занять некоторое время в зависимости от размера базы данных.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "myResourceGroup" `
    -ServerName "myLogicalServer" `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName "myStorageAccount").Value[0] `
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

Импорт базы данных в эластичный пул не поддерживается. Однако вы можете сначала выполнить импорт данных в отдельную базу данных, а затем переместить эту базу данных в пул.

## <a name="import-using-wizards"></a>Импорт с помощью мастера

Импорт также можно выполнить с помощью следующих мастеров:

- [мастер импорта приложения уровня данных в SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard);
- [мастер импорта и экспорта SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Дополнительная информация

- Чтобы научиться подключаться к импортированной Базе данных SQL и отправлять к ней запросы, ознакомьтесь со статьей [Краткое руководство. Подключение к базе данных SQL Azure и создание запросов к ней с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md).
- Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-cloud-migrate.md).
- Чтобы узнать, как безопасно управлять ключами к хранилищу данных и подписанными URL-адресами и совместно их использовать, ознакомьтесь с [руководством по безопасности службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
