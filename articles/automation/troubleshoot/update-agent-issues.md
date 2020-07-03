---
title: Устранение неполадок агента Центра обновления Windows в службе автоматизации Azure Управление обновлениями
description: Узнайте, как устранять неполадки и устранять проблемы с агентом обновления Windows с помощью решения Управление обновлениями.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e9af9c6472f49ebccd36e8d73688636c98918ff1
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996440"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Устранение проблем с агентом обновления Windows

Может быть несколько причин, по которым компьютер не отображается как готовый (работоспособный) в Управление обновлениями. Вы можете проверить работоспособность агента гибридной рабочей роли Runbook Windows, чтобы определить базовую проблему. Ниже приведены три состояния готовности для компьютера.

* Готово. Гибридная Рабочая роль Runbook развертывается и последний раз наблюдался менее одного часа назад.
* Отключено. Гибридная Рабочая роль Runbook развертывается и последний раз наблюдался за один час назад.
* Не настроено: Гибридная Рабочая роль Runbook не найдена или не завершила подключение.

> [!NOTE]
> Между отображением портал Azure и текущим состоянием компьютера может быть небольшая задержка.

В этой статье описывается, как запустить средство устранения неполадок для виртуальных машин Azure из портал Azure и компьютеров, не относящихся к Azure, в [автономном сценарии](#troubleshoot-offline). 

> [!NOTE]
> Сценарий устранения неполадок теперь включает проверки для Windows Server Update Services (WSUS), а также для ключей автоскачивания и установки. 

## <a name="start-the-troubleshooter"></a>Запуск средства устранения неполадок

Для компьютеров Azure можно запустить страницу Устранение неполадок агента обновления, щелкнув ссылку **Устранение неполадок** в столбце **готовность агента обновления** на портале. Для компьютеров, не относящихся к Azure, ссылка приводится к этой статье. См. [инструкции в автономном режиме](#troubleshoot-offline) для устранения неполадок на компьютере, отличном от Azure.

![Снимок экрана Управление обновлениями списка виртуальных машин](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Чтобы проверить работоспособность гибридной рабочей роли Runbook, необходимо, чтобы виртуальная машина была запущена. Если виртуальная машина не запущена, появится кнопка **запуска виртуальной машины**.

На странице Troubleshoot Update Agent (Устранение неполадок с агентом обновления) выберите **Run checks** (Выполнить проверки), чтобы запустить средство устранения неполадок. Средство устранения неполадок использует [команду Run](../../virtual-machines/windows/run-command.md) для выполнения сценария на компьютере, чтобы проверить зависимости. После того как средство устранения неполадок завершило свою работу, оно возвращает результаты проверок.

![Снимок экрана со страницей "Устранение неполадок агента обновления"](../media/update-agent-issues/troubleshoot-page.png)

Результаты отобразятся на странице, когда будут готовы. В разделах о проверках отображается то, что входит в состав каждой проверки.

![Снимок экрана: Проверка проверки агента обновления](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Проверки готовности

### <a name="operating-system"></a>Операционная система

Проверка операционной системы проверяет, работает ли Гибридная Рабочая роль Runbook с одной из операционных систем, показанных в следующей таблице.

|Операционная система  |Примечания  |
|---------|---------|
|Windows Server 2012 и более поздние версии |Требуется .NET Framework 4,6 или более поздней версии. ([Скачайте .NET Framework](/dotnet/framework/install/guide-for-developers).)<br/> Требуется Windows PowerShell 5,1.  ([Загрузите Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).)        |

### <a name="net-462"></a>.NET 4.6.2

Проверка .NET Framework проверяет, установлена ли в системе [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) или более поздней версии.

### <a name="wmf-51"></a>WMF 5.1

Проверка WMF проверяет, что в системе установлена требуемая версия Windows Management Framework (WMF), которая представляет собой [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Эта проверка определяет, используете ли вы протокол TLS 1.2 для шифрования связи. Протокол TLS 1.0 больше не поддерживается платформой. Используйте TLS 1,2 для взаимодействия с Управление обновлениями.

## <a name="connectivity-checks"></a>Проверка подключения

### <a name="registration-endpoint"></a>Конечная точка регистрации

Эта проверка определяет, может ли агент правильно взаимодействовать со службой агента.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать с конечной точкой регистрации. Список адресов и портов для открытия см. в разделе [сетевое планирование](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Конечная точка операций

Эта проверка определяет, может ли агент правильно взаимодействовать со службой Job Runtime Data.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать со службой Job Runtime Data. Список адресов и портов для открытия см. в разделе [сетевое планирование](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Проверки работоспособности службы виртуальных машин

### <a name="monitoring-agent-service-status"></a>Отслеживание состояния службы агента

Эта проверка определяет, запущена ли на компьютере агент Log Analytics`healthservice`для Windows (). Дополнительные сведения об устранении неполадок службы см. [в статье агент log Analytics для Windows не работает](hybrid-runbook-worker.md#mma-not-running).

Сведения о переустановке агента Log Analytics для Windows см. в разделе [Установка агента для Windows](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Мониторинг событий службы агента

Эта проверка определяет, появляются ли какие либо события 4502 в журнале Operations Manager Azure на компьютере за последние 24 часа.

Дополнительные сведения об этом событии см. в описании [события 4502 в журнале Operations Manager](hybrid-runbook-worker.md#event-4502) для этого события.

## <a name="access-permissions-checks"></a>Проверка разрешений доступа

> [!NOTE]
> В настоящее время средство устранения неполадок не направляет трафик через прокси-сервер, если он настроен.

### <a name="crypto-folder-access"></a>Шифрование доступа к папке

Проверка доступа к папке с шифрованием определяет, имеет ли локальная системная учетная запись доступ к К:\програмдата\микрософт\крипто\рса..

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Автономное устранение неполадок

Вы можете использовать средство устранения неполадок в гибридной рабочей роли Runbook в автономном режиме, запустив сценарий локально. Получите следующий скрипт из коллекция PowerShell: [reвиндовсупдатеажентрегистратион](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration). Чтобы запустить сценарий, необходимо установить WMF 4,0 или более поздней версии. Чтобы скачать последнюю версию PowerShell, см. статью [Установка различных версий PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

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

## <a name="next-steps"></a>Дальнейшие действия

[Устранение неполадок с гибридными рабочими ролями Runbook](hybrid-runbook-worker.md)
