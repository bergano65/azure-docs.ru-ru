---
title: Использование средств командной строки для запуска и остановки виртуальных машин Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как использовать средства командной строки для запуска и остановки виртуальных машин в Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 51c45fdb0c96e84d3f37f485279aa805361f3818
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798935"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Использование средств командной строки для запуска и остановки виртуальных машин Azure DevTest Labs
В этой статье показано, как использовать Azure PowerShell или интерфейса командной строки Azure для запуска или остановки виртуальных машин в лаборатории в Azure DevTest Labs. Можно создать сценарии PowerShell или интерфейса командной строки для автоматизации этих операций. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Обзор
Azure DevTest Labs — это способ создания сред разработки и тестирования, быстрые, простые и экономичные. Он позволяет управление затратами, быстро подготовить виртуальные машины и свести к минимуму потерь.  Существуют встроенные функции на портале Azure, которые дают возможность настроить виртуальные машины в лаборатории для автоматического запуска и остановки в указанное время. 

Однако в некоторых случаях можно автоматизировать запуск и остановка виртуальных машин с помощью сценариев PowerShell или интерфейса командной строки. Оно дает некоторую свободу с запуск и остановка отдельных машин в любое время, а не в указанное время. Ниже приведены некоторые из ситуаций, в какие выполнение этих задач с помощью сценариев может быть полезно.

- При использовании 3-уровневое приложение как часть тестовой среде, уровни должны быть запущены в последовательности. 
- Отключите виртуальную Машину, при достижении пользовательских критериев, чтобы сэкономить деньги. 
- Используйте его как задачи в рабочем процессе Непрерывной интеграции и начать с начала потока, используйте виртуальные машины в качестве компьютеров построения, тестирования машинами или инфраструктурой, а затем остановить виртуальные машины, когда процесс будет завершен. Примером этого может служить фабрики пользовательского образа с помощью Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell
Следующий сценарий PowerShell запускает виртуальную Машину в лаборатории. [Вызвать AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azurermps-6.13.0) является основной целью для этого скрипта. **ResourceId** параметр — это полный идентификатор ресурса для виртуальной Машины в лаборатории. **Действие** параметр именно **запустить** или **остановить** параметры задаются в зависимости от необходимых.

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
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

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


## <a name="azure-cli"></a>Инфраструктура CLI Azure
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) — еще один способ автоматизировать запуск и остановка виртуальных машин DevTest Labs. Azure CLI может быть [установлен](/cli/azure/install-azure-cli?view=azure-cli-latest) в различных операционных системах. Следующий скрипт предоставляет команды для запуска и остановки виртуальной Машины в лаборатории. 

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
См. в следующей статье на портале Azure для выполнения этих операций: [Перезапуск виртуальной Машины](devtest-lab-restart-vm.md).
