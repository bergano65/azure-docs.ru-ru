---
title: PowerShell. Масштабирование веб-приложения вручную
description: Сведения об использовании Azure PowerShell для автоматизации процессов развертывания и управления в Службе приложений. В этом примере показано, как выполнить масштабирование приложения вручную.
author: msangapu-msft
tags: azure-service-management
ms.assetid: de5d4285-9c7d-4735-a695-288264047375
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 771cdecd6248f0a4fd350dc3d18e1c05edbd5a46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033876"
---
# <a name="scale-a-web-app-manually-using-powershell"></a>Масштабирование веб-приложения вручную с помощью PowerShell

В этой статье вы узнаете, как создать группу ресурсов, план службы приложений и веб-приложение. Затем вы выполните масштабирование плана службы приложений от одного экземпляра до нескольких.

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/), а затем выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure.

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/scale-manual/scale-manual.ps1 "Scale a web app manually")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполнив пример сценария, вы можете удалить группу ресурсов, веб-приложение и все связанные ресурсы, используя следующую команду.

```powershell
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Создает план службы приложений. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Создает веб-приложение. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Изменяет конфигурацию веб-приложения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../samples-powershell.md).
