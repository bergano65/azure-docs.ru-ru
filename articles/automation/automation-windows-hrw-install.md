---
title: Гибридные рабочие роли Runbook службы автоматизации Azure для Windows
description: В этой статье содержатся сведения об установке гибридной рабочей роли Runbook в службе автоматизации Azure, которая позволяет запускать модули runbook на компьютерах под управлением Windows в локальном центре обработки данных или облачной среде.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 9f3e06f66996be4a2b43b64e6100c62a2fa41381
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649965"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Развертывание гибридной рабочей роли Runbook для Windows

Гибридную рабочую роль Runbook службы автоматизации Azure можно использовать для выполнения модулей runbook непосредственно на компьютере, размещающем роль, для работы с ресурсами в среде, что позволяет управлять этими локальными ресурсами. Для хранения модулей runbook и управления ими используется служба автоматизации Azure, затем они передаются на один или несколько целевых компьютеров. В этой статье описывается развертывание гибридной рабочей роли Runbook на компьютере Windows.

После успешного развертывания рабочей роли Runbook ознакомьтесь с [запуском модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md), чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Установка и настройка гибридной рабочей роли Runbook Windows

Чтобы установить и настроить гибридную рабочую роль Runbook Windows, можно использовать один из следующих методов.

* Для виртуальных машин Azure вы устанавливаете агент Log Analytics для Windows с помощью [расширения виртуальной машины для Windows](../virtual-machines/extensions/oms-windows.md). Расширение устанавливает агент Log Analytics на виртуальных машинах Azure и регистрирует виртуальные машины в существующей Log Analytics рабочей области с помощью шаблона Azure Resource Manager или PowerShell. После установки агента виртуальную машину можно добавить в группу гибридных рабочих ролей Runbook в учетной записи службы автоматизации, следуя **шагу 4** в разделе [Развертывание вручную](#manual-deployment) .

* Используйте модуль Runbook службы автоматизации для полной автоматизации процесса настройки компьютера Windows. Это рекомендуемый способ для компьютеров в вашем центре обработки данных или в другой облачной среде.

* Выполните пошаговую процедуру, чтобы вручную установить и настроить роль гибридной рабочей роли Runbook на виртуальной машине, отличной от Azure.

> [!NOTE]
> Чтобы управлять конфигурацией серверов, поддерживающих гибридную рабочую роль Runbook с настройкой требуемого состояния (DSC), добавьте такие серверы в качестве узлов DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Минимальные требования для гибридной рабочей роли Runbook Windows

Ниже приведены минимальные требования для гибридной рабочей роли Runbook Windows.

* Windows Server 2012 или более поздней версии;
* Windows PowerShell 5,1 или более поздней версии ([Загрузка WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 или более поздней версии.
* два ядра;
* 4 ГБ ОЗУ;
* Порт 443 (исходящий).

### <a name="network-configuration"></a>Конфигурация сети

Дополнительные требования к сети для гибридной рабочей роли Runbook см. в разделе [Настройка сети](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Серверная адаптация для управления с помощью Automation DSC

Сведения о подключении серверов для управления с помощью DSC см. в статье подключение [компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).

Если вы включаете [решение Управление обновлениями](../operations-management-suite/oms-solution-update-management.md), любой компьютер Windows, подключенный к рабочей области log Analytics, автоматически настраивается как гибридная Рабочая роль Runbook для поддержки модулей Runbook, включенных в это решение. Но он не регистрируется в группах гибридных рабочих ролей, которые уже определены в вашей учетной записи службы автоматизации.

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Добавление компьютера в группу гибридных рабочих ролей Runbook

Его можно добавить в группу гибридных рабочих ролей Runbook в учетной записи службы автоматизации, чтобы обеспечить поддержку модулей Runbook службы автоматизации при условии, что вы используете одну и ту же учетную запись для решения и для членства в группе гибридных рабочих ролей Runbook. Эта функция добавлена в версии 7.2.12024.0 гибридной рабочей роли Runbook.

## <a name="automated-deployment"></a>Автоматизированное развертывание

На целевом компьютере выполните следующие действия, чтобы автоматизировать установку и настройку роли гибридного рабочего процесса Windows.

### <a name="1-download-the-powershell-script"></a>1. Скачайте сценарий PowerShell

Скачайте сценарий New-OnPremiseHybridWorker.ps1 из [коллекции PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) непосредственно на компьютер, на котором выполняется гибридная рабочая роль Runbook, или на другой компьютер в вашей среде. Скопируйте этот сценарий в рабочую роль. Для выполнения сценария New-OnPremiseHybridWorker.ps1 необходимо задать следующие параметры.

* *AAResourceGroupName* (обязательный) — имя группы ресурсов, связанной с вашей учетной записью службы автоматизации.
* *OMSResourceGroupName* (необязательный) — имя группы ресурсов для рабочей области Log Analytics. Если эта группа ресурсов не указана, используется *AAResourceGroupName*.
* *SubscriptionID* (обязательный): идентификатор подписки Azure, в которой находится ваша учетная запись службы автоматизации.
* *TenantID* (необязательно): идентификатор организации клиента, связанной с учетной записью службы автоматизации.
* *WorkspaceName* (необязательный) — имя рабочей области Log Analytics. Если у вас нет рабочей области Log Analytics, сценарий создаст и настроит ее.
* *AutomationAccountName* (обязательно) — имя учетной записи службы автоматизации.
* *HybridGroupName* (обязательный) — имя группы гибридных рабочих ролей Runbook, которую вы указываете в качестве целевой для модулей runbook, поддерживающих этот сценарий.
* *Credential* (необязательно). учетные данные, используемые для входа в среду Azure.
  
> [!NOTE]
> При включении решений поддерживаются только определенные регионы для связывания рабочей области Log Analytics и учетной записи службы автоматизации. Список поддерживаемых пар сопоставлений см. в разделе [сопоставление регионов для учетной записи службы автоматизации и log Analytics рабочей области](how-to/region-mappings.md).

### <a name="2-open-windows-powershell-command-line-shell"></a>2. Откройте оболочку командной строки Windows PowerShell.

Откройте **Windows PowerShell** на **начальном** экране в режиме администратора.

### <a name="3-run-the-powershell-script"></a>3. Запустите сценарий PowerShell.

В оболочке командной строки PowerShell перейдите к папке, содержащей скачанный скрипт. Измените значения параметров *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* и *-WorkspaceName*. Затем выполните сценарий.

После выполнения сценария вы увидите запрос на аутентификацию в Azure. Необходимо войти с учетной записью, которая является членом роли администраторов Подписки и соадминистратором подписки.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. Установка NuGet

Вам будет предложено согласиться на установку NuGet и пройти проверку подлинности с помощью учетных данных Azure. Если у вас нет последней версии NuGet, ее можно получить из [доступных версий распространения NuGet](https://www.nuget.org/downloads).

### <a name="5-verify-the-deployment"></a>5. Проверка развертывания

После завершения выполнения сценария на странице **Группы гибридных рабочих ролей** отображается новая группа и количество элементов. Если указать имеющуюся группу, количество элементов в ней увеличивается. Вы можете выбрать группу из списка на странице " **группы гибридных рабочих ролей** " и выбрать плитку **гибридные рабочие роли** . На странице **Гибридные рабочие роли** отображается список элементов группы.

## <a name="manual-deployment"></a>Развертывание вручную

На целевом компьютере выполните первые два шага для среды автоматизации. Затем выполните оставшиеся действия для каждого рабочего компьютера.

### <a name="1-create-a-log-analytics-workspace"></a>1. Создание рабочей области Log Analytics

Если у вас еще нет рабочей области Log Analytics, сначала ознакомьтесь с [руководством по проектированию журналов Azure Monitor](../azure-monitor/platform/design-logs-deployment.md) перед созданием рабочей области.

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Добавление решения службы автоматизации в рабочую область Log Analytics

Решение автоматизации расширяет функциональные возможности службы автоматизации Azure, включая поддержку гибридных рабочих ролей Runbook. При добавлении решения в рабочую область Log Analytics автоматически отправляет рабочие компоненты на компьютер агента, который будет установлен на следующем шаге.

Чтобы добавить решение для **автоматизации** в рабочую область, выполните следующий командлет PowerShell.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Установка агента Log Analytics для Windows

Агент Log Analytics для Windows подключает компьютеры к рабочей области Log Analytics Azure Monitor. При установке агента на компьютере и подключении его к рабочей области автоматически загружаются компоненты, необходимые для работы гибридных рабочих ролей Runbook.

Чтобы установить агент на компьютере, следуйте инструкциям в статье [Подключение компьютеров Windows к Azure Monitor журналах](../log-analytics/log-analytics-windows-agent.md). Выполните эту процедуру на нескольких компьютерах, чтобы добавить в среду несколько компонентов Worker.

Когда агент успешно подключился к рабочей области Log Analytics через несколько минут, можно выполнить следующий запрос, чтобы убедиться, что он отправляет данные пульса в рабочую область:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

В результатах поиска должны отобразиться записи пульса для компьютера, указывающие, что он подключен и сообщает службе. По умолчанию каждый агент перенаправляет запись пульса в назначенную ему рабочую область. Вы можете убедиться, что агент правильно скачал решение службы автоматизации, если в папке C:\Program Files\Microsoft Monitoring Ажент\ажент. находится папка с именем каталоге. Чтобы подтвердить версию гибридной рабочей роли Runbook, перейдите к папке C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ и обратите внимание на вложенную папку \\*Version* .

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Установка среды Runbook и подключение к службе автоматизации Azure

При настройке агента для отправки отчетов в Log Analyticsную рабочую область решение службы автоматизации отправляет модуль PowerShell HybridRegistration, содержащий командлет **Add-HybridRunbookWorker** . Используйте этот командлет для установки среды Runbook на компьютере и его регистрации в службе автоматизации Azure.

Откройте сеанс PowerShell с правами администратора и импортируйте модуль, выполнив указанные ниже команды.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Затем выполните командлет **Add-HybridRunbookWorker** , используя следующий синтаксис.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Сведения, необходимые для этого командлета, можно получить на странице "Управление ключами" в портал Azure. Откройте эту страницу, выбрав параметр **Ключи** на странице **Параметры** для учетной записи службы автоматизации.

![Страница "Управление ключами"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* В качестве параметра *GroupName* используйте имя группы гибридных рабочих ролей Runbook. Если эта группа уже существует в учетной записи службы автоматизации, то к ней добавляется текущий компьютер. Если эта группа не существует, она добавляется.
* Для параметра *конечной точки* используйте запись **URL-адрес** на странице Управление ключами.
* В качестве параметра *токена* используйте запись **первичный ключ доступа** на странице Управление ключами.

Для получения подробных сведений об установке используйте параметр *-Verbose* с командлетом **Add-HybridRunbookWorker**.

### <a name="5-install-powershell-modules"></a>5. Установка модулей PowerShell

Модули runbook могут использовать любые из действий и командлетов, которые определены в модулях, установленных в вашей среде службы автоматизации Azure. Эти модули не развертываются на локальных компьютерах автоматически, поэтому их необходимо устанавливать вручную. Исключением является модуль Azure. Этот модуль устанавливается по умолчанию и предоставляет доступ к командлетам для всех служб и действий Azure для службы автоматизации Azure.

Поскольку основной целью гибридной рабочей роли Runbook является управление локальными ресурсами, скорее всего, потребуется установить модули, поддерживающие эти ресурсы, в частности модуль PowerShellGet. Сведения об установке модулей Windows PowerShell см. в разделе [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Установленные модули должны находиться в расположении, на которое ссылается переменная среды PSModulePath, чтобы Гибридная Рабочая роль могла автоматически импортировать их. Дополнительные сведения см. [в разделе Install modules in PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Следующие шаги

* Чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде, см. статью [Запуск модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).
* Инструкции по удалению гибридных рабочих ролей Runbook см. в разделе [Удаление гибридных рабочих ролей Runbook в службе автоматизации Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker) статьи "Автоматизация ресурсов в центре обработки данных или облаке с помощью гибридной рабочей роли Runbook".
* Дополнительные сведения об устранении неполадок с гибридными рабочими ролями Runbook см. в [этом разделе](troubleshoot/hybrid-runbook-worker.md#windows).
* Дополнительные действия по устранению проблем с решением "Управление обновлениями" см. в разделе [Устранение неполадок](troubleshoot/update-management.md) статьи "Решение для управления обновлениями в Azure".
