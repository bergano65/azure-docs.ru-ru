---
title: Настройка предварительных и последующей скриптов в развертывании Управление обновлениями в Azure
description: В этой статье описывается настройка и управление предварительными и последующими скриптами для развертываний обновлений.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a5d68b64fc24577621c82be62f833c356e8fb9c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850250"
---
# <a name="manage-pre-and-post-scripts"></a>Управление предварительными и последующей сценариями

Предварительные и посторонние скрипты позволяют запускать модули Runbook PowerShell в учетной записи службы автоматизации Azure до (до задачи) и после (после задачи) развертывания обновлений. Сценарии pre и POST выполняются в контексте Azure, а не локально. Предварительные сценарии выполняются в начале развертывания обновлений. Выполняемые после выполнения сценариев выполняются в конце развертывания и после всех настроенных перезагрузок.

## <a name="runbook-requirements"></a>Требования к модулям Runbook

Чтобы модуль Runbook использовался в качестве предварительного или поступающего скрипта, модуль Runbook необходимо импортировать в учетную запись службы автоматизации и опубликовать. Дополнительные сведения об этом процессе см. в статье [Публикация модуля Runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Использование сценария предварительного или последующей процедуры

Чтобы использовать сценарий предварительного или последующей публикации в развертывании обновлений, начните с создания развертывания обновлений. Выберите **pre-Scripts + POST-Scripts**. Откроется страница **Выбор сценариев предварительного и последующего выполнения**.

![Выбор сценариев](./media/pre-post-scripts/select-scripts.png)

Выберите скрипт, который вы хотите использовать. В этом примере мы используем модуль Runbook **упдатеманажемент-турнонвмс** . При выборе модуля Runbook открывается страница **Настройка скрипта** . Выберите **предварительный сценарий**и нажмите кнопку **ОК**.

Повторите эту процедуру для сценария **UpdateManagement-TurnOffVms**. Но при выборе **типа скрипта**выберите пункт **после скрипта**.

В разделе **Выбранные элементы** теперь будут показаны выбранные сценарии. Один из них — предварительный сценарий, а второй — сценарий после выполнения:

![Выбранные элементы](./media/pre-post-scripts/selected-items.png)

Завершите настройку развертывания обновлений.

После завершения развертывания обновлений можно перейти к разделу **обновления развертывания** , чтобы просмотреть результаты. Как видите, состояние указывается для сценариев предварительного и последующей:

![Результаты обновления](./media/pre-post-scripts/update-results.png)

После выбора запуска развертывания обновлений вы видите дополнительные сведения о предварительных и последующих сценариях. В открывшемся окне будет ссылка на код сценария, который выполнялся.

![Результаты развертывания](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Передача параметров

При настройке предварительных и последующего сценариев можно передавать параметры, как и планирование модуля Runbook. Параметры определяются при создании развертывания обновлений. Сценарии pre и POST поддерживают следующие типы:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Если вам нужен другой тип объекта, вы можете привести его к другому типу с помощью собственной логики runbook.

В дополнение к стандартным параметрам Runbook предоставляется другой параметр: **софтвареупдатеконфигуратионрунконтекст**

Этот параметр является строкой JSON, и при определении параметра в предварительном или последующем скрипте он автоматически передается развертыванием обновлений. Параметр содержит сведения о развертывании обновления, которое является подмножеством сведений, возвращаемых [API софтвареупдатеконфигуратионс](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). 

В следующей таблице показаны свойства, указанные в переменной.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Свойства SoftwareUpdateConfigurationRunContext

|Свойство  |Описание  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Имя конфигурации обновления программного обеспечения.        |
|SoftwareUpdateConfigurationRunId     | Уникальный идентификатор для запуска.        |
|SoftwareUpdateConfigurationSettings     | Коллекция свойств, связанных с конфигурацией обновления программного обеспечения.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Операционные системы, предназначенные для развертывания обновлений.         |
|SoftwareUpdateConfigurationSettings.duration     | Максимальная продолжительность выполнения развертывания обновления `PT[n]H[n]M[n]S`а в соответствии с ISO8601; также называется *окном обслуживания*.          |
|SoftwareUpdateConfigurationSettings.Windows     | Коллекция свойств, связанных с компьютерами Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Список KBs, исключаемых из развертывания обновлений.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Классификации обновлений, выбранные для развертывания обновлений.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Параметры перезагрузки для развертывания обновлений.        |
|azureVirtualMachines     | Список идентификаторы ресурсов для виртуальных машин Azure в развертывании обновлений.        |
|nonAzureComputerNames|Список полных доменных имен компьютеров, не являющихся Azure, в развертывании обновлений.|

В следующем примере приведена строка JSON, передаваемая в параметре **SoftwareUpdateConfigurationRunContext**.

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Полный пример со всеми свойствами можно найти в: [получение конфигурации обновления программного обеспечения по имени](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Объект `SoftwareUpdateConfigurationRunContext` может содержать дублирующиеся записи для компьютеров. Это может привести к тому, что предварительные и выполняемые сценарии выполняются несколько раз на одном компьютере. Чтобы обойти это поведение, используйте `Sort-Object -Unique`, чтобы выбрать в скрипте только уникальные имена виртуальных машин.


## <a name="stopping-a-deployment"></a>Остановка развертывания

Если требуется отключить развертывание на основе предварительного скрипта, необходимо [создать](automation-runbook-execution.md#throw) исключение. В противном случае развертывание и последующей сценарий все равно будут выполняться. В следующем фрагменте кода показано, как создать исключение.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="samples"></a>Примеры

Примеры для сценариев предварительного и последующей публикации можно найти в [коллекции центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) и [коллекция PowerShell](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), либо можно импортировать их с помощью портал Azure. Для этого в учетной записи службы автоматизации в разделе **Автоматизация процессов**выберите **коллекция модулей Runbook**. В качестве фильтра укажите **Управление обновлениями**.

![Список в коллекции](./media/pre-post-scripts/runbook-gallery.png)

Можно также найти их по имени сценария, как показано в следующем списке:

* Update Management - Turn On VMs (Управление обновлениями — включить виртуальные машины).
* Update Management - Turn Off VMs (Управление обновлениями — выключить виртуальные машины).
* Update Management - Run Script Locally (Управление обновлениями — локальный запуск сценария).
* Update Management - Template for Pre/Post Scripts (Управление обновлениями — шаблон для скриптов предварительного и последующего выполнения).
* Update Management - Run Script with Run Command (Управление обновлениями — запуск сценария командой Run).

> [!IMPORTANT]
> После импорта модулей Runbook их необходимо опубликовать, прежде чем их можно будет использовать. Для этого найдите модуль Runbook в учетной записи службы автоматизации, выберите **изменить**и нажмите кнопку **опубликовать**.

Все примеры основаны на базовом шаблоне, который определен в следующем примере. Этот шаблон можно использовать для создания собственного модуля Runbook для использования с предварительными и последующими скриптами. Включается необходимая логика для проверки подлинности в Azure и обработки параметра `SoftwareUpdateConfigurationRunContext`.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>Взаимодействие с компьютерами

Задачи, выполняемые до и после выполнения задач, выполняются как модуль Runbook в учетной записи службы автоматизации, а не на компьютерах в развертывании. Задачи, выполняемые до и после выполнения, также выполняются в контексте Azure и не имеют доступа к компьютерам, не являющимся машинами Azure. В следующих разделах показано, как можно напрямую взаимодействовать с компьютерами независимо от того, являются ли они виртуальными машинами Azure или компьютерами, не являющимися Azure.

### <a name="interacting-with-azure-machines"></a>Взаимодействие с компьютерами Azure

Задачи, выполняемые до и после выполнения задач, выполняются в виде модулей Runbook и не запускаются на виртуальных машинах Azure в развертывании. Для взаимодействия с виртуальными машинами Azure необходимо иметь следующие элементы:

* учетная запись запуска от имени;
* Runbook, который требуется запустить

Для взаимодействия с машинами Azure следует использовать командлет [Invoke-азурермвмрункомманд](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) для взаимодействия с виртуальными машинами Azure. Пример того, как это сделать, см. в примере модуля Runbook пример [Управление обновлениями — запуск скрипта с помощью команды Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Взаимодействие с компьютерами, не являющимися машинами Azure

Задачи, выполняемые до и после выполнения задач, выполняются в контексте Azure и не имеют доступа к компьютерам, не являющимся компьютерами Azure. Для взаимодействия с компьютерами, не являющимися машинами Azure, необходимо иметь следующие элементы:

* учетная запись запуска от имени;
* установленная на компьютере гибридная рабочая роль Runbook;
* модуль Runbook, который будет запускаться локально;
* Родительский Runbook

Для взаимодействия с компьютерами, не являющимися машинами Azure, родительский модуль Runbook выполняется в контексте Azure. Он вызывает дочерний модуль Runbook с помощью командлета [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Ему нужно передать параметр `-RunOn` и имя гибридной рабочей роли Runbook, которая будет выполнять сценарий. Дополнительные сведения см. в примере модуля Runbook [Управление обновлениями — запуск скрипта локально](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Отмена развертывания исправлений

Если предварительный сценарий возвращает ошибку, может потребоваться отменить развертывание. Для этого необходимо [вызвать](/powershell/module/microsoft.powershell.core/about/about_throw) ошибку в скрипте для любой логики, которая может привести к сбою.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Известные проблемы

* Нельзя передавать логические значения, объекты или массивы в параметры при использовании предварительных и позавершающих скриптов. В противном случае произойдет сбой модуля Runbook. Полный список поддерживаемых типов см. в разделе [Передача параметров](#passing-parameters).

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как управлять обновлениями для виртуальных машин Windows, перейдите к следующему учебнику:

> [!div class="nextstepaction"]
> [Устранение неполадок при изменениях в среде](automation-tutorial-update-management.md)

