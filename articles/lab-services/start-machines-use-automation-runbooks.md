---
title: Запуск машин с использованием runbooks автоматизации в Лабораториях Azure DevTest
description: Узнайте, как запускать виртуальные машины в лаборатории лаборатории Azure DevTest Labs с помощью runbooks Azure Automation.
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
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166308"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Запустите виртуальные машины в лаборатории в порядке, используя runbooks Azure Automation
Функция [автоматического запуска](devtest-lab-set-lab-policy.md#set-autostart) DevTest Labs позволяет настроить ВМ для автоматического запуска в указанное время. Однако эта функция не поддерживает запуск машин в определенном порядке. Существует несколько сценариев, в которых такой тип автоматизации будет полезен.  Один из сценариев, где Jumpbox VM в лаборатории должен быть запущен во-первых, до других VMs, как Jumpbox используется в качестве точки доступа к другим VMs.  В этой статье показано, как настроить учетную запись Azure Automation с помощью runbook PowerShell, который выполняет сценарий. Скрипт использует теги на VMs в лаборатории, чтобы позволить вам контролировать заказ запуска без необходимости менять сценарий.

## <a name="setup"></a>Настройка
В этом примере в том, что в лаборатории необходимо добавить тег **StartupOrder** с соответствующим значением (0,1,2 и т.д.). Обозначьте любую машину, которая не должна быть запущена как -1.

## <a name="create-an-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure
Создайте учетную запись Azure Automation, следуя инструкциям в [этой статье.](../automation/automation-create-standalone-account.md) Выберите опцию **Run As Accounts** при создании учетной записи. После создания учетной записи автоматизации откройте страницу **модулей** и выберите **обновления модулей Azure** в панели меню. Модули по умолчанию несколько старых версий и без обновления скрипт может не функционировать.

## <a name="add-a-runbook"></a>Добавить книгу
Теперь, чтобы добавить книгу в учетную запись автоматизации, выберите **Runbooks** в левом меню. Выберите **книгу добавления** в меню и следуйте инструкциям для [создания runbook PowerShell.](../automation/automation-first-runbook-textual-powershell.md)

## <a name="powershell-script"></a>Скрипт PowerShell
Следующий скрипт принимает имя подписки, название лаборатории в качестве параметров. Поток скрипта заключается в том, чтобы получить все vMs в лаборатории, а затем разобрать информацию о тегах, чтобы создать список имен VM и их запуск заказа. Скрипт проходит через VMs в порядке и запускает VMs. Если в определенном номере заказа имеется несколько вмхом, они начинаются асинхронно с помощью заданий PowerShell. Для тех VMs, которые не имеют тега, установить значение запуска, чтобы быть последним (10), они будут запущены последними, по умолчанию.  Если лаборатория не хочет, чтобы VM был автоматически запущен, установите значение тегов до 11, и оно будет проигнорировано.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Создание расписания
Чтобы этот скрипт выполнялся ежедневно, [создайте расписание](../automation/shared-resources/schedules.md#creating-a-schedule) в учетной записи автоматизации. Как только расписание создано, [свяжите его с runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

В крупномасштабной ситуации, когда существует несколько подписок с несколькими лабораториями, храните информацию о параметрах в файле для разных лабораторий и передайте файл скрипту вместо отдельных параметров. Сценарий должен быть изменен, но выполнение ядра будет таким же. Хотя этот пример использует автоматизацию Azure для выполнения скрипта PowerShell, существуют и другие варианты, например, использование задачи в конвейере Build/Release.

## <a name="next-steps"></a>Дальнейшие действия
Подробнее об автоматизации Azure: введение в [автоматизацию Azure](../automation/automation-intro.md)читайте в следующей статье.
