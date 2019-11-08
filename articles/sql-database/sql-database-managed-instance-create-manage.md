---
title: Справочник по API управления для управляемого экземпляра
description: Узнайте о создании Управляемых экземпляров Базы данных SQL Azure и управлении ими.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823399"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Справочник по API управления для Управляемых экземпляров Базы данных SQL Azure

Вы можете создавать Управляемые экземпляры Базы данных SQL Azure и управлять ими с помощью портала Azure, PowerShell, Azure CLI, REST API и Transact-SQL. В этой статье представлен обзор функций и API, которые можно использовать для создания и настройки Управляемого экземпляра.

## <a name="azure-portal-create-a-managed-instance"></a>Портал Azure: создание управляемого экземпляра

Краткое руководство, в котором показано, как создать Управляемый экземпляр Базы данных SQL Azure, см. в разделе Краткое руководство. [создание управляемый экземпляр базы данных SQL Azure](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: создание управляемых экземпляров и управление ими

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRm существенно идентичны.

Для создания управляемых экземпляров и управления ими с помощью Azure PowerShell используйте приведенные ниже командлеты PowerShell. Если вам нужно выполнить установку или обновление PowerShell, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Примеры сценариев PowerShell см. в разделе [сценарий быстрого запуска: создание управляемый экземпляр Azure SQL с помощью библиотеки PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Командлет | Description (Описание) |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Создает Управляемый экземпляр Базы данных SQL Azure |
|[Get-Азсклинстанце](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Возвращает информацию об Управляемом экземпляре SQL Azure|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Задает свойства для Управляемого экземпляра Базы данных SQL Azure|
|[Remove-Азсклинстанце](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Удаляет Управляемый экземпляр Базы данных SQL Azure|
|[New-Азсклинстанцедатабасе](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Создает базу данных управляемого экземпляра Базы данных SQL Azure|
|[Get-Азсклинстанцедатабасе](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Возвращает информацию о базе данных управляемого экземпляра Базы данных SQL Azure|
|[Remove-Азсклинстанцедатабасе](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Удаляет базу данных управляемого экземпляра Базы данных SQL Azure|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Восстанавливает базу данных управляемого экземпляра Базы данных SQL Azure|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: создание управляемых экземпляров и управление ими

Чтобы создать управляемые экземпляры и управлять ими с помощью [Azure CLI](/cli/azure), воспользуйтесь следующими командами Azure CLI для [Управляемого экземпляра SQL](/cli/azure/sql/mi). Запускайте интерфейс командной строки в браузере с помощью [Cloud Shell](/azure/cloud-shell/overview) либо [установите](/cli/azure/install-azure-cli) его на платформе macOS, Linux или Windows.

> [!TIP]
> Краткое руководство по работе с Управляемым экземпляром SQL с помощью Azure CLI см. [здесь](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Командлет | Description (Описание) |
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

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: создание баз данных экземпляров и управление ими

Для создания базы данных управляемого экземпляра и управления ею после создания управляемого экземпляра используйте следующие команды T-SQL. Эти команды можно выполнить с помощью портала Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) или любой другой программы, которая может подключаться к серверу Базы данных SQL Azure и передавать команды Transact-SQL.

> [!TIP]
> Краткие руководства по настройке и подключению к Управляемый экземпляр с помощью SQL Server Management Studio в Microsoft Windows см. в статье [Краткое руководство. Настройка виртуальной машины Azure для подключения к управляемый экземпляр базы данных SQL Azure](sql-database-managed-instance-configure-vm.md) и [Краткое руководство. Настройте подключение типа "точка — сеть" к Управляемый экземпляр Базы данных SQL Azure из локальной](sql-database-managed-instance-configure-p2s.md)среды.
> [!IMPORTANT]
> С помощью Transact-SQL невозможно создать или удалить Управляемый экземпляр.

| Команда | Description (Описание) |
| --- | --- |
|[CREATE DATABASE (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Создает базу данных управляемого экземпляра. Для создания базы данных требуется подключение к базе данных master.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Изменяет базу данных управляемого экземпляра SQL Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: создание управляемых экземпляров и управление ими

Чтобы создать Управляемые экземпляры и управлять ими, используйте следующие запросы REST API.

| Команда | Description (Описание) |
| --- | --- |
|[Управляемые экземпляры. Создание или обновление](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Создает или обновляет Управляемый экземпляр.|
|[Управляемые экземпляры. Удаление](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Удаляет Управляемый экземпляр.|
|[Управляемые экземпляры. Получение](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Получает Управляемый экземпляр.|
|[Управляемые экземпляры. Перечисление](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Возвращает список Управляемых экземпляров в подписке.|
|[Управляемые экземпляры. Перечисление по группе ресурсов](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Возвращает список Управляемых экземпляров в группе ресурсов.|
|[Управляемые экземпляры. Обновление](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Обновляет Управляемый экземпляр.|

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать о переносе базы данных SQL Server в Azure, ознакомьтесь с разделом [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-single-database-migrate.md).
- Дополнительные сведения о поддерживаемых функциях см. [здесь](sql-database-features.md).
