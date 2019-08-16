---
title: Настройка сценариев pre и POST в развертывании Управление обновлениями в Azure
description: В этой статье описывается настройка сценариев предварительного и последующего выполнения, а также управление этими сценариями.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f13851dd43c80a63ec628e04b98271894c15afc0
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542864"
---
# <a name="manage-pre-and-post-scripts"></a>Управление пред и POST Scripts

Сценарии предварительного и последующего выполнения позволяют запускать модули Runbook PowerShell в учетной записи Службы автоматизации до и после задач развертывания обновлений. Такие сценарии выполняются в контексте Azure, а не в локальной среде. Предварительные сценарии выполняются в начале развертывания обновлений. Запись сценариев запускается в конце развертывания и после каждой настроенной перезагрузки.

## <a name="runbook-requirements"></a>Требования к модулям Runbook

Чтобы модуль Runbook можно было использовать в качестве сценария, выполняемого перед развертыванием или после него, модуль следует импортировать в учетную запись Службы автоматизации и опубликовать. Дополнительные сведения об этом процессе см. [здесь](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Использование сценария, выполняемого перед развертыванием или после него

Чтобы использовать сценарий предварительного или последующего выполнения, прежде всего создайте развертывание обновлений. Выберите **Предварительные скрипты + POST-скрипты**. Откроется страница **Выбор сценариев предварительного и последующего выполнения**.  

![Выбор сценариев](./media/pre-post-scripts/select-scripts.png)

Выберите нужный сценарий. Например, в этом примере выбран модуль Runbook **UpdateManagement-TurnOnVms**. При выборе модуля Runbook откроется страница **Настройка скрипта** , выберите **предварительный сценарий**. Когда закончите, нажмите кнопку **ОК**.

Повторите эту процедуру для сценария **UpdateManagement-TurnOffVms**. Но теперь для параметра **Тип сценария** выберите **Сценарий последующего выполнения**.

После этого в разделе **Выбранные элементы** отобразятся оба выбранных сценария: один для предварительного выполнения и один для последующего.

![Выбранные элементы](./media/pre-post-scripts/selected-items.png)

Завершите настройку развертывания обновлений.

Когда развертывание обновлений будет готово, откройте страницу **Развертывания обновлений** и проверьте результаты. Здесь вы увидите, среди прочего, состояние обоих сценариев.

![Обновление результатов](./media/pre-post-scripts/update-results.png)

Чтобы получить дополнительные сведения о том или ином сценарии, щелкните строку состояния развертывания обновлений. В открывшемся окне будет ссылка на код сценария, который выполнялся.

![Результаты развертывания](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Передача параметров

Настроив сценарии предварительного и последующего выполнения, вы можете передавать в них параметры, как в обычные модули Runbook. Параметры определяются при создании развертывания обновлений. В скриптах предварительного и последующего выполнения поддерживаются следующие типы данных:

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

Помимо стандартных параметров модуля Runbook, у вас есть еще один дополнительный параметр. **SoftwareUpdateConfigurationRunContext**. Этот параметр является строкой JSON, и если вы определяете параметр в скрипте pre или POST, он автоматически передается развертыванием обновлений. Параметр содержит сведения о развертывании обновлений, отбираемые из выходных данных [API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). Доступные в этой переменной свойства представлены в таблице ниже.


### <a name="softwareupdateconfigurationruncontext-properties"></a>Свойства SoftwareUpdateConfigurationRunContext

|Свойство  |Описание  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Имя конфигурации обновления программного обеспечения        |
|SoftwareUpdateConfigurationRunId     | Уникальный идентификатор для запуска.        |
|SoftwareUpdateConfigurationSettings     | Коллекция свойств, связанных с конфигурацией обновления программного обеспечения         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Операционные системы, на которых будут развертываться обновления         |
|SoftwareUpdateConfigurationSettings.duration     | Максимальная продолжительность развертывания обновлений (период обслуживания) в формате `PT[n]H[n]M[n]S` по стандарту ISO8601          |
|SoftwareUpdateConfigurationSettings.Windows     | Коллекция свойств, связанных с компьютерами Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Список номеров обновлений, которые не включаются в развертывание        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Классификации обновлений, выбранных для развертывания        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Параметры перезагрузки для развертывания обновлений        |
|azureVirtualMachines     | Список идентификаторов ресурсов для виртуальных машин Azure, включенных в развертывание обновлений        |
|nonAzureComputerNames|Список полных доменных имен компьютеров, не связанных с Azure, включенных в развертывание обновлений|

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

Полный пример со всеми доступными свойствами можно просмотреть [здесь](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext` Объект может содержать дублирующиеся записи для компьютеров. Это может привести к многократному выполнению сценариев pre и POST на одном компьютере. Чтобы обойти это поведение, `Sort-Object -Unique` используйте для выбора в скрипте только уникальных имен виртуальных машин.


## <a name="stopping-a-deployment"></a>Остановка развертывания

Если требуется отключить развертывание на основе предварительного скрипта, необходимо [создать](automation-runbook-execution.md#throw) исключение. Если исключение не возникает, скрипт развертывания и публикации по-прежнему будет выполняться. В [примере модуля Runbook](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) в галерее показано, как это можно сделать. Ниже приведен фрагмент кода из этого модуля Runbook.

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

Примеры сценариев предварительного и последующего выполнения можно найти в [коллекции центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) или импортировать через портал Azure. Чтобы импортировать сценарии через портал, в учетной записи Службы автоматизации выберите в разделе **Автоматизация процессов** элемент **Коллекция модулей Runbook**. В качестве фильтра укажите **Управление обновлениями**.

![Список в коллекции](./media/pre-post-scripts/runbook-gallery.png)

Также вы можете искать сценарии по именам, которые указаны в списке ниже.

* Update Management - Turn On VMs (Управление обновлениями — включить виртуальные машины).
* Update Management - Turn Off VMs (Управление обновлениями — выключить виртуальные машины).
* Update Management - Run Script Locally (Управление обновлениями — локальный запуск сценария).
* Update Management - Template for Pre/Post Scripts (Управление обновлениями — шаблон для скриптов предварительного и последующего выполнения).
* Update Management - Run Script with Run Command (Управление обновлениями — запуск сценария командой Run).

> [!IMPORTANT]
> Чтобы воспользоваться импортированным модулем Runbook, его нужно **опубликовать**. Для этого найдите нужный модуль в учетной записи Службы автоматизации, а затем щелкните **Изменить** и **Опубликовать**.

Все примеры основаны на базовом шаблоне, который представлен ниже. Этот шаблон можно использовать для создания собственных модулей Runbook, которые можно применять в сценариях предварительного и последующего выполнения. В шаблон уже включена вся необходимая логика для аутентификации в Azure и обработки параметра `SoftwareUpdateConfigurationRunContext`.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
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

Задачи pre и POST выполняются в качестве модуля Runbook в учетной записи службы автоматизации, а не непосредственно на компьютерах в развертывании. Задачи pre и POST также выполняются в контексте Azure и не имеют доступа к компьютерам, не являющимся компьютерами Azure. В следующих разделах показано, как можно напрямую взаимодействовать с компьютерами, будь это ВИРТУАЛЬная машина Azure или не Azure.

### <a name="interacting-with-azure-machines"></a>Взаимодействие с компьютерами Azure

Задачи pre и POST выполняются как модули Runbook и не запускаются на виртуальных машинах Azure в развертывании. Для взаимодействия с виртуальными машинами Azure необходимо иметь следующие элементы:

* учетная запись запуска от имени;
* Runbook, который требуется запустить

Для взаимодействия с машинами Azure следует использовать командлет [Invoke-азурермвмрункомманд](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) для взаимодействия с виртуальными машинами Azure. Пример того, как это сделать, см. в примере модуля Runbook, [Управление обновлениями — запуск скрипта с помощью команды Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Взаимодействие с компьютерами, не входящими в Azure

Задачи предварительного и последующего выполнения работают в контексте Azure и не имеют доступ к компьютерам в других средах. Чтобы взаимодействовать с компьютерами, не входящими в Azure, вам потребуется следующее:

* учетная запись запуска от имени;
* установленная на компьютере гибридная рабочая роль Runbook;
* модуль Runbook, который будет запускаться локально;
* родительский модуль Runbook.

Для взаимодействия с компьютерами, не относящимися к Azure, в контексте Azure выполняется родительский модуль Runbook. Он вызывает дочерний модуль Runbook с помощью командлета [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Ему нужно передать параметр `-RunOn` и имя гибридной рабочей роли Runbook, которая будет выполнять сценарий. Пример того, как это сделать, см. в примере модуля Runbook, [Управление обновлениями — запуск скрипта локально](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

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

* Нельзя передавать логические значения, объекты или массивы в параметры при использовании скриптов pre и POST. При такой попытке модуль Runbook возвратит ошибку. Полный список поддерживаемых типов см. в разделе [Parameters](#passing-parameters).

## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь с руководством ниже, чтобы узнать, как управлять обновлениями для виртуальных машин Windows.

> [!div class="nextstepaction"]
> [Устранение неполадок при изменениях в среде](automation-tutorial-update-management.md)

