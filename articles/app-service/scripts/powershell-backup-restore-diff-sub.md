---
title: 'PowerShell: восстановление резервной копии в другой подписке'
description: Сведения об использовании Azure PowerShell для автоматизации процессов развертывания и управления в Службе приложений. В этом примере показано, как восстановить резервную копию в другой подписке.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8168fbedd1a639dce0ab8bd076e14ab24e2f6d1e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685821"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Восстановление веб-приложения из резервной копии в других подписках с помощью PowerShell

Этот пример скрипта позволяет извлечь ранее созданную резервную копию из существующего веб-приложения и восстановить ее для веб-приложения в другой подписке. 

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure. 

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Вы можете удалить ставшие ненужными группу ресурсов, веб-приложение и все связанные с ним ресурсы, используя следующую команду:

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Добавляет учетную запись, прошедшую аутентификацию и используемую для запросов командлета к Azure Resource Manager.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Получение списка резервных копий веб-приложения. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Создает веб-приложение. |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Восстановление веб-приложения из ранее созданной резервной копии. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../samples-powershell.md).
