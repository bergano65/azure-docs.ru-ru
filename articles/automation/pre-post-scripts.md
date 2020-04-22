---
title: Управление предскриптовыми сценариями и постскриптумами при развертывании управления обновлением в Azure
description: В этой статье описывается, как настроить и управлять предскрипточными сценариями и постскриптами для развертывания обновлений.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 00cde5255f9c9a2baa7c7042ae2a8f73448da0ae
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679990"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Управление предскрипточными сценариями и постскриптами

Предварительные скрипты и постскриптумы — это runbooks для выполнения учетной записи Azure Automation до (предварительной задачи) и после развертывания обновления (после выполнения задачи). Предварительные скрипты и постскриптумы работают в контексте Azure, а не локально. Предварительные скрипты запускаются в начале развертывания обновления. Постскриптумы работают в конце развертывания и после перезагрузки, которые настроены.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="pre-script-and-post-script-requirements"></a>Предписывающие и постскриптумные требования

Для того, чтобы runbook использовался в качестве предварительного или постскриптума, необходимо импортировать его в свой аккаунт Автоматизации и [публиковать runbook.](manage-runbooks.md#publishing-a-runbook)

## <a name="pre-script-and-post-script-parameters"></a>Параметры предварительного сценария и постскриптум

При настройке предварительных скриптов и постскриптумов можно передавать параметры так же, как планирование runbook. Параметры определяются при создании развертывания обновлений. Предварительные скрипты и постскриптами поддерживают следующие типы:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Параметры предварительного сценария и постскриптумной runbook не поддерживают типы булеана, объекта или массива. Эти значения приводят к сбою в сбой рун. 

Если вам нужен другой тип объекта, вы можете привести его к другому типу с помощью собственной логики runbook.

В дополнение к стандартным параметрам runbook предоставляется `SoftwareUpdateConfigurationRunContext` параметр (тип строки JSON). Если вы определяете параметр в предварительном скрипте или постскриптам runbook, он автоматически передается в развертывание обновления. Параметр содержит информацию о развертывании обновления, которое представляет собой подмножество информации, возвращенной [API SoftwareUpdateconfigurations.](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) Разделы ниже определяют связанные свойства.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Свойства SoftwareUpdateConfigurationRunContext

|Свойство.  |Описание  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Название конфигурации обновления программного обеспечения.        |
|SoftwareUpdateConfigurationRunId     | Уникальный идентификатор для бега.        |
|SoftwareUpdateConfigurationSettings     | Коллекция свойств, связанных с конфигурацией обновления программного обеспечения.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Операционные системы, предназначенные для развертывания обновления.         |
|SoftwareUpdateConfigurationSettings.duration     | Максимальная продолжительность развертывания обновления `PT[n]H[n]M[n]S` в iSO8601; также называется окном обслуживания.          |
|SoftwareUpdateConfigurationSettings.Windows     | Коллекция свойств, связанных с компьютерами Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Список КБ, исключенных из развертывания обновления.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Классификация обновлений, выбранных для развертывания обновления.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Настройки перезагрузки для развертывания обновления.        |
|azureVirtualMachines     | Список ресурсов для vMs Azure в развертывании обновления.        |
|nonAzureComputerNames|Список не-Azure компьютеров F-DNs в развертывании обновления.|

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

Полный пример со всеми свойствами можно найти по адресу: [Получить конфигурацию обновления программного обеспечения по имени](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Объект `SoftwareUpdateConfigurationRunContext` может содержать дублирующие записи для машин. Это может привести к тому, что предварительные скрипты и постскрипты будут запускаться несколько раз на одной машине. Чтобы обойти это поведение, используйте `Sort-Object -Unique` только уникальные имена VM.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Использование предварительного сценария или пост-скрипта в развертывании

Чтобы использовать предварительный сценарий или пост-сценарий в развертывании обновлений, начните с создания развертывания обновления. Выберите **предварительные скрипты и постскриптумы**. Откроется страница **Выбор сценариев предварительного и последующего выполнения**.

![Выбор сценариев](./media/pre-post-scripts/select-scripts.png)

Выберите скрипт, который вы хотите использовать. В этом примере мы используем runbook **UpdateManagement-TurnOnVms.** При выборе книги запуска открывается страница **«Настройка сценария».** Выберите **Предварительный сценарий,** а затем выберите **OK**.

Повторите эту процедуру для сценария **UpdateManagement-TurnOffVms**. Но когда вы выбираете **тип сценария,** выберите **Post-Script**.

В разделе **Выбранные элементы** теперь отображаются оба выбранных сценария. Один из них является предварительный сценарий, а другой пост-сценарий:

![Выбранные элементы](./media/pre-post-scripts/selected-items.png)

Завершите настройку развертывания обновления.

После завершения развертывания обновления можно перейти к **развертыванию обновлений** для просмотра результатов. Как вы можете видеть, статус предоставляется для предварительного сценария и пост-скрипта:

![Результаты обновления](./media/pre-post-scripts/update-results.png)

Выбрав запуск развертывания обновления, вам будут показаны дополнительные сведения о предварительных сценариях и постсценариях. В открывшемся окне будет ссылка на код сценария, который выполнялся.

![Результаты развертывания](./media/pre-post-scripts/deployment-run.png)

es в вашем скрипте.

## <a name="stopping-a-deployment"></a>Остановка развертывания

Если требуется остановить развертывание на основе предварительного сценария, необходимо [выбросить](automation-runbook-execution.md#throw) исключение. В противном случае развертывание и постскриптум будут по-прежнему работать. Следующий фрагмент кода показывает, как выбросить исключение.

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



## <a name="interacting-with-machines"></a>Взаимодействие с машинами

Предварительные сценарии и постзадачи выполняются как runbooks в вашей учетной записи Automation, а не непосредственно на машинах в развертывании. Предварительные задачи и постзадачи также выполняются в контексте Azure и не имеют доступа к машинам, не входящих в Azure. В следующих разделах показано, как можно взаимодействовать с машинами напрямую, будь то VMs-инобора Azure или не-Azure машины.

### <a name="interact-with-azure-machines"></a>Взаимодействие с машинами Azure

Предварительные задачи и постзадачи выполняются как runbooks и не выполняются на вазовских вс-чатах в развертывании. Чтобы взаимодействовать с VMs Azure, необходимо иметь следующие элементы:

* учетная запись запуска от имени;
* Запуск книги, которую вы хотите запустить

Чтобы взаимодействовать с машинами Azure, необходимо использовать cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) для взаимодействия с вашими MMs-иносами Azure. Пример управления обновлением runbook можно например— в [журнале Runbook — в сценарии запуска с командой Run.](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)

### <a name="interact-with-non-azure-machines"></a>Взаимодействие с машинами, не входящих в Azure

Предварительные задачи и постзадачи выполняются в контексте Azure и не имеют доступа к машинам, не входящих в Azure. Чтобы взаимодействовать с машинами, не входящих в Azure, необходимо иметь следующие элементы:

* учетная запись запуска от имени;
* установленная на компьютере гибридная рабочая роль Runbook;
* модуль Runbook, который будет запускаться локально;
* Родительская книга

Для взаимодействия с машинами, не относящиеся к Azure, в контексте Azure работает родительская книга. Этот runbook вызывает книгу ребенка с [cmdlet Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) Ему нужно передать параметр `RunOn` и имя гибридной рабочей роли Runbook, которая будет выполнять сценарий. Смотрите пример Runbook [Управление обновлением - запустить сценарий локально.](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44)

## <a name="aborting-patch-deployment"></a>Прекращение развертывания патчей

Если предварительный скрипт вернет ошибку, может потребоваться прервать развертывание. Для этого необходимо [внести](/powershell/module/microsoft.powershell.core/about/about_throw) ошибку в сценарий для любой логики, которая представляла бы собой неудачу.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Примеры

Образцы для предварительных скриптов и постскриптумов можно найти в [галерее Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) и [PowerShell Gallery,](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)или вы можете импортировать их через портал Azure. Для этого в вашем аккаунте Автоматизации, под **автоматизацией процессов,** выберите **Runbooks Gallery.** В качестве фильтра укажите **Управление обновлениями**.

![Список в коллекции](./media/pre-post-scripts/runbook-gallery.png)

Или вы можете искать их по имени сценария, как показано в следующем списке:

* Update Management - Turn On VMs (Управление обновлениями — включить виртуальные машины).
* Update Management - Turn Off VMs (Управление обновлениями — выключить виртуальные машины).
* Update Management - Run Script Locally (Управление обновлениями — локальный запуск сценария).
* Update Management - Template for Pre/Post Scripts (Управление обновлениями — шаблон для скриптов предварительного и последующего выполнения).
* Update Management - Run Script with Run Command (Управление обновлениями — запуск сценария командой Run).

> [!IMPORTANT]
> После импорта runbooks, вы должны опубликовать их, прежде чем они могут быть использованы. Для этого найдите книгу в учетной записи Automation, выберите **«Отек»,** а затем выберите **Publish.**

Все образцы основаны на базовом шаблоне, который определен в следующем примере. Этот шаблон можно использовать для создания собственной книги для использования с помощью предварительных скриптов и постскриптумов. Включена необходимая логика для проверки `SoftwareUpdateConfigurationRunContext` подлинности с помощью Azure и обработки параметра.

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
> Для неграфических runbooks PowerShell, `Add-AzAccount` и `Add-AzureRMAccount` псевдонимы для [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Вы можете использовать эти cmdlets или вы можете [обновить свои модули](automation-update-azure-modules.md) в вашей учетной записи автоматизации до последних версий. Возможно, потребуется обновить модули, даже если вы только что создали новую учетную запись Automation.

## <a name="next-steps"></a>Следующие шаги

Перейти к следующему учебнику, чтобы узнать, как управлять обновлениями для ваших виртуальных машин Windows:

> [!div class="nextstepaction"]
> [Устранение неполадок при изменениях в среде](automation-tutorial-update-management.md)