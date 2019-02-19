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
ms.date: 02/08/2019
ms.openlocfilehash: 730d944edfc16e9ef399333540b14fcfced4ce44
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996395"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-from-backups"></a>Восстановление отдельной базы данных SQL Azure из резервных копий с помощью PowerShell

Этот пример сценария PowerShell восстанавливает базу данных SQL Azure из геоизбыточной резервной копии, восстанавливает последнюю резервную копию удаленной базы данных SQL Azure и восстанавливает базу данных SQL Azure до определенной точки во времени.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим руководством вам понадобится модуль Azure PowerShell 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. | [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Создает сервер Базы данных SQL, на котором размещена отдельная база данных или пул эластичных баз данных. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Создает на сервере Базы данных SQL отдельную базу данных или базу данных в составе пула. |
[Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) | Получает геоизбыточную резервную копию отдельной базы данных или базы данных в составе пула. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) | Восстанавливает отдельную базу данных SQL или базу данных SQL в составе пула. |
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase) | Удаляет отдельную базу данных SQL Azure или базу данных SQL Azure в составе пула. |
| [Get-AzureRmSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Получает удаленную отдельную базу данных или базу данных в составе пула, которую можно восстановить. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для базы данных SQL Azure можно найти [здесь](../sql-database-powershell-samples.md).
