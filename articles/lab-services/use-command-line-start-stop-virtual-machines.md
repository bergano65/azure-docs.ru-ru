---
title: Используйте инструменты командной строки для запуска и остановки VMs Azure DevTest Labs
description: Узнайте, как использовать инструменты командной строки для запуска и остановки виртуальных машин в Azure DevTest Labs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169253"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Используйте инструменты командной строки для запуска и остановки виртуальных машин Azure DevTest Labs
В этой статье показано, как использовать Azure PowerShell или Azure CLI для запуска или остановки виртуальных машин в лаборатории лаборатории Azure DevTest Labs. Для автоматизации этих операций можно создать скрипты PowerShell/CLI. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Обзор
Azure DevTest Labs — это способ создания быстрых, простых и худых сред для разработчиков и испытаний. Это позволяет управлять затратами, быстро предоставлять VMs и минимизировать отходы.  На портале Azure есть встроенные функции, позволяющие настроить ВМ в лаборатории для автоматического запуска и остановки в определенное время. 

Однако в некоторых сценариях может потребоваться автоматизировать запуск и остановку VMs из скриптов PowerShell/CLI. Это дает вам некоторую гибкость при запуске и остановке отдельных машин в любое время, а не в определенное время. Вот некоторые из ситуаций, в которых выполнение этих задач с помощью скриптов было бы полезно.

- При использовании 3-уровневого приложения в тестовой среде уровни необходимо запускать в последовательности. 
- Выключите VM, когда пользовательские критерии выполнены, чтобы сэкономить деньги. 
- Используйте его в качестве задачи в процессе CI/CD, чтобы начать в начале потока, используйте VMs в качестве машин сборки, тестовых машин или инфраструктуры, а затем остановите ВМ по завершении процесса. Примером этого может быть фабрика пользовательских изображений с Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> В следующем скрипте используется модуль Azure PowerShell Az. 

Следующий скрипт PowerShell запускает VM в лаборатории. Основное внимание в этом [скрипте уделяется invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) Параметр **ResourceId** является полностью квалифицированным идентификатором ресурса для VM в лаборатории. Параметр **действия** — это место, где параметры **«Пуск»** или **«Стоп»** устанавливаются в зависимости от того, что необходимо.

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
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) — это еще один способ автоматизации запуска и остановки VMs DevTest Labs. Azure CLI можно [устанавливать](/cli/azure/install-azure-cli?view=azure-cli-latest) на различные операционные системы. Следующий скрипт дает вам команды для запуска и остановки VM в лаборатории. 

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
См. следующую статью для использования портала Azure для выполнения следующих [операций: Перезапуск VM](devtest-lab-restart-vm.md).
