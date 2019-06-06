---
title: Пример для PowerShell. Группа отработки отказа георепликации для отдельной базы данных SQL Azure | Документация Майкрософт
description: Пример скрипта Azure PowerShell для настройки группы отработки отказа активной георепликации отдельной базы данных в Базе данных SQL Azure и отработки отказа этой базы данных.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: a90753d075f7f4db9dbae95fde979d471db3247c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729190"
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-database-in-azure-sql-database"></a>Настройка группы отработки отказа активной георепликации для отдельной базы данных в Базе данных SQL Azure с помощью PowerShell

Этот пример скрипта PowerShell настраивает группу отработки отказа активной георепликации для отдельной базы данных и выполняет для нее отработку отказа во вторичную реплику базы данных.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать PowerShell локально, вам понадобится AZ PowerShell 1.4.0 или последующей версии для работы с этим руководством. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-scripts"></a>Примеры скриптов

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-database-failover-group.ps1?highlight=18-21 "Set up failover group for single database")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Используйте следующую команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещены отдельные базы данных и эластичные пулы. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Создает эластичный пул. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Обновляет свойства базы данных или перемещает базу данных в эластичный пул, из него или между эластичными пулами. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Создает базу данных-получатель для существующей базы данных и начинает репликацию данных. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Получает одну или несколько баз данных. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Преобразует базу данных-получатель в базу данных-источник для запуска отработки отказа.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Получает связи георепликации между базой данных SQL Azure и группой ресурсов или SQL Server. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) | Завершает репликацию данных между базой данных SQL и указанной базой данных-получателем. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Создание группы отработки отказа в службе "База данных SQL Azure" для указанных серверов. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Переключение ролей серверов в группе отработки отказа и переключение всех баз данных-получателей на первичную роль. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Получение данных определенных групп отработки отказа в службе "База данных SQL Azure" или списка групп отработки отказа на сервере. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
