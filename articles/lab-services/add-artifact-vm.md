---
title: Добавление артефакта к виртуальной Машине в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как Добавление артефакта к виртуальной машине в лаборатории в Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 5e6a7cbc070d81de33fac07a89dabf2b469bd355
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450091"
---
# <a name="add-an-artifact-to-a-vm"></a>Добавление артефакта к виртуальной Машине
При создании виртуальной Машины, к нему можно добавить существующие артефакты. Артефактами могут быть либо из [общедоступном репозитории DevTest Labs Git](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) или из репозитория Git. В этой статье показано, как добавить артефакты на портале Azure, а также с помощью Azure PowerShell. 

*Артефакты* Azure DevTest Labs позволяют указывать *действия*, которые выполняются при подготовке виртуальной машины, включая запуск скриптов Windows PowerShell, выполнение команд Bash и установку программного обеспечения. *Параметры* артефакта позволяют настроить артефакт для конкретной ситуации.

Дополнительные сведения о том, как создавать пользовательские артефакты, см. в статье: [Создание пользовательских артефактов](devtest-lab-artifact-author.md).

## <a name="use-azure-portal"></a>Использование портала Azure 
1. Войдите на [портале Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Щелкните **Все службы** и выберите в списке **DevTest Labs**.
1. Из списка лабораторий выберите ту, где содержится виртуальная машина, с которой вы будете работать.  
1. Выберите **My virtual machines** (Мои виртуальные машины).
1. Выберите нужную виртуальную машину.
1. Выберите **Управление артефактами**. 
1. Выберите **Apply artifacts** (Применить артефакты).
1. В области **Применение артефактов** выберите артефакт для добавления к виртуальной машине.
1. В области **Добавление артефакта** введите значения обязательных параметров и другие параметры по желанию.  
1. Выберите **Добавить**, чтобы добавить артефакт и вернуться в область **Применение артефактов**.
1. Продолжайте добавлять артефакты, необходимые для виртуальной машины.
1. После добавления артефактов можно [изменить порядок, в котором они выполняются](#change-the-order-in-which-artifacts-are-run). Также можно вернуться к [просмотру или изменению артефакта](#view-or-modify-an-artifact).
1. После добавления артефактов выберите **Применить**.

### <a name="change-the-order-in-which-artifacts-are-run"></a>Изменение порядка выполнения артефактов
По умолчанию действия артефактов выполняются в порядке их добавления на виртуальную машину. Ниже показано, как изменить порядок, в котором выполняются артефакты.

1. В верхней части области **Применение артефактов** щелкните ссылку, указывающую количество добавленных к виртуальной машине артефактов.
   
    ![Количество артефактов, добавленных к виртуальной машине](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. В области **Выбранные артефакты** расположите артефакты в нужном порядке с помощью перетаскивания. Если у вас возникли проблемы при перетаскивании артефактов, убедитесь, что вы перетаскиваете их за левую сторону. 
1. По окончании нажмите кнопку **ОК** .  

### <a name="view-or-modify-an-artifact"></a>просмотру или изменению артефакта
Для просмотра или изменения параметров артефакта выполните следующие действия:

1. В верхней части области **Применение артефактов** щелкните ссылку, указывающую количество добавленных к виртуальной машине артефактов.
   
    ![Количество артефактов, добавленных к виртуальной машине](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. В области **Выбранные артефакты** выберите артефакт, который нужно просмотреть или изменить.  
1. В области **Добавление артефакта** внесите необходимые изменения. Затем нажмите кнопку **ОК**, чтобы закрыть область **Добавление артефакта**.
1. Нажмите кнопку **ОК**, чтобы закрыть область **Выбранные артефакты**.

## <a name="use-powershell"></a>Использование PowerShell
Следующий сценарий применяется заданного артефакта для указанной виртуальной Машины. [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction?view=azurermps-6.13.0) команда является тот, который выполняет операцию.  

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzureRmResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzureRmResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzureRmResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzureRmResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Дальнейшие действия
На артефакты, ознакомьтесь со следующими статьями:

- [Укажите обязательные артефакты для лаборатории](devtest-lab-mandatory-artifacts.md)
- [Создание настраиваемых артефактов](devtest-lab-artifact-author.md)
- [Добавить репозиторий артефактов в лабораторию](devtest-lab-artifact-author.md)
- [Диагностика сбоев артефактов](devtest-lab-troubleshoot-artifact-failure.md)