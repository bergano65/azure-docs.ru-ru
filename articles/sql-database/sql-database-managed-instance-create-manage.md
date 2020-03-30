---
title: Ссылка API управления для управляемого экземпляра
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268864"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Справочник по API управления для Управляемых экземпляров Базы данных SQL Azure

Вы можете создавать Управляемые экземпляры Базы данных SQL Azure и управлять ими с помощью портала Azure, PowerShell, Azure CLI, REST API и Transact-SQL. В этой статье представлен обзор функций и API, которые можно использовать для создания и настройки Управляемого экземпляра.

## <a name="azure-portal-create-a-managed-instance"></a>Портал Azure: Создание управляемого экземпляра

Для быстрого запуска, показывающего, как создать управляемую базу данных Azure S'L, [см.](sql-database-managed-instance-get-started.md)

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Создание и управление управляемыми экземплярами

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных Azure S'L, но все будущие разработки предназначены для модуля Az.Sql. Для этих cmdlets, см [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле Az и в модулях Azrm существенно идентичны.

Для создания управляемых экземпляров и управления ими с помощью Azure PowerShell используйте приведенные ниже командлеты PowerShell. Если вам нужно выполнить установку или обновление PowerShell, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Для примеров скриптов PowerShell см. [сценарий быстрого запуска: Создайте управляемый экземпляр Azure S'L с помощью библиотеки PowerShell.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/)

| Командлет | Описание |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Создает Управляемый экземпляр Базы данных SQL Azure |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Возвращает информацию об Управляемом экземпляре SQL Azure|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Задает свойства для Управляемого экземпляра Базы данных SQL Azure|
|[Удалить-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Удаляет Управляемый экземпляр Базы данных SQL Azure|
|[Новый-AzSqlInstanceБаза данных](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Создает базу данных управляемого экземпляра Базы данных SQL Azure|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Возвращает информацию о базе данных управляемого экземпляра Базы данных SQL Azure|
|[Удалить-AzSqlInstanceБаза данных](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Удаляет базу данных управляемого экземпляра Базы данных SQL Azure|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Восстанавливает базу данных управляемого экземпляра Базы данных SQL Azure|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI: Создание и управление управляемыми экземплярами

Чтобы создать управляемые экземпляры и управлять ими с помощью [Azure CLI](/cli/azure), воспользуйтесь следующими командами Azure CLI для [Управляемого экземпляра SQL](/cli/azure/sql/mi). Запускайте интерфейс командной строки в браузере с помощью [Cloud Shell](/azure/cloud-shell/overview) либо [установите](/cli/azure/install-azure-cli) его на платформе macOS, Linux или Windows.

> [!TIP]
> Краткое руководство по работе с Управляемым экземпляром SQL с помощью Azure CLI см. [здесь](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Командлет | Описание |
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

## <a name="transact-sql-create-and-manage-instance-databases"></a>Трансакт-СЗЛ: Создание и управление базами данных экземпляров

Для создания базы данных управляемого экземпляра и управления ею после создания управляемого экземпляра используйте следующие команды T-SQL. Эти команды можно выполнить с помощью портала Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) или любой другой программы, которая может подключаться к серверу Базы данных SQL Azure и передавать команды Transact-SQL.

> [!TIP]
> Для быстрой настройки и подключения к управляемой инстанции с помощью студии управления серверами S'L на Microsoft Windows [см.](sql-database-managed-instance-configure-vm.md) [Quickstart: Configure a point-to-site connection to an Azure SQL Database Managed Instance from on-premises](sql-database-managed-instance-configure-p2s.md)
> [!IMPORTANT]
> С помощью Transact-SQL невозможно создать или удалить Управляемый экземпляр.

| Команда | Описание |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Создает базу данных управляемого экземпляра. Для создания новой базы данных необходимо подключение к базе данных master.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Изменяет базу данных управляемого экземпляра SQL Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API: Создание и управление управляемыми экземплярами

Чтобы создать Управляемые экземпляры и управлять ими, используйте следующие запросы REST API.

| Команда | Описание |
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
