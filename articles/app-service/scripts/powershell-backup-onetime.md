---
title: Пример сценария Azure PowerShell. Резервное копирование веб-приложения | Документация Майкрософт
description: Пример сценария Azure PowerShell. Резервное копирование веб-приложения
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: fc755f82-ca3e-4532-b251-690b699324d6
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 13d985b95d971ea28ec5345dbb86df7a3271b716
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115187"
---
# <a name="back-up-a-web-app-using-powershell"></a>Резервное копирование веб-приложения с помощью PowerShell

С помощью этого скрипта можно создать веб-приложение в службе приложений со связанными ресурсами, а затем однократно создать для него резервную копию. 

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure. 

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-onetime/backup-onetime.ps1?highlight=1-5 "Back up a web app")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполнив пример сценария, вы можете удалить группу ресурсов, веб-приложение и все связанные ресурсы, используя следующую команду.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Создает учетную запись хранения. |
| [New-AzStorageContainer](/powershell/module/azure.storage/new-AzStoragecontainer) | Позволяет создать контейнер хранилища Azure. |
| [New-AzStorageContainerSASToken](/powershell/module/azure.storage/new-AzStoragecontainersastoken) | Создание маркера SAS для контейнера хранилища Azure.  |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Создает план службы приложений. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Создает веб-приложение. |
| [New-AzWebAppBackup](/powershell/module/az.websites/new-azwebappbackup) | Позволяет создать резервную копию для веб-приложения. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Получение списка резервных копий веб-приложения. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../samples-powershell.md).
