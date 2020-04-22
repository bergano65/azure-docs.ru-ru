---
title: Устранение проблем с обновлением агента Windows проблемы в Azure Автоматизация управления обновлением
description: Узнайте, как устранить неполадки и устранить проблемы с агентом обновления Windows с помощью решения Update Management.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678971"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Проблемы устранения проблем с обновлением Windows неусисхаем

Там может быть много причин, почему ваша машина не отображается как готовый (здоровый) в управлении обновлением. В области управления обновлениями можно проверить работоспособность гибридного агента Runbook Worker, чтобы определить основную проблему. В этой статье обсуждается, как запустить устранение неполадок для машин Azure с портала Azure и не-Azure машин в [автономном сценарии.](#troubleshoot-offline)

Ниже приведены три состояния готовности к машине:

* Готов - Гибридный Runbook Работник развернут и в последний раз видели менее 1 часа назад.
* Отключен - Гибридный Runbook Работник развернут и в последний раз видели более 1 часа назад.
* Не настроен - Гибридный Runbook Worker не найден или не закончил на борту.

> [!NOTE]
> Между тем, что показывает портал Azure, и текущим состоянием машины может быть небольшая задержка.

## <a name="start-the-troubleshooter"></a>Запуск средства устранения неполадок

Для компьютеров Azure: на портале щелкните ссылку **Устранение неполадок** в столбце **Update Agent Readiness** (Готовность агента обновления), после чего откроется страница Troubleshoot Update Agent (Устранение неполадок с агентом обновления). Для машин, не относясь от Azure, ссылка приводит вас к этой статье. Смотрите [инструкции в автономном режиме](#troubleshoot-offline) для устранения неполадок в машине, не являйтесь Azure.

![Список виртуальных машин в службе "Управление обновлениями"](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Чтобы проверить работоспособность hybrid Runbook Worker, VM должен работать. Если виртуальная машина не запущена, появится кнопка **запуска виртуальной машины**.

На странице Troubleshoot Update Agent (Устранение неполадок с агентом обновления) выберите **Run checks** (Выполнить проверки), чтобы запустить средство устранения неполадок. Устранение неполадок использует [Команду Run](../../virtual-machines/windows/run-command.md) для выполнения скрипта на машине для проверки зависимостей. После того как средство устранения неполадок завершило свою работу, оно возвращает результаты проверок.

![Страница устранения неполадок с агентом обновления](../media/update-agent-issues/troubleshoot-page.png)

Результаты отобразятся на странице, когда будут готовы. В разделах о проверках отображается то, что входит в состав каждой проверки.

![Проверки для устранения неполадок с агентом обновления](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Проверка предварительных требований

### <a name="operating-system"></a>Операционная система

Проверка операционной системы проверяет, работает ли Hybrid Runbook Worker одной из следующих операционных систем:

|Операционная система  |Примечания  |
|---------|---------|
|Windows Server 2012 и более поздние |.NET Framework 4.6 или позже требуется. ([Скачать .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> Требуется Windows PowerShell 5.1.  ([Скачать Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

Проверка рамочного соглашения .NET проверяет, что система имеет минимум [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) установлен.

### <a name="wmf-51"></a>WMF 5.1

Проверка WMF проверяет, что система имеет необходимую версию рамочной системы управления Windows (WMF) - [Рамочная система управления Windows 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Эта проверка определяет, используете ли вы протокол TLS 1.2 для шифрования связи. Протокол TLS 1.0 больше не поддерживается платформой. Мы рекомендуем, чтобы клиенты использовали протокол TLS 1.2 для обмена данными со службой "Управление обновлениями".

## <a name="connectivity-checks"></a>Проверка подключения

### <a name="registration-endpoint"></a>Конечная точка регистрации

Эта проверка определяет, может ли агент правильно взаимодействовать со службой агента.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать с конечной точкой регистрации. Чтобы открыть список адресов и [Network planning for Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning)портов, см.

### <a name="operations-endpoint"></a>Конечная точка операций

Эта проверка определяет, может ли агент правильно взаимодействовать со службой Job Runtime Data.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать со службой Job Runtime Data. Чтобы открыть список адресов и [Network planning for Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning)портов, см.

## <a name="vm-service-health-checks"></a>Проверки работоспособности службы виртуальных машин

### <a name="monitoring-agent-service-status"></a>Отслеживание состояния службы агента

Эта проверка определяет, работает ли на`healthservice`компьютере агент Log Analytics для Windows () Чтобы узнать больше о устранении неполадок службы, [см.](hybrid-runbook-worker.md#mma-not-running)

Чтобы переустановить агент Log Analytics для Windows, [см.](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)

### <a name="monitoring-agent-service-events"></a>Мониторинг событий службы агента

Эта проверка определяет, отображаются ли какие-либо события 4502 в журнале Управления операций Azure на машине за последние 24 часа.

Дополнительные сведения об этом событии см. в разделе [Событие 4502 в журнале Operations Manager](hybrid-runbook-worker.md#event-4502).

## <a name="access-permissions-checks"></a>Проверка разрешений доступа

### <a name="machinekeys-folder-access"></a>Доступ к папке MachineKeys

Проверка доступа к папке шифрования определяет, имеет ли учетная запись локальной системы доступ к папке C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Автономное устранение неполадок

Средство устранения неполадок можно использовать в гибридной рабочей роли в автономном режиме, запустив скрипт локально. Скрипт [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) можно найти в коллекции PowerShell. Для запуска скрипта необходимо установить WMF 4.0 или больше. Чтобы скачать последнюю версию PowerShell, [см.](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)

Выходные данные этого скрипта выглядят так:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
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
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

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

## <a name="next-steps"></a>Следующие шаги

Чтобы устранить другие неполадки, ознакомьтесь со статьей [Устранение неполадок с гибридными рабочими ролями Runbook](hybrid-runbook-worker.md).
