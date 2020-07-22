---
title: Справочник по API управления для Управляемый экземпляр Azure SQL
description: Сведения о создании и настройке управляемых экземпляров Управляемый экземпляр Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3f0eb2c1ecb26475b25cd5ca41c9c61d5e2b47a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695612"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Справочник по управляемому API для Управляемый экземпляр Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Вы можете создавать и настраивать управляемые экземпляры Azure SQL Управляемый экземпляр с помощью портал Azure, PowerShell, Azure CLI, REST API и Transact-SQL. В этой статье содержатся общие сведения о функциях и API, которые можно использовать для создания и настройки управляемых экземпляров.

## <a name="azure-portal-create-a-managed-instance"></a>Портал Azure: создание управляемого экземпляра

Краткое руководство, в котором показано, как создать управляемый экземпляр, см. в разделе [Краткое руководство. Создание управляемого экземпляра](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: создание и Настройка управляемых экземпляров

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Модуль PowerShell Azure Resource Manager по-прежнему поддерживается базой данных SQL Azure, но вся будущая разработка предназначена для модуля AZ. SQL. Эти командлеты см. в разделе [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Аргументы для команд в модуле AZ и в модулях AzureRM существенно идентичны.

Для создания управляемых экземпляров и управления ими с помощью Azure PowerShell используйте приведенные ниже командлеты PowerShell. Если вам нужно установить или обновить PowerShell, см. статью [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Примеры сценариев PowerShell см. в разделе сценарий быстрого запуска [: создание управляемого экземпляра с помощью библиотеки PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Командлет | Описание |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Создает управляемый экземпляр |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Возвращает сведения об управляемом экземпляре.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Задает свойства для управляемого экземпляра.|
|[Remove-Азсклинстанце](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Удаляет управляемый экземпляр.|
|[New-Азсклинстанцедатабасе](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Создает базу данных SQL Управляемый экземпляр.|
|[Get-Азсклинстанцедатабасе](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Возвращает сведения о базе данных SQL Управляемый экземпляр.|
|[Remove-Азсклинстанцедатабасе](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Удаляет базу данных SQL Управляемый экземпляр.|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Восстанавливает базу данных SQL Управляемый экземпляр.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: создание и Настройка управляемых экземпляров

Чтобы создать и настроить управляемые экземпляры с [Azure CLI](/cli/azure), используйте следующие [команды Azure CLI для SQL управляемый экземпляр](/cli/azure/sql/mi). Используйте [Azure Cloud Shell](/azure/cloud-shell/overview) , чтобы запустить интерфейс командной строки в браузере или [установить](/cli/azure/install-azure-cli) его в MacOS, Linux или Windows.

> [!TIP]
> Краткое руководство по работе с Управляемым экземпляром SQL с помощью Azure CLI см. [здесь](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Командлет | Описание |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Создает управляемый экземпляр|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Выводит список доступных управляемых экземпляров.|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Возвращает сведения об управляемом экземпляре.|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Обновляет управляемый экземпляр.|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Удаляет управляемый экземпляр.|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Создает управляемую базу данных.|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Выводит список доступных управляемых баз данных.|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Восстанавливает управляемую базу данных.|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Удаляет управляемую базу данных.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: создание и настройка баз данных экземпляров

Чтобы создать и настроить базы данных экземпляра после создания управляемого экземпляра, используйте следующие команды T-SQL. Эти команды можно выполнить с помощью портал Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)или любой другой программы, которая может подключаться к серверу и передавать команды Transact-SQL.

> [!TIP]
> Краткие руководства, в которых показано, как настроить и подключиться к управляемому экземпляру с помощью SQL Server Management Studio в Microsoft Windows, см. в разделе [Краткое руководство. Настройка виртуальной машины Azure для подключения к Azure sql управляемый экземпляр](connect-vm-instance-configure.md) и [Краткое руководство. Настройка подключения типа "точка — сеть" к Azure SQL управляемый экземпляр из локальной](point-to-site-p2s-configure.md)среды.

> [!IMPORTANT]
> Нельзя создать или удалить управляемый экземпляр с помощью Transact-SQL.

| Get-Help | Описание |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Создает новую базу данных экземпляра в SQL Управляемый экземпляр. Для создания новой базы данных необходимо подключение к базе данных master.|
| [ИЗМЕНЕНИЕ БАЗЫ ДАННЫХ](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Изменяет экземпляр базы данных в Управляемый экземпляр SQL.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: создание и Настройка управляемых экземпляров

Чтобы создать и настроить управляемые экземпляры, используйте следующие запросы REST API.

| Get-Help | Описание |
| --- | --- |
|[Управляемые экземпляры. Создание или обновление](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Создает или обновляет управляемый экземпляр.|
|[Управляемые экземпляры. Удаление](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Удаляет управляемый экземпляр.|
|[Управляемые экземпляры. Получение](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Возвращает управляемый экземпляр.|
|[Управляемые экземпляры. Перечисление](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Возвращает список управляемых экземпляров в подписке.|
|[Управляемые экземпляры. Перечисление по группе ресурсов](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Возвращает список управляемых экземпляров в группе ресурсов.|
|[Управляемые экземпляры. Обновление](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Обновляет управляемый экземпляр.|

## <a name="next-steps"></a>Дальнейшие шаги

- Чтобы узнать о переносе базы данных SQL Server в Azure, ознакомьтесь с разделом [Миграция базы данных SQL Server в базу данных SQL в облаке](../database/migrate-to-database-from-sql-server.md).
- Дополнительные сведения о поддерживаемых функциях см. [здесь](../database/features-comparison.md).
