---
title: Устранение неполадок с гибридными рабочими ролями Runbook в службе автоматизации Azure
description: В этой статье приводятся сведения об устранении неполадок с гибридными рабочими ролями Runbook в службе автоматизации Azure
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e3726037e16acdf1d6d624dbf8c2088a57b0bde6
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234547"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Устранение неполадок с гибридными рабочими ролями Runbook

В этой статье приводятся сведения об устранении неполадок с гибридными рабочими ролями Runbook.

## <a name="general"></a>Общие сведения

Гибридная рабочая роль Runbook зависит от агента, который используется для взаимодействия с учетной записью автоматизации для регистрации рабочей роли, получения заданий runbook и сообщения о состоянии. В Windows используется агент Microsoft Monitoring Agent. В Linux — Агент Operations Management Suite для Linux.

### <a name="runbook-execution-fails"></a>Сценарий. Происходит сбой выполнения модуля Runbook

#### <a name="issue"></a>Проблема

Происходит сбой выполнения модуля Runbook со следующей ошибкой:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Модуль Runbook приостанавливается сразу после третьей попытки выполнения. Некоторые условия могут помешать модулю Runbook завершить работу. Если это произойдет, соответствующее сообщение об ошибке может не включать дополнительных сведений о причинах ошибки.

#### <a name="cause"></a>Причина:

Вот возможные причины:

* Модули Runbook не могут выполнить аутентификацию с помощью локальных ресурсов.

* Гибридная рабочая роль находится за прокси-сервером или брандмауэром.

* Модули Runbook не могут выполнить аутентификацию с помощью локальных ресурсов.

* Компьютер, предназначенный для выполнения гибридной рабочей роли Runbook, не соответствует минимальным требованиям к оборудованию.

#### <a name="resolution"></a>Способы устранения:

Проверьте, имеет ли компьютер исходящий доступ к *.azure-automation.net на порту 443.

Прежде чем назначать компьютеры, на которых будет выполняться гибридная рабочая роль Runbook, необходимо убедиться, что они соответствуют минимальным требования к оборудованию. Модули Runbook и фоновые процессы, которые они используют, могут привести к слишком интенсивному использованию системы, что вызовет задержки или увеличение времени ожидания при выполнении заданий Runbook.

Убедитесь, что компьютер, предназначенный для выполнения гибридной рабочей роли Runbook, соответствует минимальным требованиям к оборудованию. Если требования выполнены, отследите использование ЦП и памяти, чтобы определить корреляцию между производительностью процессов гибридной рабочей роли Runbook и Windows. Если память или ЦП используются слишком интенсивно, может потребоваться обновить ресурсы. Вы также можете выбрать другой вычислительный ресурс, который соответствует минимальным требованиям и масштабируется в соответствии с требованиями рабочей нагрузки.

Проверьте журнал событий **Microsoft-SMA** на наличие соответствующего события с описанием *Процесс Win32 завершен с кодом [4294967295]*. Эта ошибка возникла из-за того, что аутентификация в модулях Runbook еще не настроена или для группы гибридных рабочих ролей указаны учетные данные запуска от имени. Просмотрите [разрешения для модулей Runbook](../automation-hrw-run-runbooks.md#runbook-permissions), чтобы убедиться, что аутентификация для этих модулей настроена правильно.

### <a name="no-cert-found"></a>Сценарий. Не найден сертификат в хранилище сертификатов в гибридной рабочей роли Runbook

#### <a name="issue"></a>Проблема

Модуль Runbook, запущенный в гибридной рабочей роли Runbook, завершается сбоем со следующей ошибкой.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает при попытке использовать [учетную запись запуска от имени](../manage-runas-account.md) в модуле Runbook, который выполняется в гибридной рабочей роли Runbook, в которой отсутствует сертификат учетной записи запуска от имени. У гибридных рабочих ролей Runbook по умолчанию отсутствует локальный ресурс сертификата, который требуется для правильной работы учетной записи запуска от имени.

#### <a name="resolution"></a>Способы устранения:

Если гибридная рабочая роль Runbook представляет собой виртуальную машину Azure, вы можете использовать [управляемые удостоверения для ресурсов Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources). Этот сценарий позволяет проходить проверку подлинности для ресурсов Azure с помощью управляемого удостоверения виртуальной машины Azure вместо учетной записи запуска от имени, что упрощает проверку подлинности. Если гибридная рабочая роль Runbook установлена на локальном компьютере, необходимо установить сертификат учетной записи запуска от имени на этом компьютере. Чтобы узнать, как установить сертификат, ознакомьтесь с действиями по запуску модуля Runbook [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script).

## <a name="linux"></a>Linux

Гибридная рабочая роль Runbook Linux зависит от агента OMS для Linux, который используется для взаимодействия с учетной записью службы автоматизации с целью регистрации рабочей роли, получения заданий Runbook и передачи сведений о состоянии. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

### <a name="oms-agent-not-running"></a>Сценарий. Агент OMS для Linux не запущен.

#### <a name="issue"></a>Проблема

Агент OMS для Linux не запущен

#### <a name="cause"></a>Причина:

Если Агент OMS для Linux не запущен, гибридная рабочая роль Runbook Linux не сможет взаимодействовать со службой автоматизации Azure. Агент может не работать по разным причинам.

#### <a name="resolution"></a>Способы устранения:

 Проверьте, запущен ли агент, выполнив следующую команду: `ps -ef | grep python`. Вы увидите выходные данные, похожие на следующие; это процессы python, запущенные от имени учетной записи **nxautomation**. Если решения "Управление обновлениями" и "Служба автоматизации Azure" не включены, ни один из следующих процессов не будет запущен.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Ниже перечислены процессы, запущенные для гибридной рабочей роли Runbook Linux. Все они находятся в каталоге `/var/opt/microsoft/omsagent/state/automationworker/`.


* **oms.conf** — это значение представляет собой процесс диспетчера рабочих ролей. Он запускается непосредственно из DSC.

* **worker.conf** — это процесс автоматически зарегистрированной рабочей роли, запускаемый диспетчером рабочих ролей. Этот процесс используется в решении "Управление обновлениями" и незаметен для пользователя. Если решение "Управление обновлениями" не включено на компьютере, этот процесс будет отсутствовать.

* **diy/worker.conf** — это процесс гибридной рабочей роли DIY. Процесс гибридной рабочей роли DIY используется для выполнения модулей Runbook пользователя в гибридной рабочей роли Runbook. Он отличается от процесса автоматически зарегистрированной рабочей роли только тем, что использует другую конфигурацию. Если решение "Служба автоматизации Azure" отключено и гибридная рабочая роль DIY для Linux отсутствует, этот процесс будет отсутствовать.

Если агент OMS для Linux не запущен, выполните следующую команду, чтобы запустить службу: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Сценарий. Указанный класс не существует

При появлении ошибки о том, **что указанный класс не существует**, в `/var/opt/microsoft/omsconfig/omsconfig.log` необходимо обновить агент OMS для Linux. Выполните следующую команду, чтобы повторно установить агент OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a> Windows

Гибридная рабочая роль Runbook для Windows зависит от службы Microsoft Monitoring Agent, которая используется для взаимодействия с учетной записью службы автоматизации для регистрации рабочей роли, получения заданий Runbook и сообщения о состоянии. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

### <a name="mma-not-running"></a>Сценарий. Агент Microsoft Monitoring Agent не запущен

#### <a name="issue"></a>Проблема

Служба `healthservice` не работает на компьютере с гибридной рабочей ролью Runbook.

#### <a name="cause"></a>Причина:

Если служба Windows Microsoft Monitoring Agent не запущена, то гибридная рабочая роль Runbook не может взаимодействовать со службой автоматизации Azure.

#### <a name="resolution"></a>Способы устранения:

Проверьте, запущен ли агент, введя в PowerShell команду `Get-Service healthservice`. Если служба остановлена, введите в PowerShell команду `Start-Service healthservice`, чтобы запустить эту службу.

### <a name="event-4502"></a> Событие 4502 в журнале Operations Manager

#### <a name="issue"></a>Проблема

В журнале событий в папке **Application and Services Logs\Operations Manager** отображается событие 4502 и сообщение о событии с **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** со следующим описанием: *Сертификат, представленный службой \<wsid\>.oms.opinsights.azure.com, не был выдан центром сертификации, используемым для служб Майкрософт. Обратитесь к администратору сети, чтобы узнать, использует ли он прокси-сервер, перехватывающий связь по протоколам TLS и SSL. Дополнительные сведения об устранении неполадок, связанных с подключением, содержатся в статье базы знаний KB3126513.*

#### <a name="cause"></a>Причина:

Эта проблема может быть вызвана тем, что прокси-сервер или сетевой брандмауэр блокируют подключение к Microsoft Azure. Проверьте, имеет ли компьютер исходящий доступ к *.azure-automation.net на порту 443.

#### <a name="resolution"></a>Способы устранения:

Журналы сохраняются локально в каждом гибридном компоненте Worker по адресу C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Можно проверить, зарегистрированы ли в журналах событий в папках **Application and Services Logs\Microsoft-SMA\Operations** и **Application and Services Logs\Operations Manager** какие-либо предупреждения или ошибки, которые указывают на проблемы подключения или другие проблемы, связанные с адаптацией роли службы автоматизации Azure, либо на проблемы во время выполнения обычных операций.

[Выходные данные Runbook и сообщения](../automation-runbook-output-and-messages.md) отправляются в службу автоматизации Azure из гибридных рабочих ролей точно так же, как задания Runbook, которые выполняются в облаке. Потоки Verbose и Progress можно активировать точно так же, как и для других модулей Runbook.

### <a name="corrupt-cache"></a>Гибридная рабочая роль Runbook не создает отчеты

#### <a name="issue"></a>Проблема

Компьютер гибридной рабочей роли Runbook работает, но в рабочей области не отображаются данные о пульсе для компьютера.

В следующем примере запроса показаны компьютеры в рабочей области и их последние сведения о пульсе:

```loganalytics
// Last heartbeat of each computer
Heartbeat 
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Причина:

Эта проблема может быть вызвана повреждением кэша в гибридной рабочей роли Runbook.

#### <a name="resolution"></a>Способы устранения:

Чтобы решить данную проблему, войдите в систему гибридной рабочей роли Runbook и запустите следующий сценарий. Он останавливает Microsoft Monitoring Agent, удаляет его кэш и перезапускает службу. Это действие заставляет гибридную рабочую роль Runbook повторно скачать свою конфигурацию из службы автоматизации Azure.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Сценарий. Не удается добавить гибридную рабочую роль Runbook

#### <a name="issue"></a>Проблема

Вы получите следующее сообщение при попытке добавить гибридную рабочую роль Runbook с помощью командлета `Add-HybridRunbookWorker`.

```
Machine is already registered to a different account
```

#### <a name="cause"></a>Причина:

Это может возникать, если компьютер уже зарегистрирован в другой учетной записи службы автоматизации или при попытке повторного добавления гибридной рабочей роли Runbook после ее удаления с компьютера.

#### <a name="resolution"></a>Способы устранения:

Чтобы устранить эту проблему, удалите следующий раздел реестра и повторите командлет `Add-HybridRunbookWorker` еще раз.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Дополнительная информация

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.

