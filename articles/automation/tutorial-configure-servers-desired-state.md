---
title: Настройка требуемого состояния серверов и управление смещением с помощью службы автоматизации Azure
description: Руководство по управлению настройками сервера с помощью службы "Настройка состояния службы автоматизации Azure"
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 72e5018dc1212e57dc190c05cc54158d37ca7fe1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231502"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Настройка требуемого состояния серверов и управление смещением

Служба "Настройка состояния службы автоматизации Azure" позволяет задавать конфигурации для серверов и гарантировать, что спустя время они будут находиться в указанном состоянии.

> [!div class="checklist"]
> - Подключение виртуальной машины для управления с помощью DSC службы автоматизации Azure.
> - Передача конфигурации в службу автоматизации Azure.
> - Компиляция конфигурации в конфигурации узла
> - Назначение конфигурации узла управляемому узлу
> - Проверка состояния соответствия управляемого узла

## <a name="prerequisites"></a>Технические условия

Для работы с этим учебником требуется:

- Учетная запись службы автоматизации Azure. Указания по созданию учетной записи запуска от имени пользователя для службы автоматизации Azure см. в статье [Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени](automation-sec-configure-azure-runas-account.md).
- Виртуальная машина Azure Resource Manager (неклассическая) под управлением Windows Server 2008 R2 или более поздней версии. Инструкции по созданию виртуальной машины см. в статье [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Azure PowerShell 3.6.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Знакомство с платформой Desired State Configuration (DSC) Дополнительные сведения о DSC см. в статье [Общие сведения о службе настройки требуемого состояния Windows PowerShell](/powershell/scripting/dsc/overview/overview).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Создание и передача конфигурации в службе автоматизации Azure

В этом руководстве используется простая конфигурация DSC, которая гарантирует, что на виртуальной машине установлены службы IIS.

Дополнительные сведения о конфигурации DSC см. в статье [Конфигурации DSC](/powershell/scripting/dsc/configurations/configurations).

В текстовом редакторе введите следующую команду и сохраните ее локально как `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> In more advanced scenarios where you require multiple modules to be imported that provide DSC Resources, make sure each module has a unique `Import-DscResource` line in your configuration.

Вызовите командлет `Import-AzureRmAutomationDscConfiguration`, чтобы отправить конфигурацию в учетную запись службы автоматизации:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Компиляция конфигурации в конфигурации узла

Прежде чем назначать конфигурацию DSC узлу, ее нужно скомпилировать в конфигурации узла.

Дополнительные сведения о компилировании конфигураций см. в статье [Конфигурации DSC](/powershell/scripting/dsc/configurations/configurations).

Вызовите командлет `Start-AzureRmAutomationDscCompilationJob`, чтобы скомпилировать конфигурацию `TestConfig` в конфигурацию узла:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

В вашей учетной записи службы автоматизации будет создана конфигурация узла с именем `TestConfig.WebServer`.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Регистрация виртуальной машины, управляемой с помощью настройки состояния

С помощью службы "Настройка состояния службы автоматизации Azure" можно управлять виртуальными машинами Azure (классическими или Resource Manager), локальными виртуальными машинами, компьютерами Linux, виртуальными машинами AWS и локальными физическими компьютерами. В этом разделе рассматривается регистрация только виртуальных машин Azure Resource Manager. Дополнительные сведения о регистрации компьютеров других типов см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).

Вызовите командлет `Register-AzureRmAutomationDscNode`, чтобы зарегистрировать виртуальную машину в службе "Настройка состояния службы автоматизации Azure".

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Указанная виртуальная машина будет зарегистрирована в качестве управляемого узла на платформе настройки состояния.

### <a name="specify-configuration-mode-settings"></a>Указание параметров режима конфигурации

При регистрации виртуальной машины в качестве управляемого узла можно также указать свойства конфигурации. Например, вы можете указать, что состояние машины должно применяться только один раз (DSC не будет пытаться применить конфигурацию после начальной проверки), указав `ApplyOnly` в качестве значения свойства **ConfigurationMode**.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Вы также можете указать, как часто DSC нужно проверять состояние конфигурации, с помощью свойства **ConfigurationModeFrequencyMins**.

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Дополнительные сведения о настройке свойств конфигурации для управляемого узла см. в статье о [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Дополнительные сведения о настройках конфигурации DSC см. в статье [Настройка локального диспетчера конфигураций](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Назначение конфигурации узла управляемому узлу

Теперь можно назначить конфигурации скомпилированного узла для виртуальной машины, которую необходимо настроить.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

При этом конфигурация узла с именем `TestConfig.WebServer` присваивается зарегистрированному узлу DSC с именем `DscVm`.
По умолчанию узел DSC проверяется на соответствие конфигурации узла каждые 30 минут.
Сведения о том, как изменить интервал проверки соответствия, см. в статье [Настройка локального диспетчера конфигураций](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Working with Partial Configurations

Azure Automation State Configuration supports usage of [partial configurations](/powershell/scripting/dsc/pull-server/partialconfigs).
In this scenario, DSC is configured to manage multiple configurations independently, and each configuration is retrieved from Azure Automation.
However, only one configuration can be assigned to a node per automation account.
This means if you are using two configurations for a node you will require two automation accounts.

For details about how to register a partial configuration from pull service, see the documentation for [partial configurations](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

For more information about how teams can work together to collaboratively manage servers using configuration as code see [Understanding DSC's role in a CI/CD Pipeline](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Проверка состояния соответствия управляемого узла

Отчеты о состоянии соответствия управляемого узла можно получить, вызвав командлет `Get-AzureRmAutomationDscNodeReport`.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Removing nodes from service

When you add a node to Azure Automation State Configuration, the settings in Local Configuration Manager are set to register with the service and pull configurations and required modules to configure the machine.
If you choose to remove the node from the service, you can do so using either the Azure portal or the Az cmdlets.

> [!NOTE]
> Unregistering a node from the service only sets the Local Configuration Manager settings so the node is no longer connecting to the service.
> This does not effect the configuration that is currently applied to the node.
> To remove the current configuration, use the [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) or delete the local configuration file (this is the only option for Linux nodes).

### <a name="azure-portal"></a>портала Azure

From Azure Automation, click on **State configuration (DSC)** in the table of contents.
Next click **Nodes** to view the list of nodes that are registered with the service.
Click on the name of the node you wish to remove.
In the Node view that opens, click **Unregister**.

### <a name="powershell"></a>PowerShell

To unregister a node from Azure Automation State Configuration service using PowerShell, follow the documentation for the cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Дополнительные сведения о подключении узлов см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
