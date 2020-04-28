---
title: Запуск и завершение работы виртуальных машин с помощью программ командной строки Azure DevTest Labs
description: Узнайте, как использовать программы командной строки для запуска и завершения работы виртуальных машин в Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169253"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Использование программ командной строки для запуска и завершения Azure DevTest Labs виртуальных машин
В этой статье показано, как использовать Azure PowerShell или Azure CLI для запуска или завершения работы виртуальных машин в лаборатории в Azure DevTest Labs. Вы можете создать скрипты PowerShell/CLI для автоматизации этих операций. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Обзор
Azure DevTest Labs — это способ создания быстрых, простых и экономичных сред разработки и тестирования. Она позволяет управлять затратами, быстро подготавливать виртуальные машины и сокращать отходы.  В портал Azure есть встроенные функции, позволяющие настраивать виртуальные машины в лаборатории для автоматического запуска и завершения в определенное время. 

Однако в некоторых сценариях может потребоваться автоматизировать запуск и остановку виртуальных машин из скриптов PowerShell или CLI. Она обеспечивает некоторую гибкость при запуске и остановке отдельных компьютеров в любое время, а не в определенное время. Ниже приведены некоторые ситуации, в которых выполнение этих задач с помощью сценариев было бы полезным.

- При использовании трехуровневого приложения в рамках тестовой среды необходимо запустить уровни в последовательности. 
- Отключите виртуальную машину при соблюдении настраиваемых условий для экономии денег. 
- Используйте его в качестве задачи в рабочем процессе CI/CD для запуска в начале потока, используйте виртуальные машины как компьютеры сборки, тестовые машины или инфраструктуру, после чего завершите работу виртуальных машин после завершения процесса. Примером этого может быть настраиваемая фабрика изображений с Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> В следующем скрипте используется модуль Azure PowerShell AZ. 

Следующий сценарий PowerShell запускает виртуальную машину в лаборатории. [Командлет Invoke-азресаурцеактион](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) является основным фокусом для этого скрипта. Параметр **ResourceId** — это полный идентификатор ресурса для виртуальной машины в лаборатории. Параметр **Action** — это место, где задаются параметры **запуска** или **отмены** в зависимости от того, что требуется.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) — еще один способ автоматизации запуска и остановки виртуальных машин DevTest Labs. Azure CLI можно [установить](/cli/azure/install-azure-cli?view=azure-cli-latest) в разных операционных системах. Следующий скрипт предоставляет команды для запуска и остановки виртуальной машины в лаборатории. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Дальнейшие действия
Сведения об использовании портал Azure для выполнения этих операций см. в следующей статье: [перезапуск виртуальной машины](devtest-lab-restart-vm.md).
