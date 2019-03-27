---
title: Пример для PowerShell. Восстановление и архивация базы данных SQL Azure | Документация Майкрософт
description: Пример скрипта Azure PowerShell для восстановления отдельной базы данных SQL Azure из геоизбыточных резервных копий.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3bfefa704fdd819b3841dcc58866c310353bfdc3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883616"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-from-backups"></a>Восстановление отдельной базы данных SQL Azure из резервных копий с помощью PowerShell

Этот пример сценария PowerShell восстанавливает базу данных SQL Azure из геоизбыточной резервной копии, восстанавливает последнюю резервную копию удаленной базы данных SQL Azure и восстанавливает базу данных SQL Azure до определенной точки во времени.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится AZ PowerShell 1.4.0 или более поздней версии. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. | 
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Создает сервер Базы данных SQL, на котором размещена отдельная база данных или пул эластичных баз данных. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Создает на сервере Базы данных SQL отдельную базу данных или базу данных в составе пула. |
[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Получает геоизбыточную резервную копию отдельной базы данных или базы данных в составе пула. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Восстанавливает отдельную базу данных SQL или базу данных SQL в составе пула. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Удаляет отдельную базу данных SQL Azure или базу данных SQL Azure в составе пула. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Получает удаленную отдельную базу данных или базу данных в составе пула, которую можно восстановить. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
