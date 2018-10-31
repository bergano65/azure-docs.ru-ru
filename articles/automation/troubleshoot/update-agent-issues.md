---
title: Общие сведения о результатах проверки агента в службе "Управление обновлениями Azure"
description: Подробные сведения об устранении неполадок c помощью агента службы "Управление обновлениями".
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/10/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 1b258d115e7d9962ecab4b93dbcd98d13f5977c7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956686"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>Общие сведения о результатах проверки агента в службе "Управление обновлениями"

Есть множество причин, по которым для компьютера, не связанного с Azure, не отображается состояние **Готово** в службе "Управление обновлениями". В службе "Управление обновлениями" можно проверить работоспособность агента гибридной рабочей роли, чтобы определить первоначальную проблему. В этой статье описывается, как запустить средство устранения неполадок на портале Azure и в автономных сценариях.

## <a name="start-the-troubleshooter"></a>Запуск средства устранения неполадок

Если щелкнуть ссылку **Устранение неполадок** в столбце **Update Agent Readiness** (Готовность агента обновления) на портале, открывается страница **Troubleshoot Update Agent** (Устранение неполадок с агентом обновления). Чтобы помочь вам в устранении неполадок, на этой странице отображаются проблемы с агентом и ссылка на данную статью.

![Страница со списком виртуальных машин](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Проверка выполняется с запущенной виртуальной машиной. Если виртуальная машина не запущена, появится кнопка **Start the VM** (Запустить виртуальную машину).

На странице **Troubleshoot Update Agent** (Устранение неполадок с агентом обновления) выберите **Run Checks** (Выполнить проверки), чтобы запустить средство устранения неполадок. Средство устранения неполадок использует [команду "Выполнить"](../../virtual-machines/windows/run-command.md) для запуска на компьютере скрипта, проверяющего зависимости агента. После того как средство устранения неполадок завершило свою работу, оно возвращает результаты проверок.

![Страница "Устранение неполадок"](../media/update-agent-issues/troubleshoot-page.png)

По завершении проверок в окне появляются результаты. [Разделы о проверках](#pre-requisistes-checks) предоставляют сведения о предназначении каждой проверки.

![Страница проверки агента обновлений](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Проверка предварительных требований

### <a name="operating-system"></a>Операционная система

Проверка операционной системы обнаруживает, работает ли в гибридной рабочей роли Runbook одна из следующих операционных систем:

|Операционная система  |Примечания  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Поддерживает только оценки обновлений.         |
|Windows Server 2008 R2 с пакетом обновления 1 и более поздней версии     |Требуется .NET Framework 4.5 или более поздней версии. ([Скачать .NET Framework](/dotnet/framework/install/guide-for-developers).)<br/> Windows PowerShell, начиная с версии 4.0. ([Скачать WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855).)<br/> Для повышения надежности рекомендуем использовать Windows PowerShell 5.1.  ([Скачать WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616).)        |
|CentOS 6 (x86 или x64) и 7 (x64).      | У агентов Linux должен быть доступ к репозиторию обновлений. Для исправления на основе классификации требуется, чтобы программа yum вернула данные безопасности, которых в CentOS нет без дополнительной настройки.         |
|Red Hat Enterprise 6 (x86 или x64) и 7 (x64).     | У агентов Linux должен быть доступ к репозиторию обновлений.        |
|SUSE Linux Enterprise Server 11 (x86 или x64) и 12 (x64)     | У агентов Linux должен быть доступ к репозиторию обновлений.        |
|Ubuntu 14.04 LTS и 16.04 LTS (x86/x64)      |У агентов Linux должен быть доступ к репозиторию обновлений.         |

### <a name="net-45"></a>.NET 4.5

Проверка .NET Framework определяет, есть ли в системе как минимум версия [.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653).

### <a name="wmf-51"></a>WMF 5.1

Проверка WMF определяет, есть ли в системе требуемая версия платформы Windows Management Framework. [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) является самой ранней поддерживаемой версией. Рекомендуется установить [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) для обеспечения повышенной надежности гибридной рабочей роли Runbook.

### <a name="tls-12"></a>TLS 1.2

Эта проверка определяет, используете ли вы протокол TLS 1.2 для шифрования связи. Протокол TLS 1.0 больше не поддерживается платформой. Рекомендуется, чтобы клиенты использовали TLS 1.2 для обмена данными со службой "Управление обновлениями".

## <a name="connectivity-checks"></a>Проверка подключения

### <a name="registration-endpoint"></a>Конечная точка регистрации

Эта проверка определяет, может ли агент правильно взаимодействовать со службой агента.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать с конечной точкой регистрации. Список адресов и портов, которые необходимо открыть, см. в разделе [Гибридная рабочая роль](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Конечная точка операций

Эта проверка определяет, может ли агент правильно взаимодействовать со службой Job Runtime Data.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать со службой Job Runtime Data. Список адресов и портов, которые необходимо открыть, см. в разделе [Гибридная рабочая роль](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Проверки работоспособности службы виртуальных машин

### <a name="monitoring-agent-service-status"></a>Отслеживание состояния службы агента

Эта проверка определяет, выполняется ли на компьютере служба работоспособности (`HealthService`) для агента Microsoft Monitoring Agent.

Дополнительные сведения об устранении неполадок со службой см. в разделе [Сценарий: Microsoft Monitoring Agent не работает](hybrid-runbook-worker.md#mma-not-running).

Чтобы переустановить Microsoft Monitoring Agent, ознакомьтесь с [этой статьей](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent).

### <a name="monitoring-agent-service-events"></a>Мониторинг событий службы агента

Эта проверка определяет, записывались ли какие-либо события `4502` в журнал Operations Manager на компьютере за последние 24 часа.

Дополнительные сведения об этом событии см. в разделе [Событие 4502 в журнале Operations Manager](hybrid-runbook-worker.md#event-4502).

## <a name="access-permissions-checks"></a>Проверка разрешений доступа

### <a name="machinekeys-folder-access"></a>Доступ к папке MachineKeys

Проверка доступа к зашифрованной папке определяет, есть ли у локальной системной учетной записи доступ к `C:\ProgramData\Microsoft\Crypto\RSA`.

## <a name="troubleshoot-offline"></a>Автономное устранение неполадок

Средство устранения неполадок можно использовать в гибридной рабочей роли в автономном режиме, запустив сценарий локально. Скрипт [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) можно найти в коллекции PowerShell. Ниже показан пример выходных данных этого сценария:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .Net Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             : 
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы устранить другие неполадки, ознакомьтесь со статьей [Устранение неполадок с гибридными рабочими ролями Runbook](hybrid-runbook-worker.md).
