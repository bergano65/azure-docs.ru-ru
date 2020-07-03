---
title: Управление предварительными скриптами и пост-скриптами в развертывании Управление обновлениями в Azure
description: В этой статье описывается настройка и управление предварительными и последующими скриптами для развертываний обновлений.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: f55ebb3270fdd97a1fdbbf5a56f9703c08933f9f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855337"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Управление предварительными и пост-скриптами

Предварительные и посылаемые скрипты — это модули Runbook для запуска в учетной записи службы автоматизации Azure до (до задачи) и после (после задачи) развертывания обновлений. Предварительные и посторонние скрипты выполняются в контексте Azure, а не локально. Предварительные сценарии выполняются в начале развертывания обновлений. Выполняемые после выполнения сценариев выполняются в конце развертывания и после всех настроенных перезагрузок.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="pre-script-and-post-script-requirements"></a>Требования к предварительному и завершающему сценариям

Чтобы модуль Runbook использовался как сценарий предварительного или последующей публикации, его необходимо импортировать в учетную запись службы автоматизации и [опубликовать](manage-runbooks.md#publish-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Параметры перед скриптом и после него

При настройке предварительных и последующей скриптов можно передавать параметры, как и планирование модуля Runbook. Параметры определяются при создании развертывания обновлений. Перед скриптами и после них поддерживаются следующие типы:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Параметры Runbook перед скриптом и после него не поддерживают логические типы, объекты и массивы. Эти значения приводят к сбою модулей Runbook. 

Если вам нужен другой тип объекта, вы можете привести его к другому типу с помощью собственной логики runbook.

В дополнение к стандартным параметрам Runbook предоставляется `SoftwareUpdateConfigurationRunContext` параметр (тип строки JSON). При определении параметра в модуле Runbook, выполняемом перед сценарием или после него, он автоматически передается развертыванием обновлений. Параметр содержит сведения о развертывании обновления, которое является подмножеством сведений, возвращаемых [API софтвареупдатеконфигуратионс](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). В разделах ниже определяются связанные свойства.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Свойства SoftwareUpdateConfigurationRunContext

|Свойство  |Описание  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Имя конфигурации обновления программного обеспечения.        |
|SoftwareUpdateConfigurationRunId     | Уникальный идентификатор для запуска.        |
|SoftwareUpdateConfigurationSettings     | Коллекция свойств, связанных с конфигурацией обновления программного обеспечения.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Операционные системы, предназначенные для развертывания обновлений.         |
|SoftwareUpdateConfigurationSettings.duration     | Максимальная длительность развертывания обновления выполняется как `PT[n]H[n]M[n]S` для каждого ISO8601; также называется окном обслуживания.          |
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
> `SoftwareUpdateConfigurationRunContext` Объект может содержать дублирующиеся записи для компьютеров. Это может привести к многократному запуску сценариев и сценариев, выполняемых на одном и том же компьютере. Чтобы обойти это поведение, используйте `Sort-Object -Unique` для выбора только уникальных имен виртуальных машин.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Использование сценария предварительного или последующей процедуры в развертывании

Чтобы использовать сценарий предварительного или последующей публикации в развертывании обновлений, начните с создания развертывания обновлений. Выберите **pre-Scripts + POST-Scripts**. Откроется страница **Выбор сценариев предварительного и последующего выполнения**.

![Выбор сценариев](./media/pre-post-scripts/select-scripts.png)

Выберите скрипт, который вы хотите использовать. В этом примере мы используем модуль Runbook **упдатеманажемент-турнонвмс** . При выборе модуля Runbook открывается страница **Настройка скрипта** . Выберите **предварительный сценарий**и нажмите кнопку **ОК**.

Повторите эту процедуру для сценария **UpdateManagement-TurnOffVms**. Но при выборе **типа скрипта**выберите пункт **после скрипта**.

В разделе **Выбранные элементы** теперь будут показаны выбранные сценарии. Один из них — предварительный сценарий, а второй — сценарий после выполнения:

![Выбранные элементы](./media/pre-post-scripts/selected-items.png)

Завершите настройку развертывания обновлений.

После завершения развертывания обновлений можно перейти к разделу **обновления развертывания** , чтобы просмотреть результаты. Как видите, состояние указывается для сценариев предварительного и последующей:

![Результаты обновления](./media/pre-post-scripts/update-results.png)

Выбрав Запуск развертывания обновлений, вы видите дополнительные сведения о предварительных скриптах и последующих сценариях. В открывшемся окне будет ссылка на код сценария, который выполнялся.

![Результаты развертывания](./media/pre-post-scripts/deployment-run.png)

ES в скрипте.

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



## <a name="interacting-with-machines"></a>Взаимодействие с компьютерами

Предварительные и выполняемые задачи выполняются в качестве модулей Runbook в учетной записи службы автоматизации, а не непосредственно на компьютерах в развертывании. Задачи, выполняемые до и после задач, также выполняются в контексте Azure и не имеют доступа к компьютерам, не являющимся компьютерами Azure. В следующих разделах показано, как можно напрямую взаимодействовать с компьютерами независимо от того, являются ли они виртуальными машинами Azure или компьютерами, не являющимися Azure.

### <a name="interact-with-azure-machines"></a>Взаимодействие с компьютерами Azure

Задачи, выполняемые до и после выполнения задач, выполняются как модули Runbook и не запускаются на виртуальных машинах Azure в развертывании. Для взаимодействия с виртуальными машинами Azure необходимо иметь следующие элементы:

* учетная запись запуска от имени;
* Runbook, который требуется запустить

Для взаимодействия с машинами Azure следует использовать командлет [Invoke-азвмрункомманд](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) для взаимодействия с виртуальными машинами Azure. Пример того, как это сделать, см. в примере модуля Runbook пример [Управление обновлениями — запуск скрипта с помощью команды Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interact-with-non-azure-machines"></a>Взаимодействие с компьютерами, не являющимися машинами Azure

Задачи, выполняемые перед задачами и после них, выполняются в контексте Azure и не имеют доступа к компьютерам, не являющимся компьютерами Azure. Для взаимодействия с компьютерами, не являющимися машинами Azure, необходимо иметь следующие элементы:

* учетная запись запуска от имени;
* установленная на компьютере гибридная рабочая роль Runbook;
* модуль Runbook, который будет запускаться локально;
* Родительский Runbook

Для взаимодействия с компьютерами, не являющимися машинами Azure, родительский модуль Runbook выполняется в контексте Azure. Этот модуль Runbook вызывает дочерний модуль Runbook с помощью командлета [Start-азаутоматионрунбук](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) . Ему нужно передать параметр `RunOn` и имя гибридной рабочей роли Runbook, которая будет выполнять сценарий. См. пример модуля Runbook [Управление обновлениями — запуск скрипта локально](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="aborting-patch-deployment"></a>Прерывание развертывания исправлений

Если предварительный сценарий возвращает ошибку, может потребоваться отменить развертывание. Для этого необходимо [вызвать](/powershell/module/microsoft.powershell.core/about/about_throw) ошибку в скрипте для любой логики, которая может привести к сбою.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Примеры

Примеры для предварительных скриптов и сценариев POST можно найти в [коллекции центра скриптов](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) , в [коллекция PowerShell](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)или импортировать их с помощью портал Azure. Для этого в учетной записи службы автоматизации в разделе **Автоматизация процессов**выберите **коллекция модулей Runbook**. В качестве фильтра укажите **Управление обновлениями**.

![Список в коллекции](./media/pre-post-scripts/runbook-gallery.png)

Можно также найти их по имени сценария, как показано в следующем списке:

* Update Management - Turn On VMs (Управление обновлениями — включить виртуальные машины).
* Update Management - Turn Off VMs (Управление обновлениями — выключить виртуальные машины).
* Update Management - Run Script Locally (Управление обновлениями — локальный запуск сценария).
* Update Management - Template for Pre/Post Scripts (Управление обновлениями — шаблон для скриптов предварительного и последующего выполнения).
* Update Management - Run Script with Run Command (Управление обновлениями — запуск сценария командой Run).

> [!IMPORTANT]
> После импорта модулей Runbook их необходимо опубликовать, прежде чем их можно будет использовать. Для этого найдите модуль Runbook в учетной записи службы автоматизации, выберите **изменить**и нажмите кнопку **опубликовать**.

Все примеры основаны на базовом шаблоне, который определен в следующем примере. Этот шаблон можно использовать для создания собственного модуля Runbook для использования с скриптами предварительного и последующей. Включается необходимая логика для проверки подлинности в `SoftwareUpdateConfigurationRunContext` Azure и обработки параметра.

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

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Для неграфических модулей Runbook PowerShell `Add-AzAccount` и `Add-AzureRMAccount` являются псевдонимами для [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Вы можете использовать эти командлеты или [обновить модули](automation-update-azure-modules.md) в учетной записи службы автоматизации до последних версий. Обновление модулей может потребоваться, даже если учетная запись службы автоматизации только что создана.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как управлять обновлениями для виртуальных машин Windows, перейдите к следующему учебнику:

> [!div class="nextstepaction"]
> [Устранение неполадок при изменениях в среде](automation-tutorial-update-management.md)