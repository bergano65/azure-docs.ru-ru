---
title: Импорт BACPAC-файла для создания базы данных в базе данных SQL Azure
description: Создание новой базы данных в базе данных SQL Azure или Управляемый экземпляр SQL Azure из BACPAC-файла.
services: sql-database
ms.service: sql-db-mi
ms.subservice: migrate
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: a1e349d284d8f2c0c9c5194ac9f52534cd1ab973
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503248"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Краткое руководство. импорт BACPAC-файла в базу данных в базе данных SQL Azure или Azure SQL Управляемый экземпляр
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Вы можете импортировать SQL Server базу данных в базу данных SQL Azure или SQL Управляемый экземпляр с помощью [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -файла. Вы можете импортировать данные из BACPAC-файла, который хранится в хранилище BLOB-объектов Azure (только цен. категория "Стандартный") или локальном хранилище, в локальное расположение. Чтобы развернуть скорость импорта, предоставив дополнительные ресурсы и ускоренную скорость доступа к ним, масштабируйте базу данных до более высокого уровня службы и объема вычислительных ресурсов во время процесса импорта. После завершения импорта эти значения можно уменьшить.

> [!NOTE]
> Уровень совместимости импортированной базы данных основан на уровне совместимости базы данных-источника.

> [!IMPORTANT]
> После импорта базы данных вы можете работать с ней при текущем уровне совместимости (уровень 100 для базы данных AdventureWorks2008R2) или при более высоком уровне. Дополнительные сведения о последствиях и параметрах работы базы данных с определенным уровнем совместимости см. в разделе [ALTER DATABASE (Transact-SQL) Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Уровень совместимости ALTER DATABASE (Transact-SQL)). Кроме того, в статье [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) вы можете получить сведения о дополнительных настройках уровня базы данных, связанных с уровнем совместимости.

## <a name="using-azure-portal"></a>Использование портала Azure

Просмотрите это видео, чтобы узнать, как импортировать данные из BACPAC-файла в портал Azure или продолжить чтение ниже:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Портал Azure поддерживает создание ](https://portal.azure.com) *только* отдельной базы данных в Базе данных SQL Azure и *только* из файла BACPAC, который хранится в хранилище BLOB-объектов Azure.

Чтобы перенести базу данных в [управляемый экземпляр Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) из BACPAC-файла, используйте SQL Server Management Studio или SqlPackage с помощью портал Azure или Azure PowerShell в настоящее время не поддерживается.

> [!NOTE]
> Компьютеры, обрабатывающие запросы на импорт и экспорт, отправленные с помощью портал Azure или PowerShell, должны хранить BACPAC-файл, а также временные файлы, созданные платформой приложения уровня данных (DacFX). Необходимое дисковое пространство существенно различается в базах данных с одинаковым размером и может потребовать места на диске в 3 раза больше размера базы данных. Компьютеры, на которых работает запрос на импорт или экспорт, имеют только 450GB место на локальном диске. В результате некоторые запросы могут завершиться ошибкой `There is not enough space on the disk` . В этом случае решение заключается в запуске sqlpackage.exe на компьютере с достаточным местом на локальном диске. Мы советуем использовать SqlPackage для импорта и экспорта баз данных, превышающих 150 ГБ, чтобы избежать этой проблемы.

1. Чтобы импортировать из BACPAC-файла в новую одиночную базу данных с помощью портал Azure, откройте соответствующую серверную страницу, а затем на панели инструментов выберите **Импорт базы данных**.  

   ![База данных import1](./media/database-import/sql-server-import-database.png)

1. Сначала выберите учетную запись хранения и контейнер для файла BACPAC, а затем — сам файл BACPAC из которого необходимо выполнить импорт.

1. Укажите размер новой базы данных (обычно он такой же, как и у источника) и учетные данные для целевого сервера SQL Server. Список возможных значений для новой базы данных в базе данных SQL Azure см. в разделе [Создание базы данных](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![База данных import2](./media/database-import/sql-server-import-database-settings.png)

1. Нажмите кнопку **ОК**.

1. Чтобы отслеживать ход выполнения импорта, откройте страницу сервера базы данных. В разделе **Параметры** выберите **Журнал импорта и экспорта**. При успешном выполнении импорта отображается состояние **Завершено**.

   ![Состояние импорта базы данных](./media/database-import/sql-server-import-database-history.png)

1. Выберите **Базы данных SQL**, чтобы проверить активность базы данных на сервере, и убедитесь, что для новой базы данных задано состояние **В сети**.

## <a name="using-sqlpackage"></a>Использование SqlPackage

Импорт базы данных SQL Server с помощью программы командной строки [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) описывается в разделе [Параметры и свойства импорта](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) включают SqlPackage. Кроме того, новую версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) можно скачать в Центре загрузки Майкрософт.

Вместо портала Azure рекомендуется использовать SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в Базу данных SQL Azure с помощью файлов `BACPAC` см. в блоге команды Помощника по Azure для пользователей SQL Server [здесь](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Следующая команда SqlPackage импортирует базу данных **AdventureWorks2008R2** из локального хранилища на логический сервер SQL Server с именем **mynewserver20170403**. Эта команда создает базу данных **myMigratedDatabase** с уровнем служб **Премиум** и целевым уровнем служб **P6**. Подставьте соответствующие значения для своей среды.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Чтобы подключиться к базе данных SQL Azure через корпоративный брандмауэр, в брандмауэре должен быть открыт порт 1433. Для подключения к SQL Управляемый экземпляр требуется подключение типа " [точка — сеть](../managed-instance/point-to-site-p2s-configure.md) " или подключение Express Route.

В этом примере показано, как импортировать базу данных с помощью SqlPackage, используя универсальную проверку подлинности Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Использование PowerShell

> [!NOTE]
> [Управляемый экземпляр SQL](../managed-instance/sql-managed-instance-paas-overview.md) в настоящее время не поддерживает перенос базы данных в экземпляр базы данных из BACPAC-файла с помощью Azure PowerShell. Для импорта в Управляемый экземпляр SQL используйте SQL Server Management Studio или SQLPackage.

> [!NOTE]
> Компьютеры, обрабатывающие запросы на импорт и экспорт, отправленные с помощью портала или PowerShell, должны хранить BACPAC-файл, а также временные файлы, созданные платформой приложения уровня данных (DacFX). Требуемое дисковое пространство значительно различается в баз данных с одинаковым размером и может занять до 3 раз из размера базы данных. Компьютеры, на которых работает запрос на импорт или экспорт, имеют только 450GB место на локальном диске. В результате некоторые запросы могут завершиться ошибкой "на диске недостаточно места". В этом случае решение заключается в запуске sqlpackage.exe на компьютере с достаточным местом на локальном диске. При импорте или экспорте баз данных, размер которых превышает 150 ГБ, используйте SqlPackage, чтобы избежать этой проблемы.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager (RM) по-прежнему поддерживается, но вся будущая разработка предназначена для модуля AZ. SQL. Модуль AzureRM продолжит принимать исправления ошибок до 2020 декабря.  Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны. Дополнительные сведения о совместимости см. [в разделе Введение в новый модуль Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Используйте командлет [New-азсклдатабасеимпорт](/powershell/module/az.sql/new-azsqldatabaseimport) , чтобы отправить запрос на импорт базы данных в Azure. Операция импорта может занять некоторое время в зависимости от размера базы данных.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Для проверки хода выполнения импорта можно использовать командлет [Get-азсклдатабасеимпортекспортстатус](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Выполнение командлета сразу же после того, как запрос возвращает значение `Status: InProgress` . Импорт завершается, когда вы видите `Status: Succeeded` .

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Используйте команду [AZ-SQL-DB-Import](/cli/azure/sql/db#az-sql-db-import) , чтобы отправить запрос на импорт базы данных в Azure. Операция импорта может занять некоторое время в зависимости от размера базы данных.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Еще один пример сценария приведен в разделе [Импорт базы данных из BACPAC-файла](scripts/import-from-bacpac-powershell.md).

## <a name="limitations"></a>Ограничения

- Импорт базы данных в эластичный пул не поддерживается. Однако вы можете сначала выполнить импорт данных в отдельную базу данных, а затем переместить эту базу данных в эластичный пул.
- Служба импорта и экспорта не работает, если для параметра Разрешить доступ к службам Azure задано значение Выкл. Однако эту проблему можно решить, вручную запустив sqlpackage.exe из виртуальной машины Azure или выполнив экспорт непосредственно в коде с помощью API DACFx.

## <a name="import-using-wizards"></a>Импорт с помощью мастера

Импорт также можно выполнить с помощью следующих мастеров:

- [мастер импорта приложения уровня данных в SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard);
- [Мастер импорта и экспорта SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о подключении к базе данных в базе данных SQL Azure и выполнении запросов к ней см. в разделе [Краткое руководство по базе данных SQL Azure: использование SQL Server Management Studio для подключения и запроса данных](connect-query-ssms.md).
- Сведения о миграции из SQL Server в Базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](migrate-to-database-from-sql-server.md).
- Чтобы узнать, как безопасно управлять ключами к хранилищу данных и подписанными URL-адресами и совместно их использовать, ознакомьтесь с [руководством по безопасности службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
