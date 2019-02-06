---
title: Справочник по API управления для Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: Узнайте о создании Управляемых экземпляров Базы данных SQL Azure и управлении ими.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 26445017553435454e929d9852dfd9fc173c3b2e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460103"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Справочник по API управления для Управляемых экземпляров Базы данных SQL Azure

Вы можете создавать Управляемые экземпляры Базы данных SQL Azure и управлять ими с помощью портала Azure, PowerShell, Azure CLI, REST API и Transact-SQL. В этой статье представлен обзор функций и API, которые можно использовать для создания и настройки Управляемого экземпляра.

## <a name="azure-portal-create-a-managed-instance"></a>Портал Azure: Создание управляемого экземпляра

Дополнительные сведения по созданию Управляемого экземпляра Базы данных SQL Azure см. в статье [Краткое руководство. Создание Управляемого экземпляра Базы данных SQL Azure](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Создание управляемых экземпляров и управление ими

Для создания управляемых экземпляров и управления ими с помощью Azure PowerShell используйте приведенные ниже командлеты PowerShell. Если вам нужно выполнить установку или обновление PowerShell, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Примеры сценариев PowerShell см. в [кратком руководстве по созданию Управляемого экземпляра SQL Azure с помощью библиотеки PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Командлет | ОПИСАНИЕ |
| --- | --- |
|[New-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Создает Управляемый экземпляр Базы данных SQL Azure |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlinstance)|Возвращает информацию об Управляемом экземпляре SQL Azure|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlinstance)|Задает свойства для Управляемого экземпляра Базы данных SQL Azure|
|[Remove-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqlinstance)|Удаляет Управляемый экземпляр Базы данных SQL Azure|
|[New-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstancedatabase)|Создает базу данных Управляемого экземпляра Базы данных SQL Azure|
|[Get-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlinstancedatabase)|Возвращает информацию о базе данных Управляемого экземпляра Базы данных SQL Azure|
|[Remove-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqlinstancedatabase)|Удаляет базу данных Управляемого экземпляра Базы данных SQL Azure|
|[Restore-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqlinstancedatabase)|Восстанавливает базу данных Управляемого экземпляра Базы данных SQL Azure|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Создание управляемых экземпляров и управление ими

Чтобы создать управляемые экземпляры и управлять ими с помощью [Azure CLI](/cli/azure), воспользуйтесь следующими командами Azure CLI для [Управляемого экземпляра SQL](/cli/azure/sql/mi). Запускайте интерфейс командной строки в браузере с помощью [Cloud Shell](/azure/cloud-shell/overview) либо [установите](/cli/azure/install-azure-cli) его на платформе macOS, Linux или Windows.

> [!TIP]
> Краткое руководство по работе с Управляемым экземпляром SQL с помощью Azure CLI см. [здесь](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Командлет | ОПИСАНИЕ |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Создает Управляемый экземпляр|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Перечисляет доступные Управляемые экземпляры|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Получает сведения об Управляемом экземпляре|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Обновляет Управляемый экземпляр|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Удаляет Управляемый экземпляр|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Создает управляемую базу данных|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Выводит список доступных управляемых баз данных|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Восстанавливает управляемую базу данных|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Удаляет управляемую базу данных|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Создание и управление базами данных управляемого экземпляра

Для создания базы данных управляемого экземпляра и управления ею после создания Управляемого экземпляра используйте следующие команды T-SQL. Эти команды можно выполнить с помощью портала Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) или любой другой программы, которая может подключаться к серверу Базы данных SQL Azure и передавать команды Transact-SQL.

> [!TIP]
> Краткие руководства для настройки и подключения к Управляемому экземпляру с помощью SQL Server Management Studio в Microsoft Windows см. в статье [Краткое руководство. Настройка виртуальной машины Azure для подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-configure-vm.md) и [Краткое руководство. Настройка подключения "точка — сеть" к Управляемому экземпляру Базы данных SQL Azure с локального компьютера](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> С помощью Transact-SQL невозможно создать или удалить Управляемый экземпляр.

| Команда | ОПИСАНИЕ |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Создает базу данных Управляемого экземпляра. Для создания базы данных требуется подключение к базе данных master.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Изменяет базу данных Управляемого экземпляра SQL Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: Создание управляемых экземпляров и управление ими

Чтобы создать Управляемые экземпляры и управлять ими, используйте следующие запросы REST API.

| Команда | ОПИСАНИЕ |
| --- | --- |
|[Управляемые экземпляры. Создание или обновление](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Создает или обновляет Управляемый экземпляр.|
|[Управляемые экземпляры. Удаление](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Удаляет Управляемый экземпляр.|
|[Управляемые экземпляры. Получение](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Получает Управляемый экземпляр.|
|[Управляемые экземпляры. Перечисление](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Возвращает список Управляемых экземпляров в подписке.|
|[Управляемые экземпляры. Перечисление по группе ресурсов](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Возвращает список Управляемых экземпляров в группе ресурсов.|
|[Управляемые экземпляры. Обновление](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Обновляет Управляемый экземпляр.|

## <a name="next-steps"></a>Дополнительная информация

- Чтобы узнать о переносе базы данных SQL Server в Azure, ознакомьтесь с разделом [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).
- Дополнительные сведения о поддерживаемых функциях см. [здесь](sql-database-features.md).
