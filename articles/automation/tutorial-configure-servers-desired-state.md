---
title: Настройка требуемого состояния серверов и управление смещением с помощью службы автоматизации Azure
description: Руководство по управлению настройками сервера с помощью службы "Настройка состояния службы автоматизации Azure"
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
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

Для этого учебника мы используем простую [конфигурацию DSC,](/powershell/scripting/dsc/configurations/configurations) которая гарантирует, что IIS установлен на VM.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником необходимы указанные ниже компоненты.

- Учетная запись службы автоматизации Azure. Указания по созданию учетной записи запуска от имени пользователя для службы автоматизации Azure см. в статье [Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени](automation-sec-configure-azure-runas-account.md).
- Менеджер ресурсов Azure VM (не классический) под управлением Windows Server 2008 R2 или позже. Для получения инструкций по созданию VM смотрите [Создать свой первый виртуальный компьютер Windows на портале Azure.](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell 3.6.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Знакомство с платформой Desired State Configuration (DSC) Для получения информации о DSC, см [Windows PowerShell Желаемое состояние Конфигурация Обзор](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Поддержка частичных конфигураций

Государственная конфигурация Azure Automation поддерживает использование [частичных конфигураций.](/powershell/scripting/dsc/pull-server/partialconfigs) В этом сценарии DSC настроен для самостоятельного управления несколькими конфигурациями, и каждая конфигурация извлекается из Azure Automation. Однако только одна конфигурация может быть назначена узлам для учетной записи автоматизации. Это означает, что если вы используете две конфигурации для узла, вам потребуются две учетные записи автоматизации.

Подробную информацию о том, как зарегистрировать частичную конфигурацию из службы pull, можно узнать документацию для [частичных конфигураций.](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)

Для получения дополнительной информации о том, как команды могут работать вместе, чтобы совместно управлять серверами, используя конфигурацию в качестве кода, [см. Понимание роли DSC в CI/CD Pipeline.](/powershell/scripting/dsc/overview/authoringadvanced)

## <a name="log-in-to-azure"></a>Вход в Azure

Зайдите в подписку Azure с помощью cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Создание и передача конфигурации в службе автоматизации Azure


В текстовом редакторе введите следующее и сохраните его локально как **TestConfig.ps1**.

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
> В более продвинутых сценариях, где требуется импортировать несколько модулей, `Import-DscResource` обеспечивающих Ресурсы DSC, убедитесь, что каждый модуль имеет уникальную линию в конфигурации.

Позвоните в [импорт-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) cmdlet, чтобы загрузить конфигурацию в свою учетную запись Автоматизации.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Компиляция конфигурации в конфигурации узла

Прежде чем назначать конфигурацию DSC узлу, ее нужно скомпилировать в конфигурации узла. Посмотреть [конфигурации DSC.](/powershell/scripting/dsc/configurations/configurations)

Позвоните в [Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) cmdlet, чтобы `TestConfig` компилировать `TestConfig.WebServer` конфигурацию в конфигурацию узла, названную в вашей учетной записи Автоматизации.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Регистрация виртуальной машины, управляемой с помощью настройки состояния

С помощью службы "Настройка состояния службы автоматизации Azure" можно управлять виртуальными машинами Azure (классическими или Resource Manager), локальными виртуальными машинами, компьютерами Linux, виртуальными машинами AWS и локальными физическими компьютерами. В этом разделе рассматривается регистрация только виртуальных машин Azure Resource Manager. Дополнительные сведения о регистрации компьютеров других типов см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).

Позвоните в [Регистр-AzAutomationDscscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) cmdlet, чтобы зарегистрировать ваш VM с Azure Automation State Configuration в качестве управляемого узла. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Указание параметров режима конфигурации

Используйте Смдлет [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) для регистрации VM в качестве управляемого узла и указания свойств конфигурации. Например, можно указать, что состояние машины должно применяться `ApplyOnly` только один раз, указав в качестве значения `ConfigurationMode` свойства. Конфигурация состояния не пытается применить конфигурацию после первоначальной проверки.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Вы также можете указать, как часто DSC проверяет состояние конфигурации с помощью `ConfigurationModeFrequencyMins` свойства. Дополнительные сведения о настройках конфигурации DSC см. в статье [Настройка локального диспетчера конфигураций](/powershell/scripting/dsc/managing-nodes/metaConfig).

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

Это присваивает `TestConfig.WebServer` конфигурацию узла, `DscVm`названную зарегистрированному узлам DSC. По умолчанию узел DSC проверяется на соответствие конфигурации узла каждые 30 минут. Сведения о том, как изменить интервал проверки соответствия, см. в статье [Настройка локального диспетчера конфигураций](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Проверка состояния соответствия управляемого узла

Вы можете получить отчеты о состоянии соответствия управляемому узла с помощью cmdlet [Get-AzAutomationDscNodeReport.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Удалить узлы из службы

При добавлении узла в конфигурацию состояния автоматизации Azure настройки установки в локальном менеджере конфигурации устанавливаются для регистрации в конфигурациях службы и вытягивания и необходимых модулей для настройки машины.
Если вы решите удалить узел из службы, это можно сделать с помощью портала Azure или Az cmdlets.

> [!NOTE]
> Отменить узла из службы только устанавливают настройки локального диспетчера конфигурации, чтобы узла больше не подключали к службе.
> Это не влияет на конфигурацию, которая в настоящее время применяется к узелу.
> Чтобы удалить текущую конфигурацию, используйте [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) или удалите локальный файл конфигурации (это единственный вариант для узлов Linux).

### <a name="azure-portal"></a>Портал Azure

Из Azure Automation нажмите на **конфигурацию состояния (DSC)** в таблице содержимого.
Далее нажмите **узлы,** чтобы просмотреть список узлов, зарегистрированных в службе.
Нажмите на название узла, который вы хотите удалить.
В открываемом представлении узла нажмите **Unregister.**

### <a name="powershell"></a>PowerShell

Чтобы отменить регистрацию узла из службы государственной конфигурации Azure Automation с помощью PowerShell, следуйте документации для cmdlet [Unregister-AzAutomationDscNode.](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0)

## <a name="next-steps"></a>Следующие шаги

- Для начала см. [Начало работы с конфигурацией состояния автоматизации Azure.](automation-dsc-getting-started.md)
- Чтобы узнать, как на [Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md)бортовых узлах, см.
- Чтобы узнать о компиляции конфигураций DSC, чтобы можно было назначить их целевым узлам, см. [Конфигурации компиляции в конфигурации состояния azure Automation.](automation-dsc-compile.md)
- Для справки PowerShell cmdlet [см.](/powershell/module/azurerm.automation/#automation)
- Для получения информации о ценах на цены [см.](https://azure.microsoft.com/pricing/details/automation/)
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
