---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667851"
---
## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Очистка ресурсов с помощью портала Azure

Удалите ресурсы на портале Azure, выяснив следующие действия по [очистке ресурсов.](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)

### <a name="clean-up-resources-using-powershell"></a>Очистка ресурсов с помощью PowerShell

Если облачная оболочка по-прежнему открыта, вам не нужно копировать/запускать первую строку (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```