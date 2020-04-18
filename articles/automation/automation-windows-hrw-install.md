---
title: Гибридные рабочие роли Runbook службы автоматизации Azure для Windows
description: В этой статье содержатся сведения об установке гибридной рабочей роли Runbook в службе автоматизации Azure, которая позволяет запускать модули runbook на компьютерах под управлением Windows в локальном центре обработки данных или облачной среде.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617346"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Развертывание гибридной рабочей роли Runbook для Windows

Гибридную рабочую роль Runbook службы автоматизации Azure можно использовать для выполнения модулей runbook непосредственно на компьютере, размещающем роль, для работы с ресурсами в среде, что позволяет управлять этими локальными ресурсами. Azure Automation хранит и управляет рунбуками, а затем доставляет их на один или несколько назначенных компьютеров. В этой статье описывается, как развернуть гибридный Runbook Worker на машине Windows.

После успешного развертывания рабочей роли Runbook ознакомьтесь с [запуском модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md), чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Установка и конфигурация Рабочего рабочего сотрудника Windows Гибридная работа Runbook

Для установки и настройки Windows Hybrid Runbook Worker можно использовать один из следующих методов.

* Для использующих VMs-технологий Azure установите агент Log Analytics для Windows с использованием [виртуального расширения машины для Windows.](../virtual-machines/extensions/oms-windows.md) Расширение устанавливает агент Log Analytics на виртуальные машины Azure и регистрирует виртуальные машины в существующее рабочее пространство Log Analytics с помощью шаблона Azure Resource Manager или PowerShell. После установки агента VM может быть добавлен в группу Hybrid Runbook Worker в вашей учетной записи Автоматизации. Ссылайтесь на этапы 3 и 4 в разделе [Ручного развертывания.](#manual-deployment)

* Используйте runbook автоматизации, чтобы полностью автоматизировать процесс настройки компьютера Windows. Это рекомендуемый метод для машин в центре обработки данных или другой облачной среде.

* Следуйте пошаговой процедуре, чтобы вручную установить и настроить роль Hybrid Runbook Worker на вашем VM, не относящемся к Azure.

> [!NOTE]
> Для управления конфигурацией серверов, поддерживающих роль Hybrid Runbook Worker с помощью конфигурации состояния (DSC), необходимо добавить серверы в виде узлов DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Минимальные требования к Windows Hybrid Runbook работника

Ниже приведены минимальные требования для гибридной рабочей роли Runbook Windows.

* Windows Server 2012 или более поздней версии;
* Windows PowerShell 5.1 или позже[(скачать WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 или более поздней версии.
* два ядра;
* 4 ГБ ОЗУ;
* Порт 443 (исходящий).

### <a name="network-configuration"></a>Конфигурация сети

Дополнительные требования к сети для гибридной рабочей роли Runbook см. в разделе [Настройка сети](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Сервер на борту для управления с автоматизацией DSC

Для получения информации о бортных серверах для управления DSC [см.](automation-dsc-onboarding.md)

Включение [решения Update Management](../operations-management-suite/oms-solution-update-management.md) автоматически настраивает любой компьютер Windows, подключенный к рабочей области Log Analytics в качестве гибридного сотрудника Runbook, для поддержки runbooks, включенных в решение. Однако этот работник не зарегистрирован в гибридных группах Runbook Worker, уже определенных в вашей учетной записи Automation.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Добавление компьютера к гибридной группе Runbook Worker

Компьютер рабочего можно добавить в группу Hybrid Runbook Worker в учетной записи Автоматизации. Обратите внимание, что вы должны поддерживать запуски Automation до тех пор, пока вы используете одну и ту же учетную запись как для решения, так и для членства в группе Hybrid Runbook Worker. Эта функция добавлена в версии 7.2.12024.0 гибридной рабочей роли Runbook.

## <a name="automated-deployment"></a>Автоматизированное развертывание

На целевой машине выполните следующие действия по автоматизации установки и конфигурации роли гибридного рабочего Windows.

### <a name="step-1---download-the-powershell-script"></a>Шаг 1 - Скачать сценарий PowerShell

Скачать **новый-OnPremiseHybridWorker.ps1** скрипт из [галереи PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Загрузка должна осуществляться непосредственно с компьютера, работающего с ролью Hybrid Runbook Worker, или с другого компьютера в вашей среде. Когда вы загрузили скрипт, скопируйте его своему работнику. В скрипте **New-OnPremiseHybridWorker.ps1** используются параметры, описанные ниже во время выполнения.

| Параметр | Status | Описание |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Обязательный | имя группы ресурсов, связанной с вашей учетной записью службы автоматизации. |
| `AutomationAccountName` | Обязательный | имя учетной записи службы автоматизации.
| `Credential` | Необязательно | Учетные данные для использования при входе в среду Azure. |
| `HybridGroupName` | Обязательный | имя группы гибридных рабочих ролей Runbook, которую вы указываете в качестве целевой для модулей runbook, поддерживающих этот сценарий. |
| `OMSResourceGroupName` | Необязательно | имя группы ресурсов для рабочей области Log Analytics. Если эта группа ресурсов не указана, используется значение. `AAResourceGroupName` |
| `SubscriptionID` | Обязательный | Идентификатор подписки Azure, связанный с учетной записью Automation. |
| `TenantID` | Необязательно | Идентификатор организации-арендатора, связанной с вашей учетной записью Automation. |
| `WorkspaceName` | Необязательно | Имя рабочей области Log Analytics. Если у вас нет рабочей области Log Analytics, сценарий создаст и настроит ее. |

> [!NOTE]
> При создании решений Azure Automation поддерживает только определенные регионы для увязки рабочего пространства log Analytics и учетной записи Автоматизации. Список поддерживаемых пар отображений можно узнать на [сайте Region mapping для учетной записи Automation и рабочего пространства Log Analytics.](how-to/region-mappings.md)

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Шаг 2 - Открытая оболочка командной строки Windows PowerShell

Откройте **Windows PowerShell** от **стартового** экрана в режиме администратора.

### <a name="step-3---run-the-powershell-script"></a>Шаг 3 - Выполнить сценарий PowerShell

В оболочке командной строки PowerShell просматривайте папку, содержащую загруженный сценарий. `AutomationAccountName`Изменение значений для параметров, `OMSResourceGroupName` `HybridGroupName` `AAResourceGroupName`, `SubscriptionID`, `WorkspaceName`, и . Затем выполните сценарий.

После выполнения сценария вы увидите запрос на аутентификацию в Azure. Для входа необходимо использовать учетную запись, которая является участником роли администраторов подписки и соадминистратором подписки.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Шаг 4 - Установка NuGet

Вам предлагается согласиться установить NuGet и проверить подлинность с помощью учетных данных Azure. Если у вас нет последней версии NuGet, вы можете получить ее из [доступных версий распространения NuGet.](https://www.nuget.org/downloads)

### <a name="step-5---verify-the-deployment"></a>Шаг 5 - Проверить развертывание

После завершения выполнения сценария на странице Группы гибридных рабочих ролей отображается новая группа и количество элементов. Если указать имеющуюся группу, количество элементов в ней увеличивается. Вы можете выбрать группу из списка на странице гибридных групп рабочих и выбрать плитку **Гибридных Рабочих.** На странице Гибридных Рабочих можно увидеть каждого участника группы, указанного в списке.

## <a name="manual-deployment"></a>Развертывание вручную

На целевой машине выполните первые два шага один раз для среды автоматизации. Затем выполните оставшиеся шаги для каждого рабочего компьютера.

### <a name="step-1---create-a-log-analytics-workspace"></a>Шаг 1 - Создание рабочего пространства для анализа журналов

Если у вас еще нет рабочего пространства для анализа журналов, просмотрите [рекомендации по проектированию журналов Azure Monitor,](../azure-monitor/platform/design-logs-deployment.md) прежде чем создавать рабочее пространство.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Шаг 2 - Добавьте решение автоматизации в рабочее пространство Log Analytics

Решение Automation добавляет функциональность для Автоматизации Azure, включая поддержку гибридного runbook Worker. При добавлении решения в рабочее пространство Log Analytics он автоматически толкает на компьютер агента компоненты рабочего, которые вы устанавливаете, как описано на следующем этапе.

Чтобы добавить решение Automation в рабочее пространство, запустите следующее cmdlet PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Шаг 3 - Установка агента по анализу журналов для Windows

Агент log Analytics для Windows соединяет компьютеры с рабочим пространством Analytics Azure Monitor. Когда вы устанавливаете агент на компьютер и подключаете его к рабочему пространству, он автоматически загружает компоненты, необходимые для гибридного Runbook Worker.

Чтобы установить агент на компьютере, следуйте инструкциям в [журналах Connect Windows для журналов Azure Monitor.](../log-analytics/log-analytics-windows-agent.md) Выполните эту процедуру на нескольких компьютерах, чтобы добавить в среду несколько компонентов Worker.

Когда через несколько минут агент успешно подключился к рабочему пространству Log Analytics, можно запустить следующий запрос, чтобы убедиться, что он отправляет данные об сердцебиении в рабочее пространство.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

В результатах поиска следует увидеть записи сердцебиения компьютера, указывающие на то, что он подключен и отчитываясь перед службой. По умолчанию каждый агент перенаправляет запись сердцебиения в назначенное рабочее пространство. 

Используйте следующие шаги для завершения установки и настройки агента.

1. Включите решение на борт агент-машина. Смотрите [бортовые машины в рабочей области.](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace)
2. Убедитесь, что агент правильно скачал решение Automation. 
3. Чтобы подтвердить версию Гибридного Runbook Worker, просмотрите **c: «Файлы программы»-Агент мониторинга Майкрософт»Агент-агент-azureAutomation** и обратите внимание на **версию** subfolder.

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Шаг 4 - Установите среду runbook и подключитесь к автоматизации Azure

При настройке агента для отчета в рабочее пространство Log Analytics `HybridRegistration` решение Automation отталкивает `Add-HybridRunbookWorker` модуль PowerShell, содержащий cmdlet. Используйте этот cmdlet для установки среды runbook на компьютер еил и зарегистрируйте его в Azure Automation.

Откройте сеанс PowerShell с правами администратора и импортируйте модуль, выполнив указанные ниже команды.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Теперь `Add-HybridRunbookWorker` запустите cmdlet с помощью следующего синтаксиса.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Данные, необходимые для этого командлета, можно найти на странице Управление ключами на портале Azure. Откройте эту страницу, выбрав **ключи** на странице «Настройки» в учетной записи Автоматизация.

![Страница «Управление ключами»](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Для `GroupName` параметра используйте название группы Hybrid Runbook Worker. Если эта группа уже существует в учетной записи службы автоматизации, то к ней добавляется текущий компьютер. Если эта группа не существует, она добавляется.
* Для `EndPoint` параметра используйте запись **URL-адреса** на странице «Управляемые ключи».
* Для `Token` параметра используйте запись **PRIMARY ACCESS KEY** на странице «Управляемые ключи».
* При необходимости `Verbose` установите параметр для получения подробной информации об установке.

### <a name="step-5----install-powershell-modules"></a>Шаг 5 - Установка модулей PowerShell

Модули Runbook могут использовать любые из действий и командлетов, которые определены в модулях, установленных в вашей среде службы автоматизации Azure. Поскольку эти модули не развертываются автоматически на компьютеры, их необходимо установить вручную. Исключением является модуль Azure. Этот модуль устанавливается по умолчанию и обеспечивает доступ к cmdlets для всех служб и действий Azure для Azure Automation.

Поскольку основная цель функции Hybrid Runbook Worker заключается в управлении локальными ресурсами, скорее всего, необходимо установить модули, поддерживающие эти ресурсы, в частности `PowerShellGet` модуль. Для получения информации об установке модулей Windows PowerShell [см.](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)

Установленные модули должны находиться в `PSModulePath` месте, на которое ссылается переменная среды, чтобы гибридный работник мог автоматически импортировать их. Для получения дополнительной информации смотрите [Установка модулей в PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде, см. статью [Запуск модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).
* Для получения инструкций по удалению гибридных работников Runbook [см.](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)
* Чтобы узнать, как устранить неполадки гибридных работников Runbook, [см.](troubleshoot/hybrid-runbook-worker.md#windows)
* Дополнительные шаги для устранения проблем управления обновлениями, [см.](troubleshoot/update-management.md)
