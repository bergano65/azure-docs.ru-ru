---
title: Настройка требуемого состояния серверов и управление смещением с помощью службы автоматизации Azure
description: Руководство по управлению настройками сервера с помощью службы "Настройка состояния службы автоматизации Azure"
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678708"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Настройка требуемого состояния серверов и управление смещением

Служба "Настройка состояния службы автоматизации Azure" позволяет задавать конфигурации для серверов и гарантировать, что спустя время они будут находиться в указанном состоянии.

> [!div class="checklist"]
> - Подключение виртуальной машины для управления с помощью DSC службы автоматизации Azure.
> - Передача конфигурации в службу автоматизации Azure.
> - Компиляция конфигурации в конфигурации узла
> - Назначение конфигурации узла управляемому узлу.
> - Проверка состояния соответствия управляемого узла

В этом руководстве мы используем простую [конфигурацию DSC](/powershell/scripting/dsc/configurations/configurations) , которая обеспечивает установку служб IIS на виртуальной машине.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником необходимы указанные ниже компоненты.

- Учетная запись службы автоматизации Azure. Указания по созданию учетной записи запуска от имени пользователя для службы автоматизации Azure см. в статье [Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени](automation-sec-configure-azure-runas-account.md).
- Виртуальная машина Azure Resource Manager (не классическая) под Windows Server 2008 R2 или более поздней версии. Инструкции по созданию ВИРТУАЛЬНОЙ машины см. [в разделе Создание первой виртуальной машины Windows в портал Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Azure PowerShell 3.6.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Знакомство с платформой Desired State Configuration (DSC) Сведения о DSC см. в разделе [Общие сведения о настройке требуемого состояния Windows PowerShell](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Поддержка частичных конфигураций

Конфигурация состояния службы автоматизации Azure поддерживает использование [частичных конфигураций](/powershell/scripting/dsc/pull-server/partialconfigs). В этом сценарии DSC настроен для управления несколькими конфигурациями независимо, и каждая конфигурация извлекается из службы автоматизации Azure. Однако для каждой учетной записи службы автоматизации можно назначить только одну конфигурацию. Это означает, что при использовании двух конфигураций для узла потребуется две учетные записи службы автоматизации.

Дополнительные сведения о регистрации частичной конфигурации из опрашивающей службы см. в документации по [частичным конфигурациям](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Дополнительные сведения о совместной работе групп для совместного управления серверами с помощью конфигурации в качестве кода см. [в разделе Основные сведения о роли DSC в конвейере CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью командлета [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Создание и передача конфигурации в службе автоматизации Azure


В текстовом редакторе введите следующую команду и сохраните ее локально как **TestConfig. ps1**.

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
> В более сложных сценариях, где требуется импортировать несколько модулей, которые предоставляют ресурсы DSC, убедитесь, что каждый модуль имеет уникальную `Import-DscResource` строку в вашей конфигурации.

Вызовите командлет [Import-азаутоматиондскконфигуратион](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) , чтобы передать конфигурацию в учетную запись службы автоматизации.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Компиляция конфигурации в конфигурации узла

Прежде чем назначать конфигурацию DSC узлу, ее нужно скомпилировать в конфигурации узла. См. статью [Конфигурации DSC](/powershell/scripting/dsc/configurations/configurations).

Вызовите командлет [Start-азаутоматиондсккомпилатионжоб](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) , чтобы скомпилировать `TestConfig` конфигурацию в конфигурацию узла с именем `TestConfig.WebServer` в учетной записи службы автоматизации.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Регистрация виртуальной машины, управляемой с помощью настройки состояния

С помощью службы "Настройка состояния службы автоматизации Azure" можно управлять виртуальными машинами Azure (классическими или Resource Manager), локальными виртуальными машинами, компьютерами Linux, виртуальными машинами AWS и локальными физическими компьютерами. В этом разделе рассматривается регистрация только виртуальных машин Azure Resource Manager. Дополнительные сведения о регистрации компьютеров других типов см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).

Вызовите командлет [Register-азаутоматиондскноде](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) , чтобы зарегистрировать виртуальную машину в конфигурации состояния службы автоматизации Azure в качестве управляемого узла. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Указание параметров режима конфигурации

Используйте командлет [Register-азаутоматиондскноде](/powershell/module/azurerm.automation/register-azurermautomationdscnode) , чтобы зарегистрировать виртуальную машину в качестве управляемого узла и указать свойства конфигурации. Например, можно указать, что состояние компьютера должно применяться только один раз, указав `ApplyOnly` в качестве значения `ConfigurationMode` свойства. Конфигурация состояния не пытается применить конфигурацию после первоначальной проверки.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Можно также указать, как часто DSC проверяет состояние конфигурации с помощью `ConfigurationModeFrequencyMins` свойства. Дополнительные сведения о настройках конфигурации DSC см. в статье [Настройка локального диспетчера конфигураций](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Назначение конфигурации узла управляемому узлу.

Теперь можно назначить конфигурации скомпилированного узла для виртуальной машины, которую необходимо настроить.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

При этом Конфигурация узла с именем `TestConfig.WebServer` будет назначена зарегистрированному узлу `DscVm`DSC. По умолчанию узел DSC проверяется на соответствие конфигурации узла каждые 30 минут. Сведения о том, как изменить интервал проверки соответствия, см. в статье [Настройка локального диспетчера конфигураций](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Проверка состояния соответствия управляемого узла

Отчеты о состоянии соответствия управляемого узла можно получить с помощью командлета [Get-азаутоматиондскнодерепорт](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0) .

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Удаление узлов из службы

При добавлении узла в конфигурацию состояния службы автоматизации Azure параметры в локальной Configuration Manager задаются для регистрации в службе, а также для получения конфигураций и требуемых модулей для настройки компьютера.
Если вы решили удалить узел из службы, это можно сделать с помощью командлета портал Azure или AZ.

> [!NOTE]
> При отмене регистрации узла из службы задаются только локальные параметры Configuration Manager, поэтому узел больше не подключается к службе.
> Это не влияет на конфигурацию, которая в настоящее время применяется к узлу.
> Чтобы удалить текущую конфигурацию, используйте [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) или удалите локальный файл конфигурации (это единственный вариант для узлов Linux).

### <a name="azure-portal"></a>Портал Azure

Из службы автоматизации Azure щелкните **Конфигурация состояния (DSC)** в содержании.
Затем щелкните **узлы** , чтобы просмотреть список узлов, зарегистрированных в службе.
Щелкните имя узла, который вы хотите удалить.
В открывшемся представлении узла нажмите кнопку **отменить регистрацию**.

### <a name="powershell"></a>PowerShell

Чтобы отменить регистрацию узла в службе настройки состояния службы автоматизации Azure с помощью PowerShell, следуйте указаниям в документации командлета [Unregister-азаутоматиондскноде](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Дальнейшие шаги

- Чтобы приступить к работе, см. статью [Приступая к работе с конфигурацией состояния службы автоматизации Azure](automation-dsc-getting-started.md).
- Сведения о подключении узлов см. в статье подключение [компьютеров для управления с помощью конфигурации состояния службы автоматизации Azure](automation-dsc-onboarding.md).
- Дополнительные сведения о компиляции конфигураций DSC с целью назначения их целевым узлам см. [в разделе Компиляция конфигураций в конфигурации состояния службы автоматизации Azure](automation-dsc-compile.md).
- Справочник по командлетам PowerShell см. в статье [командлеты конфигурации состояния службы автоматизации Azure](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. в разделе [цены на настройку состояния службы автоматизации Azure](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
