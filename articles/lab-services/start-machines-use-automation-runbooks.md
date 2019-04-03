---
title: Запуск машин с помощью модулей Runbook службы автоматизации в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как для запуска виртуальных машин в лаборатории в Azure DevTest Labs с помощью модулей Runbook службы автоматизации Azure.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: d80328943ae818b3bad9c0a275b74968ee33d4b7
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887252"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Запуск виртуальных машин в лаборатории в порядке с помощью модулей Runbook службы автоматизации Azure
[Автозапуска](devtest-lab-set-lab-policy.md#set-autostart) особенностью DevTest Labs позволяет настроить виртуальные машины для автоматического запуска в указанное время. Тем не менее эта функция не поддерживает машины для запуска в определенном порядке. Существует несколько сценариев, где может потребоваться автоматизацию такого рода.  Один из сценариев — где виртуальной Машине Jumpbox в лаборатории должна быть запущена во-первых, перед другим виртуальным машинам, поскольку Jumpbox используется как точка доступа к другим виртуальным машинам.  В этой статье показано, как настроить учетную запись службы автоматизации Azure модуль Runbook PowerShell, выполняющий скрипт. Сценарий использует теги на виртуальных машинах в лабораторной среде, чтобы можно было управлять порядком запуска без изменения сценария.

## <a name="setup"></a>Настройка
В этом примере виртуальные машины в лаборатории нужно иметь тег **StartupOrder** добавлены соответствующим значением (0,1,2, и т.д.). Назначьте любой компьютер, не должны быть запущены как -1.

## <a name="create-an-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure
Создание учетной записи службы автоматизации Azure, следуя указаниям в [в этой статье](../automation/automation-create-standalone-account.md). Выберите **Run As Accounts** параметр при создании учетной записи. После создания учетной записи автоматизации откройте **модули** страницы и выберите **обновить модули Azure** в строке меню. По умолчанию модули — это несколько версий старые и без обновления скрипта может не работать.

## <a name="add-a-runbook"></a>Добавить runbook
Теперь, чтобы добавить модуль runbook в учетной записи службы автоматизации, выберите **ранбуки** в меню слева. Выберите **добавить runbook** на меню и следуйте инструкциям для [создания PowerShell runbook](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Сценарий PowerShell
Следующий скрипт принимает имя подписки, имя лаборатории в качестве параметров. Поток скрипта — получить все виртуальные машины в лаборатории, а затем проанализировать сведения тег, чтобы создать список имен виртуальных Машин и их порядок запуска. Скрипт проходит через виртуальные машины в порядке, а также запускает виртуальные машины. Если есть несколько виртуальных машин в определенного номера заказа, они запускаются асинхронно с помощью заданий PowerShell. Для этих виртуальных машин, у которых нет тега, значение для запуска набора последним (10), они будет запущен последний, по умолчанию.  Если Лаборатория не удалось автоматически запустить виртуальную Машину, значение тега равно 11, и он будет проигнорирован.

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
$dtLab = Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzureRmResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

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
Save-AzureRmContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzureRmContext -Path ($profilePath)
    Invoke-AzureRmResourceAction `
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
        $returnStatus = Invoke-AzureRmResourceAction `
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
Чтобы этот сценарий выполнения ежедневно, [создать расписание](../automation/shared-resources/schedules.md#creating-a-schedule) в учетной записи службы автоматизации. После создания расписания [свяжите его с runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

В крупномасштабных ситуации, где имеется несколько подписок с помощью нескольких лабораторных занятий, хранятся сведения о параметрах в файле для разных лабораторий и передать файл сценария, а не отдельных параметров. Скрипт необходимо изменить, но исполнение на ядрах останется прежним. Хотя этот пример использует службы автоматизации Azure для выполнения сценария PowerShell, есть другие варианты, например с помощью задачи в конвейере сборки или выпуска.

## <a name="next-steps"></a>Дальнейшие действия
Обратитесь к следующей статье, чтобы узнать больше о службе автоматизации Azure: [Общие сведения о службе автоматизации Azure](../automation/automation-intro.md).
